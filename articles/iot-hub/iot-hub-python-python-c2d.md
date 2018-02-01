---
title: Mensajes de nube a dispositivo con IoT Hub de Azure (Python) | Microsoft Docs
description: "Cómo enviar mensajes de nube a un dispositivo de una instancia de IoT Hub de Azure mediante los SDK de IoT de Azure para Python. Modifique una aplicación de dispositivo simulado para recibir mensajes de nube a dispositivo y cambie una aplicación de back-end para enviar los mensajes de nube a dispositivo."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 592e0cd858d16715f95955194eca4217d9914b05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envío de mensajes de nube a dispositivo con IoT Hub (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Introducción
IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end. El tutorial [Introducción a Iot Hub] muestra cómo crear un centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar una aplicación de dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en la [Introducción a Iot Hub]. En él se muestra cómo realizar las siguientes acciones:

* Desde el back-end de la nube de la aplicación, envíe mensajes de nube a dispositivo en un único dispositivo a través de IoT Hub.
* Reciba mensajes de nube a dispositivo en un dispositivo.
* Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde IoT Hub.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores de IoT Hub][IoT Hub developer guide - C2D].

Al final de este tutorial tendrá dos aplicaciones de consola de Python:

* **SimulatedDevice.py**, versión modificada de la aplicación creada en [Introducción a Iot Hub], que se conecta a IoT Hub y recibe mensajes de nube a dispositivo.
* **SendCloudToDeviceMessage.py**, que envía un mensaje de la nube a la aplicación de dispositivo simulado mediante IoT Hub y, luego, recibe su confirmación de entrega.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure]para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.
> 

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

> [!NOTE]
> Los paquetes *pip* de `azure-iothub-service-client` y `azure-iothub-device-client` actualmente solo están disponibles para el sistema operativo Windows. Para Linux o Mac OS, consulte las secciones específicas del sistema operativo Mac y Linux en la publicación sobre cómo [preparar el entorno de desarrollo para Python][lnk-python-devbox].
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Recepción de mensajes en la aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Python para simular el dispositivo y recibir mensajes de la nube al dispositivo desde IoT Hub.

1. Con un editor de texto, cree un archivo **SimulatedDevice.py**.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **SimulatedDevice.py**:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Agregue el código siguiente al archivo **SimulatedDevice.py**. Sustituya el valor de marcador de posición "{deviceConnectionString}" por la cadena de conexión del dispositivo para el dispositivo que creó en el tutorial [Introducción a Iot Hub]:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Agregue la función siguiente para imprimir los mensajes recibidos en la consola:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Agregue el código siguiente para inicializar el cliente y espere a recibir el mensaje de la nube al dispositivo:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Agregue la siguiente función main:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Guarde y cierre el archivo **SimulatedDevice.py**.


## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo
En esta sección, escribirá una aplicación de consola de Python que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado. Necesita el identificador de dispositivo que agregó en el tutorial de [Introducción a Iot Hub]. También necesita la cadena de conexión para IoT Hub que encontrará en [Azure Portal].

1. Con un editor de texto, cree un archivo **SendCloudToDeviceMessage.py**.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **SendCloudToDeviceMessage.py**:
   
    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Agregue el código siguiente al archivo **SendCloudToDeviceMessage.py**. Sustituya el valor de marcador de posición "{IoTHubConnectionString}" por la cadena de conexión de IoT Hub que creó en el tutorial [Introducción a Iot Hub]. Sustituya el marcador de posición "{deviceId}" por el identificador del dispositivo que agregó en el tutorial [Introducción a Iot Hub]:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Agregue la función siguiente para imprimir los resultados de la operación en la consola:
   
    ```python
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

1. Agregue la función siguiente para imprimir mensajes de comentarios en la consola:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Agregue el código siguiente para enviar un mensaje a su dispositivo y procesar el mensaje de comentarios cuando el dispositivo reconozca el mensaje de la nube al dispositivo:
   
    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

1. Agregue la siguiente función main:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Guarde y cierre el archivo **SendCloudToDeviceMessage.py**.


## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. Abra un símbolo del sistema e instale el **SDK de dispositivo de Azure IoT Hub para Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. En el símbolo del sistema, ejecute el comando siguiente para escuchar mensajes de nube a dispositivo:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Ejecución de una aplicación de dispositivo simulada][img-simulated-device]

1. Abra un nuevo símbolo del sistema e instale el **SDK del servicio Azure IoT Hub para Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. En un símbolo del sistema, ejecute el comando siguiente para enviar un mensaje de nube a dispositivo y espere el comentario del mensaje:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Ejecución de la aplicación para enviar el comando de nube a dispositivo][img-send-command]
   
1. Anote el mensaje que recibirá en el dispositivo.

    ![Mensaje recibido][img-message-recieved]


## <a name="next-steps"></a>pasos siguientes
En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. 

Para ver ejemplos de soluciones completas de un extremo a otro que usen IoT Hub, consulte [Documentación de Azure IoT Suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Introducción a Iot Hub]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Documentación de Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
