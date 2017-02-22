---
title: "Envío de mensajes de nube a dispositivo con IoT Hub | Microsoft Docs"
description: Siga este tutorial para aprender a enviar mensajes de nube a dispositivo mediante el Centro de IoT de Azure con Java.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: fdd0a695675aae56d87bb62a3299bbadf1b1676f


---
# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Tutorial: cómo enviar mensajes de la nube a un dispositivo con el Centro de IoT y Node.js
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción
Azure IoT Hub es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una aplicación back-end. El tutorial [Introducción a Iot Hub] muestra cómo crear un centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar una aplicación de dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en la [Introducción a Iot Hub]. En él se muestra cómo realizar las siguientes acciones:

* Desde el back-end de la nube de la aplicación, envíe mensajes de la nube al dispositivo en un único dispositivo a través de Centro de IoT.
* Reciba mensajes de nube a dispositivo en un dispositivo.
* Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde Centro de IoT.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores de IoT Hub][IoT Hub Developer Guide - C2D].

Al final de este tutorial, usted ejecuta dos aplicaciones de consola de Node.js:

* **SimulatedDevice**, versión modificada de la aplicación creada en [Introducción a Iot Hub], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **SendCloudToDeviceMessage**, que envía un mensaje de la nube a la aplicación de dispositivo simulado mediante IoT Hub y, luego, recibe su confirmación de entrega.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure]para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Node.js versión 0.10.x, o posteriores.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

## <a name="receive-messages-in-the-simulated-device-app"></a>Recepción de mensajes en la aplicación de dispositivo simulado
En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción a Iot Hub] para recibir mensajes de nube a dispositivo desde el centro de IoT.

1. Con un editor de texto, abra el archivo SimulatedDevice.js.
2. Modifique la función **connectCallback** para controlar los mensajes enviados desde Centro de IoT. En este ejemplo, el dispositivo siempre invoca la función **complete** para notificar al Centro de IoT que ha procesado el mensaje. La nueva versión de la función **connectCallback** es como sigue:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
   > [!NOTE]
   > Si usa HTTP en lugar de MQTT o AMQP como transporte, la instancia de **DeviceClient** busca mensajes del IoT Hub con menos frecuencia (menos de 25 minutos). Consulte la [Guía del desarrollador de IoT Hub][IoT Hub Developer Guide - C2D] para más información sobre las diferencias entre la compatibilidad con MQTT, AMQP y HTTP, y la limitación de IoT Hub.
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo
En esta sección, usted crea una aplicación de consola de Node.js que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado. Necesita el identificador de dispositivo que agregó en el tutorial de [Introducción a Iot Hub] . También necesita la cadena de conexión para IoT Hub que encontrará en [Azure Portal].

1. Cree una carpeta vacía denominada **sendcloudtodevicemessage**. En la carpeta **sendcloudtodevicemessage** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema en la carpeta **sendcloudtodevicemessage**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor de texto, cree un nuevo archivo **SendCloudToDeviceMessage.js** en la carpeta **sendcloudtodevicemessage**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **SendCloudToDeviceMessage.js** :
   
    ```
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Agregue el código siguiente al archivo **SendCloudToDeviceMessage.js** . Sustituya el valor de marcador de posición de cadena de conexión por la cadena de conexión del IoT Hub que creó en el tutorial [Introducción a Iot Hub] . Sustituya el marcador de posición de dispositivo por el identificador de dispositivo que agregó en el tutorial [Introducción a Iot Hub] :
   
    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Agregue la función siguiente para imprimir los resultados de la operación en la consola:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Agregue la función siguiente para imprimir mensajes de comentarios de entrega en la consola:
   
    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Agregue el código siguiente para enviar un mensaje a su dispositivo y procesar el mensaje de comentarios cuando el dispositivo reconozca el mensaje de la nube al dispositivo:
   
    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Guarde y cierre el archivo **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema de la carpeta **simulateddevice** , ejecute el comando siguiente para enviar la telemetría al Centro de IoT y escuchar mensajes de nube al dispositivo:
   
    ```
    node SimulatedDevice.js 
    ```
   
    ![Ejecución de una aplicación de dispositivo simulada][img-simulated-device]
2. En un símbolo del sistema de la carpeta **sendcloudtodevicemessage** , ejecute el comando siguiente para enviar un mensaje de nube al dispositivo y espere el comentario de confirmación:
   
    ```
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Ejecución de la aplicación para enviar el comando C2D][img-send-command]
   
   > [!NOTE]
   > Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling](Control de errores transitorios).
   > 
   > 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. 

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT].

Para más información sobre cómo desarrollar soluciones con el Centro de IoT, consulte la [Guía del desarrollador del Centro de IoT de Azure].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introducción a Iot Hub]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guía del desarrollador del Centro de IoT de Azure]: iot-hub-devguide.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Documentación del Conjunto de aplicaciones de IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/



<!--HONumber=Nov16_HO5-->


