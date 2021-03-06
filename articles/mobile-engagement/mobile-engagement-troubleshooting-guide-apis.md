---
title: "Guía de solución de problemas de Azure Mobile Engagement - API"
description: "Guías de solución de problemas de Azure Mobile Engagement - API"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3efc8a52-2b74-4917-b887-815ae8277474
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e0bd65366ef1162a4b82c35a86fc42708226e6ed


---
# <a name="troubleshooting-guide-for-api-issues"></a>Guía de solución de problemas de la API
Los siguientes son posibles problemas que pueden producirse con cómo los administradores interactúan con Azure Mobile Engagement a través de las API.

## <a name="syntax-issues"></a>Problemas de sintaxis
### <a name="issue"></a>Problema
* Errores de sintaxis mediante la API (o un comportamiento inesperado).

### <a name="causes"></a>Causas
* Problemas de sintaxis:
  * Asegúrese de comprobar la sintaxis de la API específica que se utiliza para confirmar que la opción está disponible.
  * Un problema común con el uso de la API consiste en confundir la API de cobertura y la API de inserción (la mayoría de las tareas debe realizarse con la API de cobertura en lugar de la API de inserción). 
  * Otro problema común con la integración del SDK y el uso de la API es confundir la clave del SDK y la clave de la API.
  * Las secuencias de comandos que se conectan a las API necesitan enviar datos al menos cada 10 minutos o la conexión agotará el tiempo de espera (especialmente frecuente en secuencias de comandos de API de supervisión que escuchan datos). Para evitar los tiempos de espera, haga que la secuencia de comandos envíe un ping XMPP cada 10 minutos para mantener la sesión activa con el servidor.

### <a name="see-also"></a>Consulte también
* [Documentación de la API][Vínculo 4]
* [Información del protocolo XMPP](http://xmpp.org/extensions/xep-0199.html)

## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>No se puede usar la API para realizar la misma acción disponible en la interfaz de usuario de Azure Mobile Engagement
### <a name="issue"></a>Problema
* Una acción que funciona desde la interfaz de usuario de Azure Mobile Engagement no funciona desde la API de Azure Mobile Engagement relacionada.

### <a name="causes"></a>Causas
* Confirmar que puede realizar la misma acción desde la interfaz de usuario de Azure Mobile Engagement muestra que ha integrado correctamente esta función de Azure Mobile Engagement con el SDK.

### <a name="see-also"></a>Consulte también
* [Documentación de la interfaz de usuario][Vínculo 1]

## <a name="error-messages"></a>Mensajes de error
### <a name="issue"></a>Problema
* Códigos de error mediante la API que se muestra en el tiempo de ejecución o en los registros.

### <a name="causes"></a>Causas
* A continuación se muestra una lista compuesta de números de códigos de estado de API comunes de referencia y de solución de problemas preliminar:
  
        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Consulte también
* [Documentación de la API (para ver errores detallados específicos de cada API)][Vínculo 4]

## <a name="silent-failures"></a>Errores silenciosos
### <a name="issue"></a>Problema
* Se produce un error en la acción de la API sin que se muestre ningún mensaje de error en el tiempo de ejecución o en los registros.

### <a name="causes"></a>Causas
* Muchos elementos se deshabilitarán en la interfaz de usuario de Azure Mobile Engagement si no se integran correctamente, pero se producirá un error silenciosamente a través de la API, por lo tanto, no olvide probar la misma funcionalidad desde la interfaz de usuario para ver si funciona.
* Azure Mobile Engagement y muchas funciones avanzadas de Azure Mobile Engagement que está intentando usar necesitan integrarse individualmente en su aplicación con el SDK en pasos distintos para poder utilizarlas.

### <a name="see-also"></a>Consulte también
* [Guía de solución de problemas: SDK][Vínculo 25]

<!--Link references-->
[Vínculo 1]: mobile-engagement-user-interface-home.md
[Vínculo 2]: mobile-engagement-troubleshooting-guide.md
[Vínculo 3]: mobile-engagement-how-tos.md
[Vínculo 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Vínculo 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Vínculo 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Vínculo 7]: https://account.windowsazure.com/PreviewFeatures
[Vínculo 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Vínculo 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Vínculo 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Vínculo 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Vínculo 12]: mobile-engagement-user-interface-navigation.md
[Vínculo 13]: mobile-engagement-user-interface-home.md
[Vínculo 14]: mobile-engagement-user-interface-my-account.md
[Vínculo 15]: mobile-engagement-user-interface-analytics.md
[Vínculo 16]: mobile-engagement-user-interface-monitor.md
[Vínculo 17]: mobile-engagement-user-interface-reach.md
[Vínculo 18]: mobile-engagement-user-interface-segments.md
[Vínculo 19]: mobile-engagement-user-interface-dashboard.md
[Vínculo 20]: mobile-engagement-user-interface-settings.md
[Vínculo 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Vínculo 22]: mobile-engagement-troubleshooting-guide-apis.md
[Vínculo 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Vínculo 24]: mobile-engagement-troubleshooting-guide-service.md
[Vínculo 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Vínculo 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Vínculo 27]: mobile-engagement-user-interface-reach-campaign.md
[Vínculo 28]: mobile-engagement-user-interface-reach-criterion.md
[Vínculo 29]: mobile-engagement-user-interface-reach-content.md




<!--HONumber=Nov16_HO3-->


