---
title: "Simulación de un dispositivo con el SDK de puerta de enlace de IoT | Microsoft Docs"
description: "Tutorial del SDK de puerta de enlace de IoT de Azure con Windows para ilustrar el envío de datos de telemetría desde un dispositivo simulado mediante el SDK de puerta de enlace de IoT de Azure."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 6a2aeda0-696a-4732-90e1-595d2e2fadc6
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f5eeea933dbd5b63c6a8f2bd5b065a13286a4bae


---
# <a name="azure-iot-gateway-sdk--send-device-to-cloud-messages-with-a-simulated-device-app-using-windows"></a>SDK de puerta de enlace de IoT de Azure: envío de mensajes del dispositivo a la nube con una aplicación de dispositivo simulado mediante Windows
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo
Antes de comenzar, realice los siguientes pasos:

* [Configure el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Windows.
* [Cree un centro de IoT][lnk-create-hub] en su suscripción de Azure; que necesitará el nombre de su centro para realizar este tutorial. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* Agregue dos dispositivos en su Centro de IoT y tome nota de sus identificadores y claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para agregar los dispositivos al centro de IoT que creó en el paso anterior y recuperar las claves.

Para compilar el ejemplo:

1. Abra un **símbolo del sistema para desarrolladores para VS2015** .
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio, genera la solución y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**.

Para ejecutar el ejemplo:

En un editor de texto, abra el archivo **samples\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** en la copia local del repositorio **azure-iot-gateway-sdk**. Este archivo configura los módulos en la puerta de enlace de ejemplo:

* El módulo **IoTHub** conecta con el Centro de IoT. Debe configurarlo para enviar datos al Centro de IoT. En concreto, establezca el valor **IoTHubName** en el nombre de IoT Hub y el valor **IoTHubSuffix** en **azure-devices.net**. Establezca el valor **Transport** en uno de los siguientes: "HTTP", "AMQP" o "MQTT". Tenga en cuenta que, actualmente, solo "HTTP" compartirá una conexión TCP para todos los mensajes de dispositivo. Si establece el valor en "AMQP" o "MQTT", la puerta de enlace mantendrá una conexión TCP independiente a IoT Hub para cada dispositivo.
* El módulo **mapping** asigna las direcciones MAC de los dispositivos simulados a los id. de dispositivo de su centro de IoT. Asegúrese de que los valores **deviceId** coincidan con los id. de los dos dispositivos agregados a su centro de IoT y que los valores **deviceKey** contengan las claves de los dos dispositivos.
* Los módulos **BLE1** y **BLE2** son los dispositivos simulados. Observe cómo sus direcciones MAC coinciden con las del módulo de **asignación** .
* El módulo **Registrador** registra la actividad de puerta de enlace en un archivo.
* En los valores de la **ruta de acceso al módulo** que se muestran a continuación se da por hecho que clonó el repositorio del SDK de puerta de enlace de IoT en la raíz de la unidad **C:**. Si lo descargó en otra ubicación, deberá ajustar los valores de la **ruta de acceso al módulo** según corresponda.
* La matriz **links** de la parte inferior del archivo JSON conecta los módulos **BLE1** y **BLE2** al módulo de **asignación** y el módulo de **asignación** al módulo **IoTHub**. También garantiza que el módulo **Registrador** registre todos los mensajes.

```
{
    "modules" :
    [
      {
        "name": "IotHub",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\iothub\\Debug\\iothub.dll"
          }
          },
          "args": {
            "IoTHubName": "<<insert here IoTHubName>>",
            "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
            "Transport": "HTTP"
          }
        },
      {
        "name": "mapping",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\identitymap\\Debug\\identity_map.dll"
          }
          },
          "args": [
            {
              "macAddress": "01:01:01:01:01:01",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            },
            {
              "macAddress": "02:02:02:02:02:02",
              "deviceId": "<<insert here deviceId>>",
              "deviceKey": "<<insert here deviceKey>>"
            }
          ]
        },
      {
        "name": "BLE1",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "01:01:01:01:01:01"
          }
        },
      {
        "name": "BLE2",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\simulated_device\\Debug\\simulated_device.dll"
          }
          },
          "args": {
            "macAddress": "02:02:02:02:02:02"
          }
        },
      {
        "name": "Logger",
        "loader": {
          "name": "native",
          "entrypoint": {
            "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
          }
        },
        "args": {
          "filename": "deviceCloudUploadGatewaylog.log"
        }
      }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IotHub" }
    ]
}
```

Guarde los cambios realizados en el archivo de configuración.

Para ejecutar el ejemplo:

1. En un símbolo del sistema, vaya a la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** .
2. Ejecute el siguiente comando:
   
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```
3. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para supervisar los mensajes que el centro de IoT recibe de la puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes
Si desea una descripción más avanzada del SDK de puerta de enlace de IoT y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

* [Envío de mensajes de un dispositivo físico a la nube con el SDK de puerta de enlace de IoT][lnk-physical-device]
* [SDK de puerta de enlace de IoT de Azure][lnk-gateway-sdk]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía del desarrollador][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Nov16_HO5-->


