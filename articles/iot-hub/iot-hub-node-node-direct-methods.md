---
title: "Métodos directos de IoT Hub de Azure (Node) | Microsoft Docs"
description: "Describe cómo usar los métodos directos de IoT Hub de Azure. Usará los SDK de IoT de Azure para Node.js con el fin de implementar una aplicación de dispositivo simulado que incluye un método directo y un servicio que invoque el método directo."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a73c25724a239e56c3ea62a8452bb7c3a2b51be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-on-your-iot-device-with-nodejs"></a>Uso de métodos directos en el dispositivo IoT con Node.js
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

* **CallMethodOnDevice.js**, que llama a un método de la aplicación de dispositivo simulado y muestra la respuesta.
* **SimulatedDevice.js**, que se conecta al centro de IoT con la identidad del dispositivo creada anteriormente y responde al método llamado desde la nube.

> [!NOTE]
> En el artículo [Azure Iot SDKs][lnk-hub-sdks] (SDK de IoT de Azure) se proporciona información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Node.js, versión 4.0.x o posterior.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Node.js que responda a un método que llama desde la nube.

1. Cree una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice**, cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **simulateddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-mqtt**:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Con un editor de texto, cree un nuevo archivo **SimulatedDevice.js** en la carpeta **simulateddevice**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. Agregue una variable **connectionString** y utilícela para crear una instancia de **DeviceClient**. Reemplace **{device connection string}** por la cadena conexión de dispositivo que generó en la sección sobre cómo *crear una identidad de dispositivo*:
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. Agregue la siguiente función para implementar el método en el dispositivo:
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. Abra la conexión al centro de IoT y empiece a por inicializar la escucha del método:
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. Guarde el archivo **SimulatedDevice.js** y ciérrelo.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintento (por ejemplo, reintento de conexión), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Control de errores temporales).
> 
> 

## <a name="call-a-method-on-a-device"></a>Llamada a un método de un dispositivo
En esta sección, creará una aplicación de consola de Node.js que llame a un método de la aplicación de dispositivo simulado y muestre la respuesta.

1. Cree una nueva carpeta vacía denominada **callmethodondevice**. En la carpeta **callmethodondevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **createdeviceidentity**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor de texto, cree un archivo **CallMethodOnDevice.js** en la carpeta **callmethodondevice**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **CallMethodOnDevice.js**:
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    ```
5. Agregue la siguiente declaración de variable y sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para su centro:
   
    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```
6. Cree el cliente para abrir la conexión al centro de IoT.
   
    ```
    var client = Client.fromConnectionString(connectionString);
    ```
7. Agregue la siguiente función para invocar el método de dispositivo e imprimir la respuesta del dispositivo en la consola:
   
    ```
    var methodParams = {
        methodName: methodName,
        payload: 'hello world',
        timeoutInSeconds: 30
    };
   
    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```
8. Guarde y cierre el archivo **CallMethodOnDevice.js**.

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta **simulateddevice** , ejecute el siguiente comando para empezar la escucha de llamadas de método desde IoT Hub:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. En un símbolo del sistema, en la carpeta **callmethodondevice**, ejecute el siguiente comando para empezar la supervisión del centro de IoT Hub:
   
    ```
    node CallMethodOnDevice.js 
    ```
   
    ![][8]
3. Verá cómo el dispositivo reacciona al método imprimiendo el mensaje y cómo la aplicación que llamó al método muestra la respuesta del dispositivo:
   
    ![][9]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para que la aplicación del dispositivo simulado reaccionara a los métodos que se invoquen desde la nube. También creó una aplicación que invoca métodos en el dispositivo y muestra la respuesta del dispositivo. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Introducción a Iot Hub]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-node-node-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-node-node-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introducción a Iot Hub]: iot-hub-node-node-getstarted.md
