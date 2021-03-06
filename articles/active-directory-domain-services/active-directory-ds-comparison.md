---
title: "Azure AD Domain Services: comparación de Azure AD Domain Services con controladores de dominio de implementación personal | Microsoft Docs"
description: "Comparación de Azure Active Directory Domain Services con controladores de dominio de implementación personal"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 67575bbbb7d99ffeef3cb5dab74f4a68065bacc1


---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Cómo decidir si Active Directory Domain Services es adecuado para su caso de uso
Active Directory Domain Service permite implementar cargas de trabajo en los servicios de infraestructura de Azure sin tener que preocuparse por mantener la infraestructura de identidad. Este servicio administrado no es lo mismo que una implementación típica de Windows Server Active Directory donde los usuarios son los encargados de llevarla a cabo y administrarla. El servicio está diseñado para implementaciones sencillas, tareas de corrección y supervisión de estado automatizadas e infraestructura de identidad simples para la nube. Realizamos continuamente cambios en el servicio para agregar compatibilidad con escenarios comunes de implementación.

Decida si quiere utilizar Azure AD Domain Services, o bien poner en marcha y administrar su propia infraestructura de AD en Azure:

* Vea la lista de [características que ofrece Azure AD Domain Services](active-directory-ds-features.md).
* Consulte los [escenarios de implementación comunes de Azure AD Domain Services](active-directory-ds-scenarios.md).
* Finalmente, [compare Azure AD Domain Services con una opción de AD de implementación personal](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparación de Azure AD Domain Services con un dominio de AD de implementación personal en Azure
La tabla siguiente ayuda a decidir si usar Azure AD Domain Services y administrar su propia infraestructura de AD en Azure.

| **Característica** | **Azure AD Domain Services** | **AD de implementación personal en máquinas virtuales de Azure** |
| --- |:---:|:---:|
| [**Servicio administrado**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Implementaciones seguras**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |El administrador debe proteger la implementación. |
| [**Servidor DNS**](active-directory-ds-comparison.md#dns-server) |**&#x2713;** (servicio administrado) |**&#x2713;** |
| [**Domain or Enterprise administrator privileges**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Unión a un dominio**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Autenticación de dominios mediante NTLM y Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Estructura de unidad organizativa personalizada**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Extensiones de esquema**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Confianzas de bosques o dominios de AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP read**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP seguro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP write**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Group Policy**](active-directory-ds-comparison.md#group-policy) |Simple |Completo |
| [**Implementación distribuida geográficamente**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Servicio administrado
Los dominios de Azure AD Domain Services los administra Microsoft. No tiene que preocuparse por las tareas de aplicación de revisiones, actualizaciones, supervisión, lo que garantiza la disponibilidad del dominio. Estas tareas de administración se ofrecen como un servicio de Microsoft Azure en los dominios administrados.

#### <a name="secure-deployments"></a>Implementaciones seguras
Microsoft bloquea de forma segura el dominio administrado según las prácticas recomendadas de seguridad de Microsoft para implementaciones de AD, que se nutren de décadas de experiencia del equipo de productos de AD creando y respaldando implementaciones de este servicio. Para implementaciones personales, debe realizar pasos de implementación específicos para bloquearlas y protegerlas.

#### <a name="dns-server"></a>Servidor DNS
Un dominio de Azure AD Domain Services incluye servicios DNS administrados. Los miembros del grupo Administradores de DC de AAD pueden administrar DNS en el dominio administrado. Asimismo, tienen privilegios completos de administración de DNS en el dominio administrado. La administración de DNS puede realizarse mediante la consola de administración de DNS que se incluye en el paquete de Herramientas de administración remota del servidor (RSAT).

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilegios de administrador de dominio o empresa
Estos privilegios administrados no se ofrecen en dominios administrados de AAD-DS. No se pueden instalar ni ejecutar aplicaciones que requieran estos privilegios elevados en los dominios administrados. Un subconjunto más reducido de privilegios administrativos está disponible para los miembros del grupo de administración delegada llamado "Administradores de DC de AAD". Estos incluyen privilegios para configurar DNS y directivas de grupo, obtener privilegios de administrador en equipos unidos a un dominio, etc.

#### <a name="domain-join"></a>Unión a un dominio
Puede unir máquinas virtuales al dominio administrado de forma similar a como se unen equipos a un dominio de AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticación de dominios mediante NTLM y Kerberos
Con Azure AD Domain Services, puede usar sus credenciales corporativas para autenticarse con el dominio administrado. Las credenciales se mantienen sincronizadas con el inquilino de Azure AD. En lo que respecta a los inquilinos sincronizados, Azure AD Connect garantiza que los cambios realizados en las credenciales desde un entorno local se sincronicen con Azure AD. Con una configuración de dominio de implementación personal, debe establecer una relación de confianza de dominio con un bosque de cuentas locales para que los usuarios se autentiquen con sus credenciales corporativas. Como alternativa, puede configurar la replicación de AD para asegurarse de que las contraseñas de usuario se sincronizan con las máquinas virtuales de controlador de dominio de Azure.

#### <a name="custom-ou-structure"></a>Estructura de unidad organizativa personalizada
Los miembros del grupo Administradores de DC de AAD pueden crear unidades organizativas personalizadas en el dominio administrado.

#### <a name="schema-extensions"></a>Extensiones de esquema
No se puede extender el esquema de base de un dominio administrado de servicios de Azure AD Domain Services. Por lo tanto, las aplicaciones que dependen de las extensiones de esquema de AD (por ejemplo, los nuevos atributos del objeto de usuario) no se transfieren "tal cual" a dominios de AAD-DS.

#### <a name="ad-domain-or-forest-trusts"></a>Confianzas de bosques o dominios de AD
Los dominios administrados no pueden configurarse para establecer relaciones de confianza (entrantes o salientes) con otros dominios. Por lo tanto, en escenarios como implementaciones de bosques de recursos o en aquellos casos donde no prefiera sincronizar contraseñas con Azure AD no podrá usar Azure AD Domain Services.

#### <a name="ldap-read"></a>Lectura LDAP
El dominio administrado es compatible con las cargas de trabajo de lectura LDAP. Por lo tanto, puede implementar aplicaciones que realicen operaciones de lectura LDAP en el dominio administrado.

#### <a name="secure-ldap"></a>LDAP seguro
Puede configurar Azure AD Domain Services para proporcionar acceso LDAP seguro a su dominio administrado, por ejemplo, a través de Internet.

#### <a name="ldap-write"></a>Escritura LDAP
El dominio administrado es de solo lectura para los objetos de usuario. Por lo tanto, las aplicaciones que realizan operaciones de escritura LDAP en los atributos del objeto de usuario no funcionan en un dominio administrado. Además, las contraseñas de usuario no se pueden cambiar en el dominio administrado. Otro ejemplo sería la modificación de pertenencias a grupos o atributos de grupo en el dominio administrado, que tampoco está permitida. Sin embargo, todos los cambios en atributos o contraseñas de usuario realizados en Azure AD (a través Azure Portal o PowerShell) o AD local se sincronizan con el dominio administrado de AAD-DS.

#### <a name="group-policy"></a>Directiva de grupo
No se admiten construcciones de directivas de grupo sofisticadas en el dominio administrado AAD-DS. Por ejemplo, no se pueden crear ni implementar GPO independientes para cada unidad organizativa personalizada en el dominio, ni tampoco usar el filtrado WMI para la selección de directivas de grupo. Hay un GPO integrado para cada uno de los contenedores de equipos y usuarios de AADDC, que pueden personalizarse para configurar directivas de grupo.

#### <a name="geo-dispersed-deployments"></a>Implementaciones dispersas geográficamente
Los dominios administrativos de Azure AD Domain Services están disponibles en una sola red virtual en Azure. Para escenarios que requieren la presencia de controladores de dominio en varias regiones de Azure en todo el mundo, la mejor opción puede ser configurar controladores de dominio en máquinas virtuales IaaS de Azure.

## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opciones de implementación de AD personal
Puede que tenga casos de uso de implementación donde necesite algunas de las funcionalidades que ofrezca una instalación de Windows Server AD. En estos casos, considere una de las siguientes opciones de implementación personal:

* **Dominio en la nube independiente** : puede configurar un dominio en la nube independiente con máquinas virtuales configuradas como controladores de dominio. Esta infraestructura no se integra con su entorno de AD local. Esta opción requeriría un conjunto diferente de credenciales en la nube para iniciar sesión o administrar máquinas virtuales en la nube.
* **Implementación de bosques de recursos** : puede configurar un dominio en la topología de bosque de recursos con máquinas virtuales configuradas como controladores de dominio. Después, podrá configurar una relación de confianza de AD con su entorno de AD local. Puede unir equipos a un dominio (máquinas virtuales de Azure) en este bosque de recursos en la nube. La autenticación de usuarios se produce a través de una conexión VPN o ExpressRoute en el directorio local.
* **Ampliación del dominio local a Azure** : puede conectar una red virtual de Azure a su red local mediante una conexión VPN o ExpressRoute, de modo que las máquinas virtuales de Azure se pueden unir a su entorno de AD local. Otra alternativa es promover controladores de dominio de réplica del dominio local en Azure como una máquina virtual. De esta forma, podrá establecer que se replique a través de una conexión VPN o ExpressRoute en el directorio local. Este modo de implementación amplía de manera eficaz su dominio local a Azure.

> [!NOTE]
> Puede determinar que una opción de implementación personal es más adecuada para sus casos de uso de implementación. Considere la posibilidad de [enviarnos comentarios](active-directory-ds-contact-us.md) para ayudarnos a comprender qué características lo ayudarán a decantarse por Azure AD Domain Services en un futuro. Estos comentarios nos ayudan a mejorar el servicio para que se ajuste mejor a sus necesidades de implementación y casos de uso.
> 
> 

Hemos publicado [directrices de implementación de Windows Server Active Directory en Microsoft Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx) para ayudar a simplificar las instalaciones personales.

## <a name="related-content"></a>Contenido relacionado
* [Características de Azure AD Domain Services](active-directory-ds-features.md)
* [Escenarios de implementación de Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Directrices para implementar Windows Server Active Directory en máquinas virtuales de Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)




<!--HONumber=Dec16_HO4-->


