---
title: Uso de las propiedades de dispositivos gemelos de Azure IoT Hub (Python) | Microsoft Docs
description: "En este artículo se describe cómo usar los dispositivos gemelos de IoT Hub de Azure para configurar dispositivos. Usará el SDK de Azure IoT para Python para implementar la aplicación de dispositivo simulado y una aplicación de servicio que modifica una configuración de dispositivo mediante un dispositivo gemelo."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo
ms.openlocfilehash: d0d5a30a76068eb3212124fd14e7ea1616b75708
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Uso de las propiedades deseadas para configurar dispositivos (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Al final de este tutorial, tendrá dos aplicaciones de consola de Python:

* **SimulateDeviceConfiguration.py**, una aplicación de dispositivo simulado que espera una actualización de la configuración deseada y notifica el estado de un proceso de actualización de configuración simulada.
* **SetDesiredConfigurationAndQuery.py**, una aplicación Python diseñada para ejecutarse desde el back-end, que establece la configuración deseada en un dispositivo y consulta el proceso de actualización de la configuración.

> [!NOTE]
> En el artículo [SDK de IoT de Azure][lnk-hub-sdks] se proporciona información sobre los SDK que puede usar para crear aplicaciones de dispositivo y de back-end.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

Si ha seguido el tutorial [Introducción a los dispositivos gemelos][lnk-twin-tutorial], ya tiene un centro de IoT y una identidad de dispositivo denominada **myDeviceId**; y puede ir directamente a la sección [Creación de la aplicación de dispositivo simulado][lnk-how-to-configure-createapp] .

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Creación de la aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Python que se conecta a su central como **myDeviceId**, espera una actualización de la configuración deseada e informa de las actualizaciones en el proceso de actualización de la configuración simulada.

1. Instale el **SDK de dispositivo Python de Azure IoT** con el comando siguiente en el símbolo del sistema:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Con un editor de texto, cree un archivo **SimulateDeviceConfiguration.py** nuevo.

1. Agregue el código siguiente al archivo **SimulateDeviceConfiguration.py** y sustituya el marcador de posición **{device connection string}** por la cadena de conexión del dispositivo que copió al crear la identidad de dispositivo **myDeviceId**:

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    El objeto **CLIENT** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. Con más código, adjuntará un controlador para la actualización de las propiedades deseadas, luego agregará un controlador adicional para comprobar que hay una solicitud de cambio de la configuración real mediante la comparación de los valores de configId, lo que luego invocará un método que inicie el cambio en la configuración. Por simplicidad, el código anterior usa un valor predeterminado codificado de forma rígida para la configuración inicial. Una aplicación real probablemente cargaría esa configuración desde un almacenamiento local.
   
   > [!IMPORTANT]
   > Los eventos de cambio de propiedad deseados siempre se emiten una vez en la conexión del dispositivo, no olvide comprobar que hay un cambio real en las propiedades deseadas antes de realizar cualquier acción.
   > 

1. Agregue el código siguiente al final del archivo **SimulateDeviceConfiguration.py**:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    El método **device_twin_callback** actualiza las propiedades notificadas del objeto del dispositivo gemelo local con la solicitud de actualización de configuración y establece el valor de _configId_. Después de actualizar correctamente el dispositivo gemelo, imprime un mensaje de actualización. Para ahorrar ancho de banda, las propiedades notificadas se actualizan especificando solo las propiedades que se van a modificar (denominadas **TWIN_PAYLOAD** en el código anterior), en lugar de reemplazar el documento completo.
   
   > [!NOTE]
   > Este tutorial no simula ningún comportamiento de las actualizaciones de configuración simultáneas. Algunos procesos de actualización de configuración pueden dar cabida a cambios de configuración de destino mientras se está ejecutando la actualización, otros tienen que ponerlos en cola, y otros podrían rechazarlos con una condición de error. Asegúrese de que considera el comportamiento deseado para el proceso de configuración específica y agregue la lógica adecuada antes de iniciar el cambio de configuración.
   > 

1. Agregue el código siguiente al final del archivo **SimulateDeviceConfiguration.py**: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

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
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Ejecute la aplicación del dispositivo:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Verá el mensaje `Device twins updated.`. Mantenga la aplicación en ejecución.


## <a name="create-the-service-app"></a>Creación de la aplicación de servicio
En esta sección, creará una aplicación de consola Python que actualiza las *propiedades deseadas* en el dispositivo gemelo asociado con **myDeviceId** con un nuevo objeto de configuración de telemetría. A continuación, consulta los dispositivos gemelos almacenados en IoT Hub y muestra la diferencia entre las configuraciones deseada y notificada del dispositivo.

1. Instale el **SDK de servicio de Python de Azure IoT** con el comando siguiente en el símbolo del sistema:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Con un editor de texto, cree un archivo **SetDesiredAndQuery.py** nuevo.

1. Agregue el código siguiente al archivo **SetDesiredAndQuery.py** y sustituya el marcador de posición **{IoTHubConnectionString}** por la cadena de conexión de IoT Hub que copió cuando creó el centro y el marcador de posición **{deviceId}** por el nombre del dispositivo:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Agregue el código siguiente al final del archivo **SetDesiredAndQuery.py**:

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. Con **SimulateDeviceConfiguration.py** en ejecución, ejecute la aplicación en un símbolo del sistema nuevo con:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Debería ver el cambio del identificador de la configuración notificada de **1** a **2** con la nueva frecuencia de envío activa de cinco minutos en lugar de 24 horas.


## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha establecido una configuración deseada como *propiedades deseadas* desde una aplicación back-end, y ha escrito una aplicación de dispositivo simulado para detectar ese cambio y simular un proceso de actualización que informe de su estado como *propiedades notificadas* para el dispositivo gemelo.

Use los siguientes recursos para obtener información sobre cómo:

* Enviar telemetría desde dispositivos con el tutorial [Introducción a IoT Hub][lnk-iothub-getstarted].
* Programar o realizar operaciones en grandes conjuntos de dispositivos (consulte el tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] [Programación y difusión de trabajos]).
* Controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial [Uso de métodos directos][lnk-methods-tutorial].

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
