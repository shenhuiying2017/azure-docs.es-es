---
title: Introducción al Centro de IoT de Azure para Node.js | Microsoft Docs
description: Tutorial de introducción al Centro de IoT de Azure con Node.js Utilice Centro de IoT de Azure y Node.js con los SDK de IoT de Microsoft Azure para implementar una solución de Internet de las cosas.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2016
ms.author: dobett

---
# <a name="get-started-with-azure-iot-hub-for-node.js"></a>Introducción al Centro de IoT de Azure para Node.js
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de Node.js:

* **CreateDeviceIdentity.js**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **SimulatedDevice.js**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQPS.

> [!NOTE]
> El artículo [IoT Hub SDKs][lnk-hub-sdks] (SDK de IoT Hub) proporciona información acerca de los SDK que se pueden usar para crear tanto las aplicaciones que se ejecuten en los dispositivos como el back-end de la solución.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Node.js versión 0.10.x, o posteriores.
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Cree su cuenta gratuita de Azure hoy mismo][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ahora ha creado su Centro de IoT. Ha creado el nombre de host y la cadena de conexión del Centro de IoT que necesita para completar el resto del tutorial.

## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, creará una aplicación de consola de Node.js que crea una identidad de dispositivo en el registro de identidades de IoT Hub. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Para más información, consulte la sección **Device identity registry operations** (Operaciones del Registro de identidad del dispositivo) de [Manage device identities in IoT Hub](Administración de identidades de dispositivos en IoT Hub.md)[lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. Cree una nueva carpeta vacía denominada **createdeviceidentity**. En la carpeta **createdeviceidentity** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema en la carpeta **createdeviceidentity**, ejecute el siguiente comando para instalar el paquete del SDK del servicio **azure-iothub**:
   
    ```
    npm install azure-iothub --save
    ```
3. Con un editor de texto, cree un archivo **CreateDeviceIdentity.js** en la carpeta **createdeviceidentity**.
4. Agregue la siguiente instrucción `require` al principio del archivo **CreateDeviceIdentity.js** :
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. Agregue el siguiente código al archivo **CreateDeviceIdentity.js** y sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección anterior: 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. Agregue el siguiente código para crear una definición de dispositivo en el registro de identidades de dispositivo en IoT Hub. Este código crea un dispositivo si el identificador de dispositivo no existe en el registro o, de lo contrario, devuelve la clave del dispositivo existente:
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```
7. Guarde y cierre el archivo **CreateDeviceIdentity.js** .
8. Para ejecutar la aplicación **createdeviceidentity** , ejecute el siguiente comando en el símbolo del sistema en la carpeta createdeviceidentity:
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. Anote el **identificador del dispositivo** y la **clave del dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a IoT Hub como un dispositivo.

> [!NOTE]
> El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Para más información, consulte [Manage device identities in IoT Hub][lnk-devguide-identity] (Administración de identidades de dispositivos en IoT Hub).
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Recepción de mensajes de dispositivo a nube
En esta sección, creará una aplicación de consola de Node.js que lee los mensajes de dispositivo a nube de IoT Hub. Un IoT Hub expone un punto de conexión compatible con [Event Hubs][lnk-event-hubs-overview]que permite leer los mensajes del dispositivo a la nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. En [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub mediante .Net][lnk-process-d2c-tutorial], se muestra cómo procesar mensajes de dispositivo a la nube a escala. En el tutorial [Introducción a Event Hubs][lnk-eventhubs-tutorial] se proporciona más información acerca de cómo procesar los mensajes desde Event Hubs. Dicha información se puede aplicar a los puntos de conexión compatibles con Event Hubs de IoT Hub.

> [!NOTE]
> El punto de conexión compatible con los Centros de eventos para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQPS.
> 
> 

1. Cree una nueva carpeta vacía denominada **readdevicetocloudmessages**. En la carpeta **readdevicetocloudmessages** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **readdevicetocloudmessages**, ejecute el siguiente comando para instalar el paquete **azure-event-hubs**:
   
    ```
    npm install azure-event-hubs --save
    ```
3. Con un editor de texto, cree un archivo **ReadDeviceToCloudMessages.js** en la carpeta **readdevicetocloudmessages**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **ReadDeviceToCloudMessages.js** :
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. Agregue la siguiente declaración de variable y sustituya el valor de marcador de posición por la cadena de conexión para su Centro de IoT:
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. Agregue las siguientes dos funciones que imprimen la salida en la consola:
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. Agregue el siguiente código para crear **EventHubClient**, abra la conexión a IoT Hub y cree un receptor para cada partición. Esta aplicación utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados al Centro de IoT después de que el receptor comience a ejecutarse. Este filtro es útil en un entorno de prueba, porque puede ver solo el conjunto actual de mensajes. En un entorno de producción, el código debe asegurarse de que se procesan todos los mensajes (para más información, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-process-d2c-tutorial]).
   
    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. Guarde y cierre el archivo **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Node.js que simula un dispositivo que envía mensajes de dispositivo a nube a IoT Hub.

1. Cree una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:
   
    ```
    npm init
    ```
2. En el símbolo del sistema, en la carpeta **simulateddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-amqp**:
   
    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```
3. Con un editor de texto, cree un nuevo archivo **SimulatedDevice.js** en la carpeta **simulateddevice**.
4. Agregue las siguientes instrucciones `require` al principio del archivo **SimulatedDevice.js** :
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo. Reemplace **{nombreDeHostDeIoT}** por el nombre del centro de IoT que creó en la sección *Creación de un centro de IoT* . Reemplace **{nombreDeClaveDeDispositivo}** por el valor de la clave del dispositivo que ha generado en la sección *Creación de una identidad de dispositivo* :
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. Agregue la siguiente función para mostrar la salida de la aplicación:
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Cree una devolución de llamada y utilice la función **setInterval** para enviar un nuevo mensaje a su Centro de IoT cada segundo:
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
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
8. Abra la conexión al su Centro de IoT y empiece a enviar mensajes:
   
    ```
    client.open(connectCallback);
    ```
9. Guarde el archivo **SimulatedDevice.js** y ciérrelo.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, debe implementar directivas de reintentos (por ejemplo, retroceso exponencial), como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults](Control de errores transitorios).
> 
> 

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta **readdevicetocloudmessages** , ejecute el siguiente comando para empezar la supervisión del Centro de IoT:
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![Aplicación cliente de servicio de IoT Hub de Node.js para supervisar mensajes de dispositivo a nube][7]
2. En un símbolo del sistema, en la carpeta **simulateddevice** , ejecute el siguiente comando para empezar el envío de datos de telemetría al Centro de IoT:
   
    ```
    node SimulatedDevice.js
    ```
   
    ![Aplicación cliente de dispositivo de IoT Hub de Node.js para enviar mensajes de dispositivo a nube][8]
3. El icono **Uso** de [Azure Portal][lnk-portal] muestra el número de mensajes enviados al centro:
   
    ![Icono Uso de Azure Portal que muestra el número de mensajes enviados a IoT Hub][43]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo simulado para enviar al centro los mensajes de dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Conectar el dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos de Centro de IoT de Azure con C# (versión preliminar)][lnk-device-management]
* [SDK de puerta de enlace de IoT (beta): introducción usando Linux][lnk-gateway-SDK]

Para aprender a ampliar una solución IoT y a procesar los mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial].

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/



<!--HONumber=Oct16_HO2-->


