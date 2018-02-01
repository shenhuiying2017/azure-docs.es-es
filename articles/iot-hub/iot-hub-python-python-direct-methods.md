---
title: "Métodos directos de IoT Hub de Azure (Python) | Microsoft Docs"
description: "Describe cómo usar los métodos directos de IoT Hub de Azure. Usará los SDK de IoT de Azure para Python con el fin de implementar una aplicación de dispositivo simulado que incluye un método directo y un servicio que invoque el método directo."
services: iot-hub
documentationcenter: 
author: msebolt
manager: timlt
editor: 
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: 9dac7b45894c2da0dcd32e456c8806faadf814e9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="use-direct-methods-on-your-iot-device-with-python"></a>Uso de métodos directos en el dispositivo IoT con Python
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Al final de este tutorial tendrá dos aplicaciones de consola de Python:

* **CallMethodOnDevice.py**, que llama a un método de la aplicación de dispositivo simulado y muestra la respuesta.
* **SimulatedDevice.py**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente y responde al método llamado desde la nube.

> [!NOTE]
> En el artículo [Azure Iot SDKs][lnk-hub-sdks] (SDK de IoT de Azure) se proporciona información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución.
> 

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Python que responda a un método que llama desde la nube.

1. Con un editor de texto, cree un nuevo archivo **SimulatedDevice.py**.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **SimulatedDevice.py**:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    
    WAIT_COUNT = 5
    METHOD_CONTEXT = 0
    METHOD_CALLBACKS = 0

    # chose MQTT or MQTT_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "<deviceConnectionString>"
    ```

1. Agregue las siguientes funciones para implementar la devolución de llamada del método del dispositivo y el método directo en el dispositivo:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "DeviceMethod":
            onDeviceMethod()
        
        print ( "\nMethod callback called with:\nmethodName = %s\npayload = %s\ncontext = %s" % (method_name, payload, user_context) )
        METHOD_CALLBACKS += 1
    
        print ( "Total calls confirmed: %d\n" % METHOD_CALLBACKS )
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value

    def onDeviceMethod():
        print ( "Direct method called." )
    ```

1. Agregue la función siguiente para abrir la conexión a su IoT Hub e inicializar la escucha del método directo:
   
    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

        return client
    ```

1. Agregue las funciones principales:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for direct method call, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python direct methods sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Guarde y cierre el archivo **SimulatedDevice.py**.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintento (por ejemplo, reintento de conexión), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Control de errores temporales).
> 


## <a name="call-a-method-on-a-device"></a>Llamada a un método de un dispositivo
En esta sección, creará una aplicación de consola de Python que llame a un método en la aplicación de dispositivo simulado y muestre la respuesta.


1. Con un editor de texto, cree un nuevo archivo **CallMethodOnDevice.py**.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **CallMethodOnDevice.py**:
   
    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "<IoTHubConnectionString>"
    DEVICE_ID = "<deviceId>"

    METHOD_NAME = "DeviceMethod"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    ```

1. Agregue las siguientes funciones para implementar la devolución de llamada del método del dispositivo y el método directo en el dispositivo:
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Device Method called" )
            print ( "Device Method name       : {0}".format(METHOD_NAME) )
            print ( "Device Method payload    : {0}".format(METHOD_PAYLOAD) )
            print ( "" )
            print ( "Response status          : {0}".format(response.status) )
            print ( "Response payload         : {0}".format(response.payload) )

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )
    ```

1. Agregue la función principal:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceMethod Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. Guarde y cierre el archivo **CallMethodOnDevice.py**.

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, ejecute el siguiente comando para empezar la escucha de llamadas de método desde IoT Hub:
   
    ```cmd/sh
    python SimulatedDevice.py
    ```
   
    ![][7]

1. En un símbolo del sistema, ejecute el siguiente comando para empezar a supervisar IoT Hub:
   
    ```cmd/sh
    python CallMethodOnDevice.py
    ```
   
    ![][8]

1. Verá cómo el dispositivo reacciona al método imprimiendo el mensaje y cómo la aplicación que llamó al método muestra la respuesta del dispositivo:
   
    ![][9]

## <a name="next-steps"></a>pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para que la aplicación del dispositivo simulado reaccionara a los métodos que se invoquen desde la nube. También creó una aplicación que invoca métodos en el dispositivo y muestra la respuesta del dispositivo. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Introducción a IoT Hub]
* [Programación de trabajos en varios dispositivos][lnk-devguide-jobs]

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-python-python-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-python-python-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-python-python-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introducción a IoT Hub]: iot-hub-node-node-getstarted.md
