---
title: "Herramientas de línea de comandos multiplataforma basadas en Azure Resource Manager | Microsoft Docs"
description: "Aprenda a usar las nuevas herramientas de línea de comandos multiplataforma basadas en Azure Resource Manager para administrar sus aplicaciones web de Azure."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 11a9e0a67b71e7ce4adf9713c825511ade4ff7d7
ms.openlocfilehash: 36a6863990e3c4cb87bc4f3a1207ab552c836c20


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Uso de la CLI de XPlat basada en Azure Resource Manager para Azure Web App
> [!div class="op_single_selector"]
> * [CLI de Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Con el lanzamiento de la versión 0.10.5 de las herramientas de línea de comandos multiplataforma de Microsoft Azure, se han agregado nuevos comandos. Estos comandos ofrecen la posibilidad de usar comandos de PowerShell basados en Azure Resource Manager para administrar aplicaciones web.

Para más información sobre cómo administrar grupos de recursos, consulte [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure](../xplat-cli-azure-resource-manager.md). 

## <a name="managing-app-service-plans"></a>Administración de planes del Servicio de aplicaciones
### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones
Para crear un plan de App Service, use el comando **azure appserviceplan create**.

Estas son las descripciones de los distintos parámetros:

* **--resource-group**: grupo de recursos que incluye el plan de App Service reciñen creado.
* **--name**: nombre del plan de App Service.
* **--location**: ubicación del plan de App Service.
* **--tier**:  la sku de precios deseada (las opciones son: F1 (Gratis). D1 (Compartida). B1 (Básico inferior), B2 (Básico medio) y B3 (Básico superior). S1 (Estándar inferior), S2 (Estándar medio) y S3 (Estándar superior). P1 (Premium inferior), P2 (Premium medio) y P3 (Premium superior).)
* **--instances**: el número de trabajos en el plan de App Service (el valor predeterminado es 1).

Ejemplo para usar este cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

Con el mismo comando **azure appserviceplan create**, con el parámetro adicional **--islinux true**. Tenga en cuenta las restricciones y las regiones que se describen en [Introducción a App Service en Linux](app-service-linux-intro.md).

### <a name="list-existing-app-service-plans"></a>Enumeración de planes del Servicio de aplicaciones existentes
Para enumerar los planes de App Service existentes, use el cmdlet **azure appserviceplan list**.

Para enumerar todos los planes del Servicio de aplicaciones de un grupo de recursos específico, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obtener un plan de App Service específico, use el cmdlet **azure appserviceplan show**.

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configuración de un plan del Servicio de aplicaciones existente
Para cambiar la configuración de un plan de App Service existente, use el cmdlet **azure appserviceplan config**. Puede cambiar la sku y el número de trabajos. 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Escalado de un plan del Servicio de aplicaciones
Para escalar un plan del Servicio de aplicaciones existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Cambio de la SKU de un plan de App Service
Para cambiar la sku de un plan de App Service, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminación de un plan del Servicio de aplicaciones
Para eliminar un plan de App Service existente, primero se deben mover o eliminar todas las aplicaciones web asignadas. A continuación, mediante el comando **azure webapp delete**, puede eliminar el plan de App Service.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Administración de aplicaciones web del Servicio de aplicaciones
### <a name="create-a-web-app"></a>Creación de una aplicación web
Para crear una aplicación web, use el comando **azure webapp create**.

Estas son las descripciones de los distintos parámetros:

* **--name**: nombre de la aplicación web.
* **--plan**: nombre del plan usado para hospedar la aplicación web.
* **--resource-group**: grupo de recursos que hospeda el plan de App Service.
* **--location**: la ubicación de la aplicación web.

Ejemplo para usar este cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Eliminación de una aplicación web existente
Para eliminar una aplicación web existente, puede usar el cmdlet **azure webapp delete**, pero debe especificar el nombre de la aplicación web y el nombre del grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Lista de aplicaciones web existentes
Para enumerar las aplicaciones web existentes, use el comando **azure webapp list**.

Para enumerar todas los aplicaciones web de un grupo de recursos específico, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obtener una aplicación web específica, use el comando **azure webapp show**.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configuración de una aplicación web existente
Para cambiar los valores y las configuraciones de una aplicación web existente, use el comando **azure webapp config set**.

Ejemplo (1): Cambio de la versión de php de una aplicación web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Ejemplo (2): Adición o cambio de la configuración de una aplicación

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber qué otra configuración se puede cambiar, use el comando **azure webapp config set -h**.

### <a name="change-the-state-of-an-existing-web-app"></a>Cambio del estado de una aplicación web existente
#### <a name="restart-a-web-app"></a>Reinicio de una aplicación web
Para reiniciar una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Detención de una aplicación web
Para detener una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Inicio de una aplicación web
Para iniciar una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administración de perfiles de publicación de aplicaciones web
Cada aplicación web tiene un perfil de publicación que puede usarse para publicar las aplicaciones.

#### <a name="get-publishing-profile"></a>Obtención de un perfil de publicación
Para obtener el perfil de publicación de una aplicación web, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Este comando devuelve el nombre de usuario y la contraseña del perfil de publicación a la línea de comandos.

### <a name="manage-web-app-hostnames"></a>Administración de nombres de host de aplicaciones web
Para administrar los enlaces de nombre de host de la aplicación web, use el comando **azure webapp config hostnames**.  

#### <a name="list-hostname-bindings"></a>Enumeración de enlaces de nombre de host
Para obtener los enlaces de nombre de host actuales para una aplicación web, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Adición de enlaces de nombre de host
Para agregar enlaces de nombre de host a una aplicación web, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminación de enlaces de nombre de host
Para eliminar enlaces de nombre de host, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Pasos siguientes
* Para aprender sobre la compatibilidad con la CLI de Azure Resource Manager, consulte [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure.](../xplat-cli-azure-resource-manager.md)
* Para más información sobre cómo administrar App Service con PowerShell, consulte [Uso de PowerShell basado en Azure Resource Manager para administrar aplicaciones web de Azure.](app-service-web-app-azure-resource-manager-powershell.md)
* Para más información sobre Azure App Service, consulte [Introducción a App Service en Linux](app-service-linux-intro.md).



<!--HONumber=Nov16_HO3-->


