---
title: Escenarios y ejemplos de gobierno de suscripciones | Microsoft Docs
description: "Proporciona ejemplos de cómo implementar un sistema de gobierno de suscripciones de Azure para escenarios comunes."
services: azure-resource-manager
documentationcenter: na
author: rdendtler
manager: timlt
editor: tysonn
ms.assetid: e8fbeeb8-d7a1-48af-804f-6fe1a6024bcb
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: rodend;karlku;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4f541e34e7c0696e4074613c4ab0734a096c6d12
ms.openlocfilehash: ccbf4b3819051f4520205ce6ebfb66bc39a9dd3b


---
# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Ejemplos de implementación de plantillas scaffold empresariales de Azure
En este tema se ofrecen ejemplos de cómo una empresa puede implementar las recomendaciones de una [plantilla scaffold empresarial de Azure](resource-manager-subscription-governance.md). Se usa una compañía ficticia denominada "Contoso" con el objetivo de ilustrar las prácticas recomendadas para escenarios comunes.

## <a name="background"></a>Fondo
Contoso es una empresa con presencia internacional que proporciona soluciones de la cadena de suministro para clientes de, por ejemplo, modelos de software como servicio y modelos empaquetados implementados en un entorno local.  Desarrollan software en todo el mundo con centros de desarrollo importantes en India, Estados Unidos y Canadá.

La parte de ISV de la compañía está dividida en varias unidades de negocio independientes que administran productos con una importante actividad empresarial. Cada unidad de negocio tiene sus propios arquitectos, directores de producto y desarrolladores.

La unidad de negocio de servicios de tecnología empresariales (ETS) proporciona las funcionalidades de TI centralizadas y administra varios centros de datos donde las unidades de negocio hospedan sus aplicaciones. Además de la administración de los centros de datos, la organización ETS proporciona y administra la colaboración centralizada (por ejemplo, el correo electrónico y los sitios web) y los servicios de red y telefonía. También se encargan de las cargas de trabajo orientadas al cliente de las unidades de negocio más pequeñas que no disponen de personal de operaciones.

En este tema se usan los siguientes nombres de persona:

* David, administrador de Azure en ETS
* Alicia, directora de desarrollo de Contoso en la unidad de negocio de la cadena de suministro

Contoso necesita compilar una aplicación de línea de negocio y una aplicación orientada al cliente. Ha decidido ejecutar las aplicaciones en Azure. David lee el tema sobre [gobierno de suscripción preceptivo](resource-manager-subscription-governance.md) y ahora está listo para implementar las recomendaciones.

## <a name="scenario-1-line-of-business-application"></a>Escenario 1: aplicación de línea de negocio
Contoso está creando un sistema de administración de código fuente (BitBucket) para que puedan utilizarlo desarrolladores de todo el mundo.  La aplicación utiliza la infraestructura como servicio (IaaS) para el hospedaje y consta de servidores web y un servidor de bases de datos. Los desarrolladores acceden a los servidores en sus entornos de desarrollo, pero no necesitan entrar en los servidores de Azure. La unidad ETS de Contoso quiere permitir que el equipo y el propietario de la aplicación puedan administrar la aplicación. Esta solo está disponible mientras lo esté la red corporativa de Contoso. David debe configurar la suscripción para esta aplicación. La suscripción también hospedará más adelante otro software relacionado con el desarrollador.  

### <a name="naming-standards--resource-groups"></a>Estándares de nomenclatura y grupos de recursos
David crea una suscripción para admitir las herramientas de desarrollo que son comunes a todas las unidades de negocio. Debe crear nombres descriptivos para la suscripción y los grupos de recursos (de la aplicación y las redes). Por tanto, genera la siguiente suscripción y estos grupos de recursos:

| Elemento | Nombre | Descripción |
| --- | --- | --- |
| La suscripción |Contoso ETS DeveloperTools Production |Admite las herramientas de desarrollo comunes. |
| Grupo de recursos |rgBitBucket |Contiene el servidor de aplicaciones web y el de bases de datos. |
| Grupo de recursos |rgCoreNetworks |Contiene las redes virtuales y la conexión de puerta de enlace de sitio a sitio. |

