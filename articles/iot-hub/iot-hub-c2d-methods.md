<properties
 pageTitle="Uso de métodos directos | Microsoft Azure"
 description="En este tutorial se muestra cómo usar métodos directos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial-use-direct-methods"></a>Tutorial: Uso de métodos directos

## <a name="introduction"></a>Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. En tutoriales anteriores ([Introducción a Iot Hub] y [Envío de mensajes de nube a dispositivo con IoT Hub]) se muestra cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo de IoT Hub. IoT Hub también ofrece la capacidad de invocar métodos temporales en dispositivos desde la nube. Los métodos representan una interacción solicitud-respuesta con un dispositivo similar a una llamada HTTP en la cual se completan correctamente o generan un error de inmediato (tras un tiempo de espera que especifica el usuario) para informar al usuario sobre el estado de la llamada. En el artículo [Invoke a direct method on a device][lnk-devguide-methods] (Invocación de un método directo en un dispositivo) se describe con más detalle y se ofrece orientación sobre cuándo utilizar métodos o mensajes de la nube al dispositivo.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar Azure Portal para crear un centro de IoT y una identidad de dispositivo en este.
- Crear un dispositivo simulado con un método directo que se pueda llamar desde la nube.
- Crear una aplicación de consola que llame a un método directo del dispositivo simulado a través del centro de IoT.

> [AZURE.NOTE] En la actualidad, solo se puede acceder a los métodos directos desde dispositivos conectados a IoT Hub mediante el protocolo MQTT. Para instrucciones acerca de cómo convertir la aplicación de dispositivo existente para usar MQTT, consulte el artículo sobre [compatibilidad con MQTT][lnk-devguide-mqtt].

Al final de este tutorial tendrá dos aplicaciones de consola de Node.js:

* **CallMethodOnDevice.js**, que llama a un método del dispositivo simulado y muestra la respuesta.
* **SimulatedDevice.js**, que se conecta al centro de IoT con la identidad del dispositivo creada anteriormente y responde al método llamado desde la nube.

> [AZURE.NOTE] El artículo [IoT Hub SDKs][lnk-hub-sdks] (Los SDK de IoT Hub) proporciona información acerca de los SDK que puede usar para crear las aplicaciones que se ejecuten en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

+ Node.js versión 0.10.x o posteriores.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [prueba gratuita de Azure][lnk-free-trial]).

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Node.js que responda a un método que llama desde la nube.

1. Cree una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **simulateddevice**, ejecute el siguiente comando para instalar el paquete del SDK de dispositivo **azure-iot-device** y el paquete **azure-iot-device-mqtt**:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Con un editor de texto, cree un nuevo archivo **SimulatedDevice.js** en la carpeta **simulateddevice**.

4. Agregue las siguientes instrucciones `require` al principio del archivo **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo. Reemplace **{device connection string}** por la cadena conexión que generó en la sección sobre cómo *crear una identidad de dispositivo*:

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

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintento (por ejemplo, reintento de conexión), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Control de errores temporales).

## <a name="call-a-method-on-a-device"></a>Llamada a un método de un dispositivo

En esta sección, creará una aplicación de consola de Node.js que llame a un método del dispositivo simulado y muestre la respuesta.

1. Cree una nueva carpeta vacía denominada **callmethodondevice**. En la carpeta **callmethodondevice** , cree un archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **createdeviceidentity**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Con un editor de texto, cree un archivo **CallMethodOnDevice.js** en la carpeta **callmethodondevice**.

4. Agregue las siguientes instrucciones `require` al principio del archivo **CallMethodOnDevice.js**:

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Agregue la siguiente declaración de variable y sustituya el valor de marcador de posición por la cadena de conexión para su Centro de IoT:

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
        payload: 'a line to be written',
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

7. Guarde y cierre el archivo **CallMethodOnDevice.js**.

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta **simulateddevice** , ejecute el siguiente comando para empezar la escucha de llamadas de método desde IoT Hub:

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. En un símbolo del sistema, en la carpeta **callmethodondevice**, ejecute el siguiente comando para empezar la supervisión del centro de IoT:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Verá cómo el dispositivo reacciona al método imprimiendo el mensaje y cómo la aplicación que llamó al método muestra la respuesta del dispositivo:

    ![][9]
    
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Usó esta identidad de dispositivo para que la aplicación del dispositivo simulado reaccionara a los métodos que se invoquen desde la nube. También creó una aplicación que invoca métodos en el dispositivo y muestra la respuesta del dispositivo. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

- [Introducción a IoT Hub]
- [Schedule jobs on multiple devices][lnk-devguide-jobs] (Programación de trabajos en varios dispositivos)

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Schedule and broadcast jobs][lnk-tutorial-jobs] (Programación y difusión de trabajos).

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Procesamiento de mensajes de dispositivo a la nube]: iot-hub-csharp-csharp-process-d2c.md
[Introducción al Centro de IoT]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


