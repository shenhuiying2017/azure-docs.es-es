---
title: Enrutamiento de mensajes con Azure IoT Hub (Node) | Microsoft Docs
description: "Cómo procesar mensajes de dispositivo a nube de Azure IoT Hub mediante reglas de enrutamiento y puntos de conexión personalizados para enviar mensajes a otros servicios de back-end."
services: iot-hub
documentationcenter: node
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo
ms.openlocfilehash: e5d57e087e5f4dc1e0abf112001218aa7390a4f7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Enrutamiento de mensajes con IoT Hub (Node)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial se basa en el tutorial [Introducción a Azure IoT Hub para .NET].  Este tutorial:

* Muestra cómo usar reglas de enrutamiento para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración.
* Ilustra cómo detectar mensajes interactivos que requieren acción inmediata en el back-end de soluciones para su procesamiento posterior.  Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM.  Por el contrario, los mensajes de punto de datos, como los datos de telemetría de temperatura, se envían a un motor de análisis.

Al final de este tutorial ejecutará tres aplicaciones de consola de Node.js:

* **SimulatedDevice.js**, una versión modificada de la aplicación creada en el tutorial de [Introducción a Azure IoT Hub para .NET], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos a intervalos aleatorios. Esta aplicación usa el protocolo MQTT para comunicarse con IoT Hub.
* **ReadDeviceToCloudMessages.js**, que muestra los datos de telemetría enviados por la aplicación de dispositivo.
* **ReadCriticalQueue.js** quita de la cola los mensajes críticos de la cola de la instancia de Service Bus adjunta a IoT Hub.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para instrucciones sobre cómo reemplazar el dispositivo de este tutorial con un dispositivo físico y sobre cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesitará lo siguiente:

* Una versión funcional y completa del tutorial [Introducción a Azure IoT Hub para .NET] .
* Node.js, versión 4.0.x o posterior.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

También se recomienda leer sobre [Azure Storage] y [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Envío de mensajes interactivos desde una aplicación de dispositivo
En esta sección, se modifica la aplicación de dispositivo que creó en el tutorial [Introducción a Azure IoT Hub para .NET] a fin de enviar ocasionalmente mensajes que requieren un procesamiento inmediato.

1. Utilice un editor de texto para abrir el archivo **simulateddevice\SimulatedDevice.js**. Este archivo contiene el código para la aplicación **SimulatedDevice** que creó en el tutorial [Introducción a Azure IoT Hub para .NET].

2. Reemplace la función **connectCallback** por el código siguiente:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Con este método se agrega aleatoriamente la propiedad `"level": "critical"` y `"level": "storage"` a los mensajes que envía el dispositivo, lo que simula un mensaje que requiere una acción inmediata del back-end de aplicaciones o uno que necesita almacenarse permanentemente. La aplicación admite el enrutamiento de mensajes basado en el cuerpo del mensaje.
   
   > [!NOTE]
   > Puede usar propiedades de mensaje a fin de enrutar mensajes en diferentes escenarios, como el procesamiento en frío, además del ejemplo de procesamiento en caliente que se muestra aquí.

2. Guarde y cierre el archivo **simulateddevice\SimulatedDevice.js**.

    > [!NOTE]
    > Es muy recomendable implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adición de una cola de Service Bus a su IoT Hub y enrutamiento de mensajes a ella

En esta sección, se crea una cola de Service Bus, se conecta con el IoT Hub y se configura el IoT Hub para enviar mensajes a la cola en función de la presencia de una propiedad en el mensaje. Para obtener más información acerca de cómo procesar los mensajes desde las colas de Service Bus, consulte [Introducción a las colas][lnk-sb-queues-node].

1. Cree una cola de Service Bus como se describe en [Introducción a las colas][lnk-sb-queues-node]. Tome nota del espacio de nombres y del nombre de la cola.

2. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión**.

    ![Puntos de conexión en IoT Hub][30]

3. En la hoja **Puntos de conexión**, haga clic en **Agregar** en la parte superior para agregar la cola al centro de IoT. Ponga al punto de conexión el nombre **CriticalQueue** y use las listas desplegables para seleccionar **Cola de Service Bus**, el espacio de nombres de Service Bus en el que reside la cola y el nombre de la cola. Cuando haya terminado, haga clic en **Aceptar** en la parte inferior.  

    ![Adición de un punto de conexión][31]

4. Ahora haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **Mensajes del dispositivo** como origen de los datos. Escriba `level="critical"` como condición y elija **CriticalQueue** como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Haga clic en **Guardar** en la parte inferior.  

    ![Adición de una ruta][32]

    Asegúrese de que la ruta de reserva se establece en **ON** (Activado). Esta es la configuración predeterminada del centro de IoT.

    ![Ruta de reserva][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Lectura desde el punto de conexión de la cola

En esta sección, creará una aplicación de consola de Node.js que lee los mensajes críticos de IoT Service Bus. Consulte más información en [Introducción a las colas][lnk-sb-queues-node]. 

1. Cree una carpeta vacía denominada `readcriticalqueue`. En la carpeta `readcriticalqueue`, cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```cmd/sh
    npm init
    ```

2. En el símbolo del sistema, en la carpeta `readcriticalqueue`, ejecute el siguiente comando para instalar el paquete **azure**:

    ```cmd/sh
    npm install azure --save
    ```

3. Con un editor de texto, cree un archivo **ReadCriticalQueue.js** en la carpeta `readcriticalqueue`.

4. Agregue las siguientes instrucciones `require` al principio del archivo **ReadCriticalQueue.js**:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Agregue la siguiente declaración de variable y sustituya los valores de marcador de posición por la cadena de conexión y el nombre de la cola de IoT Service Bus:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Agregue las siguientes dos funciones que imprimen la salida en la consola:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Guarde y cierre el archivo **ReadCriticalQueue.js**.

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las tres aplicaciones.

1. Para ejecutar la aplicación **ReadDeviceToCloudMessages.js**, en un símbolo del sistema o en el shell, vaya a la carpeta readdevicetocloudmessages y ejecute el siguiente comando:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Ejecución de read-d2c-messages][readd2c]

2. Para ejecutar la aplicación **ReadCriticalQueue.js**, en un símbolo del sistema o en el shell, vaya a la carpeta readcriticalqueue y ejecute el siguiente comando:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Ejecución de read-critical-messages][readqueue]

