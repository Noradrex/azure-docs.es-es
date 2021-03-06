---
title: "Creación de un recurso de Application Insights nuevo | Microsoft Docs"
description: "Configuración de la supervisión de Application Insights para una nueva aplicación activa. Enfoque basado en web."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 0157b796a09b5685386cd96d6969da04179279dc


---
# <a name="create-an-application-insights-resource"></a>Creación de recursos en Application Insights
Azure Application Insights muestra los datos de la aplicación en un *recurso*de Microsoft Azure. Por tanto, la creación de un nuevo recurso forma parte de la [configuración de Application Insights para supervisar una aplicación nueva][start]. En muchos casos, esto puede realizarse automáticamente por el IDE, y esta es la manera recomendada cuando esté disponible. Pero en algunos casos, se crea un recurso manualmente.

Después de haber creado el recurso, obtiene su clave de instrumentación y la usa para configurar el SDK en la aplicación. Esto envía la telemetría al recurso.

## <a name="sign-up-to-microsoft-azure"></a>Suscripción a Microsoft Azure
Si todavía no dispone de una [cuenta Microsoft, obtenga una ahora](http://live.com). (Si usa servicios como Outlook.com, OneDrive, Windows Phone o XBox Live, ya tiene una cuenta Microsoft).

También necesitará una suscripción a [Microsoft Azure](http://azure.com). Si su equipo u organización tiene una suscripción a Azure, el propietario puede agregarle a ella mediante su Windows Live ID.

O bien, puede crear una suscripción. La cuenta gratuita permite probar todos los componentes de Azure. Después de que expire el período de prueba, podría encontrar la suscripción de pago por uso adecuada, ya que no se le cobrarán los servicios gratuitos. 

Cuando tenga acceso a una suscripción, inicie sesión en Application Insights en [http://portal.azure.com](https://portal.azure.com)y utilice su Live ID para iniciar sesión.

## <a name="create-an-application-insights-resource"></a>Creación de recursos en Application Insights
En el [portal.azure.com](https://portal.azure.com), agregue un recurso de Application Insights:

![Haga clic en Nuevo, Application Insights.](./media/app-insights-create-new-resource/01-new.png)

* **Tipo de aplicación** afecta a lo que se ve en la hoja de información general y a las propiedades disponibles en el [explorador de métricas][metrics]. Si no ve el tipo de aplicación, elija ASP.NET.
* **grupo de recursos** resulta práctico para administrar las propiedades, como el control de acceso. Si ya ha creado otros recursos de Azure, puede optar por colocar este recurso nuevo en el mismo grupo.
* **suscripción** es su cuenta de pago de Azure.
* **ubicación** es donde se guardan los datos.
* **agregar al panel de inicio** coloca un icono de acceso rápido al recurso en la página principal de Azure. Se recomienda su uso.

Cuando se haya creado la aplicación, se abrirá una nueva hoja. Aquí es donde podrá ver datos de uso y rendimiento sobre la aplicación. 

Para volver aquí la próxima vez que inicie sesión en Azure, busque el icono de inicio rápido de la aplicación en el panel de inicio (pantalla principal). O bien, haga clic en Examinar para buscar esta opción.

## <a name="copy-the-instrumentation-key"></a>Copia de la clave de instrumentación
La clave de instrumentación identifica al recurso que ha creado. La necesitará para proporcionársela al SDK.

![Haga clic en Essentials y elija la clave de instrumentación, CTRL + C](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Instalación del SDK en la aplicación
Instale el SDK de Application Insights en su aplicación. Este paso depende en gran medida del tipo de aplicación. 

Use la clave de instrumentación para configurar el [SDK que se instala en la aplicación][start].

El SDK incluye módulos estándar que envían telemetría sin tener que escribir código. Para realizar un seguimiento de las acciones del usuario o diagnosticar los problemas con más detalle, [use la API][api] para enviar sus propios datos de telemetría.

## <a name="a-namemonitorasee-telemetry-data"></a><a name="monitor"></a>Visualización de los datos de telemetría
Cierre la hoja de inicio rápido para volver a la hoja de aplicación en el portal de Azure.

Haga clic en el icono de búsqueda para ver [Búsqueda de diagnóstico][diagnostic], donde aparecerán los primeros eventos. 

Si espera más datos, haga clic en Actualizar después de unos segundos.

## <a name="creating-a-resource-automatically"></a>Creación de un recurso de forma automática
Puede escribir un [script de PowerShell](app-insights-powershell-script-create-resource.md) para crear automáticamente un recurso.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un panel](app-insights-dashboards.md)
* [Búsqueda de diagnóstico](app-insights-diagnostic-search.md)
* [Exploración de métricas](app-insights-metrics-explorer.md)
* [Escribir consultas de Analytics](app-insights-analytics.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md




<!--HONumber=Nov16_HO3-->


