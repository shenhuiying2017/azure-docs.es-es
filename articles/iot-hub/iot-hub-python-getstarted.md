---
title: "Introducción a Azure IoT Hub (Python) | Microsoft Docs"
description: "Obtenga información sobre cómo enviar mensajes del dispositivo a la nube a una instancia de Azure IoT Hub mediante los SDK de IoT para Python. Cree aplicaciones de servicio y de dispositivo simuladas para registrar el dispositivo, enviar mensajes y leerlos en IoT Hub."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: acc237afc5c7eccbf2caf876973c147eb8574d7b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Conexión de un dispositivo simulado a IoT Hub mediante Python
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá dos aplicaciones de Python:

* **CreateDeviceIdentity.py**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar la aplicación de dispositivo simulado.
* **SimulatedDevice.py**, que se conecta a IoT Hub con la identidad de dispositivo creada anteriormente y envía un mensaje de telemetría mediante el protocolo MQTT periódicamente.

> [!NOTE]
> En el artículo [Azure Iot SDKs][lnk-hub-sdks] (SDK de IoT de Azure) se proporciona información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* [Node.js 4.0 o posterior][lnk-node-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Esto es necesario para instalar la [herramienta Explorador de IoT Hub][lnk-iot-hub-explorer].
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.

> [!NOTE]
> Los paquetes *pip* de `azure-iothub-service-client` y `azure-iothub-device-client` actualmente solo están disponibles para el sistema operativo Windows. Para Linux o Mac OS, consulte las secciones específicas del sistema operativo Mac y Linux en la publicación sobre cómo [preparar el entorno de desarrollo para Python][lnk-python-devbox].
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ahora ha creado su Centro de IoT. Use el nombre de host y la cadena de conexión de IoT Hub en el resto del tutorial.

> [!NOTE]
> Puede también crear fácilmente una instancia de IoT Hub en una línea de comandos, mediante la CLI de Azure basada en Python o Node.js. En el artículo [Creación de una instancia de IoT Hub mediante la CLI de Azure 2.0][lnk-azure-cli-hub] se muestran los pasos rápidos para hacerlo. 
> 

## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección se enumeran los pasos para crear una aplicación de consola de Python que crea una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. Solo se puede conectar un dispositivo a IoT Hub si tiene una entrada en el registro de identidades. Para más información, consulte la sección sobre el **registro de la identidad** de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía a IoT Hub mensajes de dispositivo a la nube.

1. Abra un símbolo del sistema e instale el **SDK del servicio Azure IoT Hub para Python**. Cierre el símbolo del sistema después de instalar el SDK.

    ```
    pip install azure-iothub-service-client
    ```

2. Cree un archivo de Python denominado **CreateDeviceIdentity.py**. Ábralo en el[ editor/IDE de Python que prefiera][lnk-python-ide-list], por ejemplo, el predeterminado [IDLE][lnk-idle].

3. Agregue el código siguiente para importar los módulos necesarios desde el SDK del servicio:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Agregue el código siguiente; para ello, sustituya el marcador de posición por `[IoTHub Connection String]` con la cadena de conexión de la instancia de IoT Hub que creó en la sección anterior. Como `DEVICE_ID` puede usar cualquier nombre.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Agregue la siguiente función para imprimir parte de la información del dispositivo.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Agregue la siguiente función para crear la identificación del dispositivo mediante el Administrador del Registro. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Por último, agregue la función principal como se indica a continuación y guarde el archivo.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. En el símbolo del sistema, ejecute **CreateDeviceIdentity.py** como se indica a continuación:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Debería ver la creación del dispositivo simulado. Anote los valores de **deviceId** y **primaryKey** de este dispositivo. Los necesitará más adelante cuando cree una aplicación que se conecta a IoT Hub como un dispositivo.

    ![Dispositivo creado][1]

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro al centro de IoT. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.
> 
> 


## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección se enumeran los pasos para crear una aplicación de consola de Python que simula un dispositivo y que envía mensajes del dispositivo a la instancia de IoT Hub en la nube.

1. Abra un símbolo del sistema nuevo e instale el SDK del dispositivo Azure IoT Hub para Python. Tras la instalación, cierre el símbolo del sistema.

    ```
    pip install azure-iothub-device-client
    ```
2. Cree un archivo y llámelo **SimulatedDevice.py**. Ábralo en el editor/IDE de Python que prefiera (por ejemplo, IDLE).

3. Agregue el código siguiente para importar los módulos necesarios desde el SDK del dispositivo.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Agregue el código siguiente y sustituya el marcador de posición por `[IoTHub Device Connection String]` con la cadena de conexión del dispositivo. La cadena de conexión del dispositivo suele tener el formato `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Use los valores **deviceId** y **primaryKey** del dispositivo que creó en la sección anterior para reemplazar `<deviceId>` y `<primaryKey>` respectivamente. Reemplace `<hostName>` por el nombre de host de su instancia de IoT Hub, que normalmente aparece como `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Agregue el código siguiente para definir una devolución de llamada de confirmación de envío. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Agregue el código siguiente para inicializar al cliente de dispositivo.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. Agregue la siguiente función para dar formato y enviar un mensaje desde el dispositivo simulado a la instancia de IoT Hub.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Finalmente, agregue la función principal. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Guarde y cierre el archivo **SimulatedDevice.py**. Ya está preparado para ejecutar la aplicación.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Recepción de mensajes procedentes del dispositivo simulado
Para recibir mensajes de telemetría del dispositivo, debe usar un punto de conexión compatible con [Event Hubs][lnk-event-hubs-overview] expuesto en IoT Hub que lea los mensajes del dispositivo a la nube. Lea el tutorial de [Introducción a Event Hubs][lnk-eventhubs-tutorial], con información acerca de cómo procesar los mensajes desde Event Hubs en los puntos de conexión compatibles con Event Hubs de IoT Hub. Event Hubs aún no admite telemetría en Python, por lo que puede crear una aplicación de consola de [Node.js](iot-hub-node-node-getstarted.md#D2C_node) o [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) basada en Event Hubs para leer los mensajes del dispositivo a la nube desde IoT Hub. En este tutorial se muestra cómo puede utilizar la [herramienta Explorador de IoT Hub][lnk-iot-hub-explorer] para leer estos mensajes del dispositivo.

1. Abra un símbolo del sistema e instale el Explorador de IoT Hub. 

    ```
    npm install -g iothub-explorer
    ```

2. Ejecute el siguiente comando en el símbolo del sistema para iniciar la supervisión de los mensajes del dispositivo a la nube. Use la cadena de conexión de su instancia de IoT Hub del marcador de posición después de `--login`.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Abra un nuevo símbolo del sistema y desplácese al directorio que contiene el archivo **SimulatedDevice.py**.

4. Ejecute el archivo **SimulatedDevice.py** que envía periódicamente los datos de telemetría a la instancia de IoT Hub. 
   
    ```
    python SimulatedDevice.py
    ```
5. Observe los mensajes de dispositivo en el símbolo del sistema donde se ejecuta el Explorador de IoT Hub de la sección anterior. 

    ![Mensajes de Python del dispositivo a la nube][2]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró una nueva instancia de IoT Hub en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades de IoT Hub. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo simulado para enviar al centro de IoT los mensajes del dispositivo a la nube. Observó los mensajes que recibió IoT Hub con la ayuda de la herramienta Explorador de IoT Hub. 

Para explorar el SDK de Python para el uso intensivo de Azure IoT Hub, visite [este repositorio de Git Hub][lnk-python-github]. Para revisar las funcionalidades de mensajería del SDK del servicio Azure IoT Hub para Python, puede descargar y ejecutar [iothub_messaging_sample.py][lnk-messaging-sample]. Para la simulación del lado del dispositivo mediante el SDK del dispositivo Azure IoT Hub para Python, puede descargar y ejecutar [iothub_client_sample.py][lnk-client-sample].

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Conexión del dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos][lnk-device-management]
* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iot-edge]

Para aprender a ampliar su solución IoT y cómo procesar mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
