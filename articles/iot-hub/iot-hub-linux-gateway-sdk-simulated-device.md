---
title: "Simulación de un dispositivo con Azure IoT Edge (Linux) | Documentos de Microsoft"
description: "Describe cómo usar Azure IoT Edge en Linux para crear un dispositivo simulado que envíe datos de telemetría a través de una puerta de enlace de IoT Edge a un centro de IoT Hub."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 11e7bf28-ee3d-48d6-a386-eb506c7a31cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: eea65c3befb2e7924ccdd174c6d493f0c79b95c6
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="use-azure-iot-edge-to-send-device-to-cloud-messages-with-a-simulated-device-linux"></a>Uso de Azure IoT Edge para enviar mensajes de dispositivo a nube con un dispositivo simulado (Linux)
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo
Antes de comenzar, realice los siguientes pasos:

* [Configure el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.
* [Cree un centro de IoT][lnk-create-hub] en su suscripción de Azure; que necesitará el nombre de su centro para realizar este tutorial. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* Agregue dos dispositivos a su centro de IoT y tome nota de los identificadores y las claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] para agregar los dispositivos a la instancia de IoT Hub que creó en el paso anterior y recuperar las claves.

Para compilar el ejemplo:

1. Abra un shell.
2. Vaya a la carpeta raíz en la copia local del repositorio **iot-edge**.
3. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **iot-edge** y generar un archivo Make. A continuación, el script compila la solución, omitiendo las pruebas unitarias y las pruebas completas. Agregue el parámetro **--run-unittests** si desea crear y ejecutar las pruebas unitarias. Agregue el parámetro **--run-e2e-tests** si desea crear y ejecutar las pruebas completas. 

> [!NOTE]
> Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **iot-edge**.
> 
> 

Para ejecutar el ejemplo:

En un editor de texto, abra el archivo **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** en la copia local del repositorio **azure-iot-gateway-sdk**. Este archivo configura los módulos de IoT Edge en la puerta de enlace de ejemplo:

* El módulo **IoTHub** conecta con el centro de IoT. Debe configurarlo para enviar datos al Centro de IoT. En concreto, establezca el valor **IoTHubName** en el nombre de IoT Hub y el valor **IoTHubSuffix** en **azure-devices.net**. Establezca el valor **Transport** en uno de los siguientes: "HTTP", "AMQP" o "MQTT". Tenga en cuenta que, actualmente, solo "HTTP" compartirá una conexión TCP para todos los mensajes de dispositivo. Si establece el valor en "AMQP" o "MQTT", la puerta de enlace mantendrá una conexión TCP independiente a IoT Hub para cada dispositivo.
* El módulo **mapping** asigna las direcciones MAC de los dispositivos simulados a los id. de dispositivo de su centro de IoT. Asegúrese de que los valores **deviceId** coincidan con los identificadores de los dos dispositivos agregados a su centro de IoT y que los valores **deviceKey** contengan las claves de los dos dispositivos.
* Los módulos **BLE1** y **BLE2** son los dispositivos simulados. Observe cómo sus direcciones MAC coinciden con las del módulo de **asignación** .
* El módulo **Registrador** registra la actividad de puerta de enlace en un archivo.
* Los valores de **ruta de acceso** del módulo mostrados a continuación dan por hecho que ejecutará el ejemplo desde la raíz de la copia local del repositorio **iot-edge**.
* La matriz **links** de la parte inferior del archivo JSON conecta los módulos **BLE1** y **BLE2** al módulo de **asignación** y el módulo de **asignación** al módulo **IoTHub**. También garantiza que el módulo **Registrador** registre todos los mensajes.

```
{
    "modules": [
        {
            "name": "IotHub",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "./modules/iothub/libiothub.so"
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
              "module.path": "./modules/identitymap/libidentity_map.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/simulated_device/libsimulated_device.so"
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
              "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args": {
              "filename": "deviceCloudUploadGatewaylog.log"
            }
          }
    ],
    "links": [
        {
            "source": "*",
            "sink": "Logger"
        },
        {
            "source": "BLE1",
            "sink": "mapping"
        },
        {
            "source": "BLE2",
            "sink": "mapping"
        },
        {
            "source": "mapping",
            "sink": "IotHub"
        }
    ]
}
```

Guarde los cambios realizados en el archivo de configuración.

Para ejecutar el ejemplo:

1. En el shell, vaya a la carpeta **iot-edge/build**.
2. Ejecute el siguiente comando:
   
    ```
    ./samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./../samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. Puede usar la herramienta [Explorador de dispositivos][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] para supervisar los mensajes que la instancia de IoT Hub recibe de la puerta de enlace de IoT Edge.

## <a name="next-steps"></a>Pasos siguientes
Si desea una descripción más avanzada de Azure IoT Edge y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

* [Envío de mensajes de un dispositivo físico a la nube con Azure IoT Edge][lnk-physical-device]
* [Azure IoT Edge][lnk-gateway-sdk]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Protección total de la solución de IoT][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-gateway-sdk]: https://github.com/Azure/iot-edge/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md

