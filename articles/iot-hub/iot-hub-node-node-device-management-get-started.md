---
title: "Introducción a la administración de dispositivos | Microsoft Docs"
description: "Este tutorial muestra cómo comenzar con la administración de dispositivos en IoT Hub de Azure"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 829164eaa856d824ed1f37c43799dabb8f0a0868


---
# <a name="tutorial-get-started-with-device-management"></a>Tutorial: Introducción a la administración de dispositivos
## <a name="introduction"></a>Introducción
Las aplicaciones en la nube de IoT pueden utilizar primitivos en IoT Hub de Azure, es decir, el doble de dispositivo y métodos directos para iniciar y supervisar de forma remota las acciones de administración de dispositivos en los dispositivos.  Este artículo proporciona las instrucciones y el código para el funcionamiento conjunto de una aplicación en la nube de IoT y un dispositivo para iniciar y supervisar un reinicio del dispositivo remoto con IoT Hub.

Para iniciar y supervisar remotamente las acciones de administración de dispositivos en los dispositivos desde una aplicación basada en la nube y de back-end, use primitivos de Azure IoT Hub como el [dispositivo gemelo][lnk-devtwin] y los [métodos directos][lnk-c2dmethod]. Este tutorial muestra cómo una aplicación de back-end y un dispositivo pueden trabajar juntos para permitirle iniciar y supervisar el reinicio del dispositivo remoto desde IoT Hub.

Utilice un método directo para iniciar acciones de administración de dispositivos (por ejemplo, reinicio, restablecimiento de fábrica y actualización de firmware) desde una aplicación de back-end en la nube. El dispositivo es responsable de:

* Controlar la solicitud del método enviada desde IoT Hub.
* Iniciar la acción específica del dispositivo correspondiente en el dispositivo.
* Proporcionar actualizaciones de estado a través de las propiedades de las que informa a IoT Hub.

Puede usar una aplicación de back-end en la nube para ejecutar consultas de dispositivos gemelos para informar sobre el progreso de las acciones de administración de los dispositivos.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Usar Azure Portal para un IoT Hub y una identidad de dispositivo en este.
* Crear una aplicación de dispositivo simulado con un método directo que permita un reinicio que se pueda llamar desde la nube.
* Crear una aplicación de consola que llame a un método directo de reinicio en la aplicación de dispositivo simulado a través de su centro de IoT.

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

**dmpatterns_getstarted_device.js**, que se conecta al IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo de reinicio, simula un reinicio físico y notifica la hora del último reinicio.

**dmpatterns_getstarted_service.js**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra las propiedades notificadas actualizadas.

Para completar este tutorial, necesitará lo siguiente:

* Node.js versión 0.12.x o posteriores. <br/>  En [Preparación del entorno de desarrollo][lnk-dev-setup] se describe cómo instalar Node.js para este tutorial en Windows o Linux.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de Node.js que responda a un método directo que se llama desde la nube.
* Desencadenará un reinicio del dispositivo simulado.
* Utilizará las propiedades notificadas para habilitar consultas de dispositivos gemelos a fin de identificar los dispositivos y cuándo se reiniciaron por última vez.

1. Cree una nueva carpeta vacía denominada "**manageddevice**".  En la carpeta **manageddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **manageddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un nuevo archivo **dmpatterns_getstarted_device.js** en la carpeta **manageddevice**.
4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_getstarted_device.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo.  Reemplace la cadena de conexión por la cadena de conexión del dispositivo.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Agregue la siguiente función para implementar el método directo en el dispositivo
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```
7. Abra la conexión al IoT Hub e inicie la escucha del método directo:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
8. Guarde y cierre el archivo **dmpatterns_getstarted_device.js**.
   
   [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Desencadenar un reinicio remoto en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de Node.js que inicia un reinicio remoto en un dispositivo con un método directo y utiliza las consultas del dispositivo gemelo para obtener el tiempo de reinicio más reciente para ese dispositivo.

1. Cree una nueva carpeta vacía denominada **triggerrebootondevice**.  En la carpeta **triggerrebootondevice**, cree un archivo package.json con el siguiente comando en el símbolo del sistema.  Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **triggerrebootondevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iothub** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor de texto, cree un nuevo archivo**dmpatterns_getstarted_service.js** en la carpeta **triggerrebootondevice**.
4. Agregue las siguientes instrucciones "require" al principio del archivo **dmpatterns_getstarted_service.js**:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Agregue las siguientes declaraciones de variable y reemplace los valores de marcador de posición:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Agregue la siguiente función para invocar el método del dispositivo con el fin de reiniciar el dispositivo de destino:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```
7. Agregue la siguiente función para consultar el dispositivo y obtener la última hora de reinicio:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
8. Agregue el siguiente código para llamar a las funciones que desencadenarán el método directo de reinicio y la consulta de última hora de reinicio:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Guarde y cierre el archivo **dmpatterns_getstarted_service.js**.

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema dentro de la carpeta **manageddevice**, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. En el símbolo del sistema dentro de la carpeta **triggerrebootondevice**, ejecute el siguiente comando para desencadenar el reinicio remoto y la consulta en el dispositivo gemelo para buscar la hora del último reinicio.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Verá la respuesta del dispositivo al método directo en la consola.

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar y ampliar el dispositivo las acciones de administración del dispositivo
Las soluciones de IoT pueden expandir el conjunto definido de patrones de la administración de dispositivos o habilitar modelos personalizados mediante el uso del dispositivo gemelo y primitivos del método directo. Otros ejemplos de acciones de administración de dispositivos son el restablecimiento de fábrica, la actualización de firmware, la actualización de software, la administración de energía, la administración de conectividad y red, y el cifrado de datos.

## <a name="device-maintenance-windows"></a>Ventanas de mantenimiento del dispositivo
Normalmente, puede dispositivos para llevar a cabo acciones a la vez que minimiza las interrupciones y el tiempo de inactividad.  Las ventanas de mantenimiento dle dispositivo son un patrón que se utiliza habitualmente para definir la hora en la que un dispositivo debe actualizar su configuración. Las soluciones de back-end pueden utilizar las propiedades deseadas del dispositivo gemelo para definir y activar una directiva en el dispositivo que permita una ventana de mantenimiento. Cuando un dispositivo recibe la directiva de la ventana de mantenimiento, puede usar la propiedad notificada del dispositivo gemelo para informar del estado de la directiva. La aplicación de back-end puede usar luego consultas de dispositivos gemelos para dar testimonio de cumplimiento de dispositivos y cada directiva.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial ha usado un método directo para desencadenar un reinicio remoto en un dispositivo, ha utilizado las propiedades notificadas para notificar la última hora de reinicio del dispositivo y ha consultado el dispositivo gemelo para detectar la última hora de reinicio del dispositivo desde la nube.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remoto a través de la actualización de firmware de aire, consulte:

[Tutorial: Cómo realizar una actualización de firmware][lnk-fwupdate]

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

Para continuar con la introducción a IoT Hub, consulte [Introducción al SDK de puerta de enlace de IoT de Azure][lnk-gateway-SDK].

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx



<!--HONumber=Nov16_HO5-->


