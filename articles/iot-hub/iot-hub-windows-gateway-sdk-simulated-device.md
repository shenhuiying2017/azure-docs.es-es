<properties
	pageTitle="Simulación de un dispositivo con el SDK de puerta de enlace | Microsoft Azure"
	description="Tutorial del SDK de puerta de enlace de del Centro de IoT de Azure usando Windows para ilustrar el envío de datos de telemetría desde un dispositivo simulado mediante el SDK de puerta de enlace del Centro de IoT de Azure."
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


# SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Compilación y ejecución del ejemplo

Antes de comenzar, realice los siguientes pasos:

- [Configure el entorno de desarrollo][lnk-setupdevbox] para trabajar con el SDK en Windows.
- [Cree un Centro de IoT][lnk-create-hub] en su suscripción de Azure; necesitará el nombre de su centro para realizar este tutorial. Si aún no tiene una suscripción de Azure, puede obtener una [cuenta gratuita][lnk-free-trial].
- Agregue dos dispositivos en su Centro de IoT y tome nota de sus identificadores y claves de dispositivo. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para agregar los dispositivos al Centro de IoT que ha creado en el paso anterior y recuperar sus claves.

Para compilar el ejemplo:

1. Abra un **símbolo del sistema para desarrolladores para VS2015**.
2. Vaya a la carpeta raíz en la copia local del repositorio **azure-iot-gateway-sdk**.
3. Ejecute el script **tools\\build.cmd**. Este script crea un archivo de solución de Visual Studio, genera la solución y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta **build** de su copia local del repositorio **azure-iot-gateway-sdk**¡.

Para ejecutar el ejemplo:

En un editor de texto, abra el archivo **samples\\simulated\_device\_cloud\_upload\\src\\simulated\_device\_cloud\_upload\_win.json** en la copia local del repositorio **azure-iot-gateway-sdk**. Este archivo configura los módulos en la puerta de enlace de ejemplo:

- El módulo **IoTHub** conecta con el Centro de IoT. Debe configurarlo para enviar datos al Centro de IoT. En concreto, establezca el valor **IoTHubName** en el nombre de IoT Hub y el valor **IoTHubSuffix** en **azure-devices.net**. Establezca el valor **Transport** en uno de los siguientes: "HTTP", "AMQP" o "MQTT". Tenga en cuenta que, actualmente, solo "HTTP" compartirá una conexión TCP para todos los mensajes de dispositivo. Si establece el valor en "AMQP" o "MQTT", la puerta de enlace mantendrá una conexión TCP independiente a IoT Hub para cada dispositivo.
- El módulo **mapping** asigna las direcciones MAC de los dispositivos simulados a los id. de dispositivo de IoT Hub. Asegúrese de que los valores **deviceId** coincidan con los id. de los dos dispositivos agregados a IoT Hub y que los valores **deviceKey** contengan las claves de los dos dispositivos.
- Los módulos **BLE1** y **BLE2** son los dispositivos simulados. Observe cómo sus direcciones MAC coinciden con las del módulo de **asignación**.
- El módulo **Registrador** registra la actividad de puerta de enlace en un archivo.
- En los valores de la **ruta de acceso al módulo** que se muestran a continuación se da por hecho que clonó el repositorio del SDK de puerta de enlace en la raíz de la unidad **C:**. Si lo descargó en otra ubicación, deberá ajustar los valores de la **ruta de acceso al módulo** según corresponda.
- La matriz **links** de la parte inferior del archivo JSON conecta los módulos **BLE1** y **BLE2** al módulo de **asignación** y el módulo de **asignación** al módulo **IoTHub**. También garantiza que el módulo **Registrador** registre todos los mensajes.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
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
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
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

1. En un símbolo del sistema, vaya a la carpeta raíz de la copia local del repositorio **azure-iot-gateway-sdk**.
2. Ejecute el siguiente comando:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. Puede usar la herramienta [Explorador de dispositivos o iothub-explorer][lnk-explorer-tools] para supervisar los mensajes que el centro de IoT recibe de la puerta de enlace.


## Pasos siguientes

Si desea una descripción más avanzada del SDK de puerta de enlace y experimentar con algunos ejemplos de código, consulte los siguientes tutoriales y recursos para desarrolladores:

- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo real a través de Linux][lnk-physical-device]
- [SDK de puerta de enlace de IoT (beta): administración de dispositivos con el SDK de puerta de enlace][lnk-manage-devices]
- [SDK de puerta de enlace IoT de Azure][lnk-gateway-sdk]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Diseño de la solución][lnk-design]
- [Guía del desarrollador][lnk-devguide]
- [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md
[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->