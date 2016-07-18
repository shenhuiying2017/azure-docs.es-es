<properties
	pageTitle="Envío de mensajes de nube a dispositivo con el Centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensajes de nube a dispositivo mediante el Centro de IoT de Azure con Java."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/04/2016"
     ms.author="dobett"/>

# Tutorial: cómo enviar mensajes de la nube a un dispositivo con el Centro de IoT y Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introducción

Centro de IoT de Azure es un servicio totalmente administrado que ayuda a habilitar la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de aplicación. El tutorial [Introducción al Centro de IoT] muestra cómo crear un Centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar un dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en el artículo [Introducción al Centro de IoT de Azure para Node.js]. En él se muestra cómo realizar las siguientes acciones:

- Desde el back-end de la nube de la aplicación, envíe mensajes de la nube al dispositivo en un único dispositivo a través de Centro de IoT.
- Reciba mensajes de nube a dispositivo en un dispositivo.
- Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde Centro de IoT.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, ejecutará dos aplicaciones de consola de Node.js:

* **SimulatedDevice**, versión modificada de la aplicación creada en [Introducción al Centro de IoT], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **SendCloudToDeviceMessage**, que envía un mensaje de la nube al dispositivo simulado mediante el Centro de IoT y, luego, recibe su confirmación de entrega.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

Para completar este tutorial, necesitará lo siguiente:

+ Node.js versión 0.12.x o posteriores. <br/>En [Prepare your development environment][lnk-dev-setup] (Preparación de un entorno de desarrollo) se describe cómo instalar Node.js para este tutorial en Windows o Linux.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Recepción de mensajes en el dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Introducción al Centro de IoT de Azure para .NET] para recibir mensajes de la nube a un dispositivo desde el Centro de IoT.

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

    > [AZURE.NOTE] Si usa HTTP/1 en lugar de AMQP como transporte, la instancia de **DeviceClient** busca mensajes del Centro de IoT con menos frecuencia (menos de 25 minutos). Consulte la [Guía del desarrollador del Centro de IoT][IoT Hub Developer Guide - C2D] para más información sobre las diferencias entre la compatibilidad con AMQP y HTTP/1, y la limitación del Centro de IoT.

## Envío de un mensaje de nube a dispositivo desde el back-end de la aplicación

En esta sección, creará una aplicación de consola de Node.js que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado. Necesitará el identificador del dispositivo que agregó en el tutorial de [introducción al Centro de IoT] y la cadena de conexión de su centro de IoT que encontrará en el [Portal de Azure].

1. Cree una nueva carpeta vacía denominada **sendcloudtodevicemessage**. En la carpeta **sendcloudtodevicemessage**, cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema en la carpeta **sendcloudtodevicemessage**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Con un editor de texto, cree un nuevo archivo **SendCloudToDeviceMessage.js** en la carpeta **sendcloudtodevicemessage**.

4. Agregue las siguientes instrucciones `require` al principio del archivo **SendCloudToDeviceMessage.js**:

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Agregue el código siguiente al archivo **SendCloudToDeviceMessage.js** y reemplace el valor del marcador de posición de la cadena de conexión por la cadena de conexión para el Centro de IoT que creó en el tutorial [Introducción al Centro de IoT] y el marcador de posición del dispositivo de destino por el identificador de dispositivo del dispositivo que agregó en el tutorial [Introducción al Centro de IoT] tutorial:

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

7. Guarde y cierre el archivo **SendCloudToDeviceMessage.js**.

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema de la carpeta **simulateddevice**, ejecute el comando siguiente para enviar la telemetría al Centro de IoT y escuchar mensajes de nube al dispositivo:

    ```
    node SimulatedDevice.js 
    ```

    ![][img-simulated-device]

2. En un símbolo del sistema de la carpeta **sendcloudtodevicemessage**, ejecute el comando siguiente para enviar un mensaje de nube al dispositivo y espere el comentario de confirmación:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![][img-send-command]

    > [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

## Pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. Puede seguir explorando las características del Centro de IoT y los distintos escenarios con los tutoriales siguientes:

- [Procesamiento de mensajes de dispositivo a la nube] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Carga de archivos desde dispositivos], describe un patrón que usa los mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles]
* [Centro para desarrolladores de Azure]

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]: media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introducción al Centro de IoT]: iot-hub-node-node-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-node-node-getstarted.md
[Introducción al Centro de IoT de Azure para Node.js]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Procesamiento de mensajes de dispositivo a la nube]: iot-hub-csharp-csharp-process-d2c.md
[Carga de archivos desde dispositivos]: iot-hub-csharp-csharp-file-upload.md
[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[Lenguajes y plataformas de dispositivos compatibles]: iot-hub-supported-devices.md
[Centro para desarrolladores de Azure]: http://www.azure.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->