3. Para ejecutar la aplicación **SimulatedDevice.js**, en un símbolo del sistema o en el shell, vaya a la carpeta simulateddevice y ejecute el siguiente comando:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Ejecución de simulated-device][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adición de un contenedor de almacenamiento a IoT Hub y enrutamiento de mensajes a este

En esta sección, se crea una cuenta de Storage, se conecta con el IoT Hub y se configura este para enviar mensajes a la cuenta en función de la presencia de una propiedad en el mensaje. Para más información acerca de cómo administrar el almacenamiento, consulte [Introducción a Azure Storage][Azure Storage].

 > [!NOTE]
   > Si no está limitado a un **punto de conexión**, puede configurar **StorageContainer**, además de **CriticalQueue**, y ejecutar ambos simultáneamente.

1. Cree una cuenta de Storage como se describe en la [documentación de Azure Storage][lnk-storage]. Tome nota del nombre de la cuenta.

2. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión**.

3. En la hoja **Puntos de conexión**, seleccione el punto de conexión **CriticalQueue** y haga clic en **Eliminar**. Haga clic en **Sí** y, luego, en **Agregar**. Asigne el nombre **StorageContainer** al punto de conexión y use los menús desplegables para seleccionar **Contenedor de Azure Storage** y crear una **cuenta de Storage** y un **contenedor de Storage**.  Tome nota de los nombres.  Cuando haya terminado, haga clic en **Aceptar** en la parte inferior. 

 > [!NOTE]
   > Si no está limitado a un **punto de conexión**, no es necesario eliminar **CriticalQueue**.

4. Haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **Mensajes del dispositivo** como origen de los datos. Escriba `level="storage"` como condición y elija **StorageContainer** como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Haga clic en **Guardar** en la parte inferior.  

    Asegúrese de que la ruta de reserva se establece en **Activado**. Esta es la configuración predeterminada del centro de IoT.

1. Asegúrese de que la aplicación anterior **SimulatedDevice.js** todavía se está ejecutando. 

1. En Azure Portal, vaya a la cuenta de almacenamiento en **Blob Service**, haga clic en **Examinar blobs...**.  Seleccione el contenedor, vaya al archivo JSON y haga clic en él. Después, haga clic en **Descargar** para ver los datos.

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido a enviar de manera confiable mensajes del dispositivo a la nube mediante la funcionalidad de enrutamiento de mensajes de IoT Hub.

El tutorial sobre [cómo enviar mensajes de dispositivo a la nube con IoT Hub][lnk-c2d] muestra cómo enviar mensajes a los dispositivos desde la solución de back-end.

Para ver ejemplos de soluciones completas de un extremo a otro que usan IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

Para obtener más información sobre el enrutamiento de mensajes en IoT Hub, consulte [Envío y recepción de mensajes con IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introducción a Azure IoT Hub para .NET]: iot-hub-node-node-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[Control de errores transitorios]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/