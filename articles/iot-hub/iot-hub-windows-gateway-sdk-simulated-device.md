---
title: "Simulación de un dispositivo con el SDK de puerta de enlace de IoT de Azure (Windows) | Microsoft Docs"
description: "Describe cómo usar el SDK de puerta de enlace de IoT de Azure en Windows para crear un dispositivo simulado que envíe datos de telemetría a través de una puerta de enlace a un centro de IoT Hub."
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
ms.date: 02/08/2017
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 5edf2f4c7d9d2e8e8ceb2e8de9ae7cef4c9fd02e
ms.openlocfilehash: 446e7361b5817ce888105fbb78f329affa7de6e6
ms.lasthandoff: 02/06/2017


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-simulated-device-windows"></a>Uso del SDK de puerta de enlace de IoT de Azure para enviar mensajes de dispositivo a nube con un dispositivo simulado (Windows)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo
Antes de comenzar, realice los siguientes pasos:

* [Configure el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Windows.
* [Cree un centro de IoT][lnk-create-hub] en su suscripción de Azure; que necesitará el nombre de su centro para realizar este tutorial. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* Agregue dos dispositivos en su Centro de IoT y tome nota de sus identificadores y claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] para agregar los dispositivos a la instancia de IoT Hub que creó en el paso anterior y recuperar las claves.

Para compilar el ejemplo:

1. Abra un **símbolo del sistema para desarrolladores para VS2015** .
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio y compila la solución. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**. Se pueden pasar parámetros adicionales al script para compilar y ejecutar pruebas unitarias y de principio a fin. Estos parámetros son **--run-unittests** y **--run-e2e-tests**, respectivamente.

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
3. Puede usar la herramienta [Explorador de dispositivos][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] para supervisar los mensajes que la instancia de IoT Hub recibe de la puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes
Si desea una descripción más avanzada del SDK de puerta de enlace de IoT y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

* [Envío de mensajes de un dispositivo físico a la nube con el SDK de puerta de enlace de IoT][lnk-physical-device]
* [SDK de puerta de enlace de IoT de Azure][lnk-gateway-sdk]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Seguridad de la solución de IoT desde el principio][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md