### <a name="role-based-access-control"></a>Control de acceso basado en rol
Después de crear la suscripción, David quiere asegurarse de que los equipos y propietarios de aplicaciones adecuados pueden acceder a sus recursos. David identifica que cada equipo tiene requisitos diferentes. Por tanto, utiliza los grupos que se han sincronizado del servicio local Active Directory (AD) local de Contoso en Azure Active Directory y proporciona el nivel adecuado de acceso a los equipos.

David asigna los siguientes roles a la suscripción:

| Rol | Asignado a | Descripción |
| --- | --- | --- |
| [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) |Identificador administrado del servicio AD de Contoso |Este identificador se controla con acceso Just-In-Time (JIT) a través de la herramienta de administración de identidades de Contoso. Asimismo, garantiza que se audite por completo el acceso del propietario de la suscripción. |
| [Administrador de seguridad](../active-directory/role-based-access-built-in-roles.md#security-manager) |Departamento de administración de riesgos y seguridad |Este rol permite a los usuarios ver Azure Security Center y el estado de los recursos. |
| [Colaborador de la red](../active-directory/role-based-access-built-in-roles.md#network-contributor) |Equipo de red |Este rol permite al equipo de red de Contoso administrar la VPN de sitio a sitio y las redes virtuales. |
| *Rol personalizado* |Propietario de la aplicación |David crea un rol que concede la capacidad de modificar recursos en el grupo de recursos. Para obtener más información, vea el artículo [Roles personalizados en RBAC de Azure](../active-directory/role-based-access-control-custom-roles.md). |

### <a name="policies"></a>Directivas
David tiene los siguientes requisitos para administrar recursos de la suscripción:

* Dado que las herramientas de desarrollo pueden usarla desarrolladores de todo el mundo, no quiere impedir que los usuarios creen recursos en cualquier región. Sin embargo, debe saber dónde se crean los recursos.
* Además, le preocupan los costos. Por lo tanto, quiere impedir que los propietarios de la aplicación creen máquinas virtuales innecesariamente costosas.  
* Como esta aplicación la utilizan desarrolladores de muchas unidades de negocio, quiere etiquetar cada recurso con el propietario de la aplicación y la unidad de negocio. Mediante el uso de estas etiquetas, la unidad ETS puede emitir las facturas a los equipos adecuados.

Para ello, crea las siguientes [directivas de Resource Manager](resource-manager-policy.md):

| Campo | Efecto | Descripción |
| --- | --- | --- |
| location |audit |Audita la creación de los recursos en cualquier región. |
| type |deny |Deniega la creación de máquinas virtuales de serie G. |
| etiquetas |deny |Exige la etiqueta de propietario de la aplicación. |
| etiquetas |deny |Exige la etiqueta de centro de costos. |
| etiquetas |append |Anexa el nombre de etiqueta **BusinessUnit** y el valor de etiqueta **ETS** a todos los recursos. |

### <a name="resource-tags"></a>Etiquetas del recurso
David es consciente de que debe tener información específica sobre facturación para identificar el centro de costos para la implementación de BitBucket. Además, quiere conocer todos los recursos que posee ETS.

Para ello, agrega las siguientes [etiquetas](resource-group-using-tags.md) a los grupos de recursos y recursos.

| Nombre de etiqueta | Valor de etiqueta |
| --- | --- |
| ApplicationOwner |El nombre de la persona que administra esta aplicación. |
| CostCenter |El centro de costos del grupo que paga el consumo de Azure. |
| BusinessUnit |**ETS** (la unidad de negocio asociada a la suscripción). |

### <a name="core-network"></a>Red principal
El equipo de administración de riesgos y de seguridad de información de la unidad ETS de Contoso revisa el plan que ha propuesto David para migrar la aplicación a Azure. Quieren asegurarse de que no pueda accederse a la aplicación a través de Internet.  David también tiene aplicaciones de desarrollador que, más adelante, se migrarán a Azure. Estas aplicaciones requieren interfaces públicas.  Para cumplir estos requisitos, proporciona redes virtuales externas e internas, y un grupo de seguridad de red para restringir el acceso.

Además, crea estos recursos:

| Tipo de recurso | Nombre | Descripción |
| --- | --- | --- |
| Red virtual |vnInternal |Se utiliza con la aplicación de BitBucket y se conecta a través de ExpressRoute a la red corporativa de Contoso.  Una subred (sbBitBucket) proporciona la aplicación con un espacio de direcciones IP específico. |
| Red virtual |vnExternal |Está disponible para las aplicaciones futuras que requieran puntos de conexión orientados al público. |
| Grupo de seguridad de red (NSG) |nsgBitBucket |Garantiza que se reduzca al máximo la superficie de ataque permitiendo conexiones solo en el puerto 443 de la subred donde se encuentra la aplicación (sbBitBucket). |

### <a name="resource-locks"></a>Bloqueos de recursos
David identifica que la conectividad de la red corporativa de Contoso a la red virtual interna debe protegerse de cualquier script "rebelde" o eliminación accidental.

Para ello, crea los siguientes [bloqueos de recursos](resource-group-lock-resources.md):

| Tipo de bloqueo | Recurso | Descripción |
| --- | --- | --- |
| **CanNotDelete** |vnInternal |Impide que los usuarios eliminen la red virtual o las subredes, pero no que se agreguen nuevas subredes. |

### <a name="azure-automation"></a>Automatización de Azure
David no tiene que automatizar nada en esta aplicación. Aunque creó una cuenta de Azure Automation, por ahora, no la utilizará.

### <a name="azure-security-center"></a>Azure Security Center
El equipo de administración de servicios de TI de Contoso debe identificar y controlar rápidamente las amenazas. También quiere entender los problemas que existan.  

Para cumplir estos requisitos, David habilita [Azure Security Center](../security-center/security-center-intro.md) y proporciona acceso al rol de administrador de seguridad.

## <a name="scenario-2-customer-facing-app"></a>Escenario 2: aplicación orientada al cliente
Los responsables de la unidad de negocio de la cadena de suministro han identificado diversas oportunidades para aumentar la interacción con los clientes de Contoso mediante el uso de una tarjeta de fidelización. El equipo de Alicia debe crear esta aplicación y decide que Azure aumenta su capacidad de satisfacer las necesidades del negocio. Alicia trabaja con David de la unidad ETS para configurar dos suscripciones con el objetivo de desarrollar y usar esta aplicación.

### <a name="azure-subscriptions"></a>Suscripciones de Azure
David inicia sesión en Azure Enterprise Portal y se percata de que el departamento de la cadena de suministro ya existe.  Sin embargo, como este proyecto es el primero de desarrollo del equipo de la cadena de suministro en Azure, David identifica la necesidad de que el equipo de desarrollo de Alicia tenga una nueva cuenta.  Por tanto, crea la cuenta I+D para su equipo y concede acceso a Alicia. Alicia se conecta mediante el portal de cuentas y crea dos suscripciones: una para almacenar los servidores de desarrollo y otra donde se hospedarán los servidores de producción.  Además, sigue las normas de nomenclaturas establecidas previamente al crear las siguientes suscripciones:

| Uso de la suscripción | Nombre |
| --- | --- |
| Desarrollo |SupplyChain ResearchDevelopment LoyaltyCard Development |
| Producción |SupplyChain Operations LoyaltyCard Production |

### <a name="policies"></a>Directivas
David y Alicia analizan la aplicación e identifican que solo la utilizan clientes de la región de Norteamérica.  Alicia y su equipo planean utilizar Azure SQL y el entorno de servicios de aplicaciones de Azure para crear la aplicación. Deben crear máquinas virtuales durante el desarrollo.  Alicia quiere asegurarse de que sus desarrolladores dispongan de los recursos que necesitan para explorar y examinar problemas sin perjudicar a la unidad ETS.

Para la **suscripción de desarrollo**, crean la siguiente directiva:

| Campo | Efecto | Descripción |
| --- | --- | --- |
| location |audit |Audita la creación de los recursos en cualquier región. |

No limitan el tipo de SKU que puede crear un usuario durante el desarrollo y no exigen etiquetas para ningún recurso o grupo de recursos.

Para la **suscripción de producción**, crean las siguientes directivas:

| Campo | Efecto | Descripción |
| --- | --- | --- |
| location |deny |Deniega la creación de cualquier recurso fuera de los centros de datos de Estados Unidos. |
| etiquetas |deny |Exige la etiqueta de propietario de la aplicación. |
| etiquetas |deny |Exige la etiqueta de departamento. |
| etiquetas |append |Anexa una etiqueta a cada grupo de recursos que indica el entorno de producción. |

No limitan el tipo de SKU que puede crear un usuario durante la producción.

### <a name="resource-tags"></a>Etiquetas del recurso
David es consciente de que debe tener información específica con el objetivo de identificar los grupos de negocios adecuados para las tareas de facturación y propiedad. Por ello, define las etiquetas de recurso para los grupos de recursos y recursos.

| Nombre de etiqueta | Valor de etiqueta |
| --- | --- |
| ApplicationOwner |El nombre de la persona que administra esta aplicación. |
| Departamento |El centro de costos del grupo que paga el consumo de Azure. |
| EnvironmentType |**Production** (aunque el nombre de la suscripción incluya **Production**, esta etiqueta permite facilitar su identificación al buscar recursos en el portal o en la factura). |

### <a name="core-networks"></a>Redes principales
El equipo de administración de riesgos y de seguridad de información de la unidad ETS de Contoso revisa el plan que ha propuesto David para migrar la aplicación a Azure. Quiere asegurarse de que la aplicación de tarjeta de fidelización está aislada y protegida en una red DMZ correctamente.  Para cumplir este requisito, Alicia y David crean una red virtual externa y un grupo de seguridad de red para aislar la aplicación de tarjeta de fidelización de la red corporativa de Contoso.  

Para la **suscripción de desarrollo**, crean los siguientes recursos:

| Tipo de recurso | Nombre | Descripción |
| --- | --- | --- |
| Red virtual |vnInternal |Se utiliza con el entorno de desarrollo de la tarjeta de fidelización de Contoso y se conecta a través de ExpressRoute a la red corporativa de Contoso. |

Para la **suscripción de producción**, crean los siguientes recursos:

| Tipo de recurso | Nombre | Descripción |
| --- | --- | --- |
| Red virtual |vnExternal |Hospeda la aplicación de tarjeta de fidelización y no está conectada directamente a ExpressRoute de Contoso. El código se envía directamente a través de su sistema de código fuente a los servicios de PaaS. |
| Grupo de seguridad de red (NSG) |nsgBitBucket |Garantiza que se reduzca al máximo la superficie de ataque permitiendo solo las conexiones entrantes en el puerto TCP 443.  Contoso también está investigando el uso de un firewall de aplicación web para agregar más protección. |

### <a name="resource-locks"></a>Bloqueos de recursos
David y Alicia se reúnen y deciden agregar bloqueos de recursos en algunos de los recursos claves del entorno para evitar la eliminación accidental durante una inserción de código malintencionado.

Para ello, crean el bloqueo siguiente:

| Tipo de bloqueo | Recurso | Descripción |
| --- | --- | --- |
| **CanNotDelete** |vnExternal |Se utiliza para evitar que los usuarios eliminen la red virtual o las subredes. El bloqueo no impide que se agreguen nuevas subredes. |

### <a name="azure-automation"></a>Automatización de Azure
Alicia y su equipo de desarrollo tienen runbooks de gran tamaño para administrar el entorno de esta aplicación. Los runbooks permiten agregar o eliminar nodos en la aplicación, además de realizar otras tareas de DevOps.

Para usar estos runbooks, habilitan [Automation](../automation/automation-intro.md).

### <a name="azure-security-center"></a>Azure Security Center
El equipo de administración de servicios de TI de Contoso debe identificar y controlar rápidamente las amenazas. También quiere entender los problemas que existan.  

Para cumplir estos requisitos, David habilita Azure Security Center. Se asegura de que Azure Security Center esté supervisando los recursos y proporciona acceso a los equipos de DevOps y seguridad.

## <a name="next-steps"></a>Pasos siguientes
* Para ver más recomendaciones sobre cómo crear plantillas de Resource Manager, consulte [Best practices for creating Azure Resource Manager templates](resource-manager-template-best-practices.md) (Procedimientos recomendados para crear plantillas de Azure Resource Manager).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) ha realizado una aportación a este tema.*



<!--HONumber=Nov16_HO3-->


