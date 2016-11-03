<properties
    pageTitle="Simulación de un dispositivo con el SDK de puerta de enlace | Microsoft Azure"
    description="Tutorial del SDK de puerta de enlace de del Centro de IoT de Azure usando Linux para ilustrar el envío de datos de telemetría desde un dispositivo simulado mediante el SDK de puerta de enlace del Centro de IoT de Azure."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-beta-send-devicetocloud-messages-with-a-simulated-device-using-linux"></a>SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

Antes de comenzar, realice los siguientes pasos:

- [Configure el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Linux.
- [Cree un centro de IoT][lnk-create-hub] en su suscripción de Azure; necesitará el nombre de su centro para realizar este tutorial. Si aún no tiene una suscripción de Azure, puede obtener una [cuenta gratuita][lnk-free-trial].
- Agregue dos dispositivos en su Centro de IoT y tome nota de sus identificadores y claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para agregar los dispositivos al centro de IoT que ha creado en el paso anterior y recuperar sus claves.

Para compilar el ejemplo:

1. Abra un shell.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
3. Ejecute el script **tools/build.sh** . Este script emplea la utilidad **cmake** para crear una carpeta llamada **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk** y generar un archivo Make. A continuación, el script compila la solución y ejecuta las pruebas.

> [AZURE.NOTE]  Cada vez que ejecute el script **build.sh**, elimina y luego vuelve a crear la carpeta **build** en la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.

Para ejecutar el ejemplo:

En un editor de texto, abra el archivo **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** en la copia local del repositorio **azure-iot-gateway-sdk**. Este archivo configura los módulos en la puerta de enlace de ejemplo:

- El módulo **IoTHub** conecta con el Centro de IoT. Debe configurarlo para enviar datos al Centro de IoT. En concreto, establezca el valor **IoTHubName** en el nombre de IoT Hub y el valor **IoTHubSuffix** en **azure-devices.net**. Establezca el valor **Transport** en uno de los siguientes: "HTTP", "AMQP" o "MQTT". Tenga en cuenta que, actualmente, solo "HTTP" compartirá una conexión TCP para todos los mensajes de dispositivo. Si establece el valor en "AMQP" o "MQTT", la puerta de enlace mantendrá una conexión TCP independiente a IoT Hub para cada dispositivo.
- El módulo **mapping** asigna las direcciones MAC de los dispositivos simulados a los id. de dispositivo de su centro de IoT. Asegúrese de que los valores **deviceId** coincidan con los id. de los dos dispositivos agregados a su centro de IoT y que los valores **deviceKey** contengan las claves de los dos dispositivos.
- Los módulos **BLE1** y **BLE2** son los dispositivos simulados. Observe cómo sus direcciones MAC coinciden con las del módulo de **asignación** .
- El módulo **Registrador** registra la actividad de puerta de enlace en un archivo.
- Los valores de **ruta de acceso** del módulo mostrados a continuación dan por hecho que ejecutará el ejemplo desde la raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
- La matriz **links** de la parte inferior del archivo JSON conecta los módulos **BLE1** y **BLE2** al módulo de **asignación** y el módulo de **asignación** al módulo **IoTHub**. También garantiza que el módulo **Registrador** registre todos los mensajes.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothub/libiothub_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentity_map_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}

```

Guarde los cambios realizados en el archivo de configuración.

Para ejecutar el ejemplo:

1. En el shell, vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk** .
2. Ejecute el siguiente comando:

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```

3. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para supervisar los mensajes que IoT Hub recibe de la puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes

Si desea una descripción más avanzada del SDK de puerta de enlace y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo real a través de Linux][ lnk-physical-device]
- [SDK de puerta de enlace de Azure IoT][lnk-gateway-sdk]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Guía para desarrolladores][lnk-devguide]
- [Seguridad de la solución de IoT desde el principio][lnk-securing]

<!-- Links -->
[lnk setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md


<!--HONumber=Oct16_HO2-->


