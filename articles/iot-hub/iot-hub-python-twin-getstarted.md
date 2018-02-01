---
title: "Introducción a los dispositivos gemelos de Azure IoT Hub (Python) | Microsoft Docs"
description: "Describe cómo usar dispositivos gemelos de IoT Hub de Azure para agregar etiquetas y, luego, usar una consulta de IoT Hub. El SDK de IoT de Azure para Python se usa para implementar la aplicación de dispositivo simulado y una aplicación de servicio que agrega las etiquetas y ejecuta la consulta de IoT Hub."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2017
ms.author: v-masebo
ms.openlocfilehash: 20c1eeee6ca690ddcf0b9489b88689213b79488e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="get-started-with-device-twins-python"></a>Introducción a los dispositivos gemelos (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Al final de este tutorial, tendrá dos aplicaciones de consola de Python:

* **AddTagsAndQuery.py**, una aplicación de Python de back-end diseñada para ejecutarse desde el back-end, que agrega etiquetas y consulta dispositivos gemelos.
* **ReportConnectivity.py**, una aplicación Python para dispositivo que simula un dispositivo que se conecta a su instancia de IoT Hub con la identidad del dispositivo creada anteriormente e informa de su estado de conectividad.

> [!NOTE]
> En el artículo [SDK de IoT de Azure][lnk-hub-sdks] se proporciona información sobre los SDK que puede usar para crear aplicaciones de dispositivo y de back-end.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

> [!NOTE]
> Los paquetes *pip* de `azure-iothub-service-client` y `azure-iothub-device-client` actualmente solo están disponibles para el sistema operativo Windows. Para Linux o Mac OS, consulte las secciones específicas del sistema operativo Mac y Linux en la publicación sobre cómo [preparar el entorno de desarrollo para Python][lnk-python-devbox].
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Creación de la aplicación de servicio
En esta sección, creará una aplicación de consola de Python que agrega metadatos de ubicación al dispositivo gemelo asociado con **{Device Id}**. Después, consulta los dispositivos gemelos almacenados en la instancia de IoT Hub mediante la selección de los dispositivos ubicados en Redmond (EE. UU.) y, después, los que informan de una conexión de red de telefonía móvil.

1. Abra un símbolo del sistema e instale el **SDK del servicio Azure IoT Hub para Python**. Cierre el símbolo del sistema después de instalar el SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Con un editor de texto, cree un nuevo archivo **AddTagsAndQuery.py**.

3. Agregue el código siguiente para importar los módulos necesarios desde el SDK del servicio:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Agregue el código siguiente; para ello, sustituya el marcador de posición por `[IoTHub Connection String]` y `[Device Id]` con la cadena de conexión de la instancia de IoT Hub y el ID de dispositivo que creó en las secciones anteriores.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Agregue el código siguiente al archivo **AddTagsAndQuery.py**:
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    El objeto **Registro** expone todos los métodos necesarios para interactuar con dispositivos gemelos del servicio. En primer lugar, el código inicializa el objeto **Registry** y, después, actualiza el dispositivo gemelo para **deviceId** que, por último, ejecuta dos consultas. La primera selecciona solo los dispositivos gemelos que se encuentran en la planta **Redmond43** y la segunda mejora la consulta para seleccionar solo los dispositivos que están también conectados a través de la red de telefonía móvil.
   
1. Agregue el código siguiente al final de **AddTagsAndQuery.py** para implementar la función **iothub_service_sample_run**:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Ejecute la aplicación con:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Debería ver un dispositivo en los resultados de la consulta que pregunta por todos los dispositivos que se encuentran en **Redmond43** y ninguno para la consulta que restringe los resultados a los dispositivos que utilizan una red de telefonía móvil.
   
    ![primera consulta][1]

En la siguiente sección, creará una aplicación de dispositivo que notifica la información de conectividad y cambia el resultado de la consulta en la sección anterior.

## <a name="create-the-device-app"></a>Creación de la aplicación del dispositivo
En esta sección, creará una aplicación de consola de Python que se conecta al centro como su **{Device Id}**, y luego actualiza las propiedades notificadas de su dispositivo gemelo para contener la información que está conectada mediante una red de telefonía móvil.

1. Abra un símbolo del sistema e instale el **SDK del servicio Azure IoT Hub para Python**. Cierre el símbolo del sistema después de instalar el SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Con un editor de texto, cree un nuevo archivo **ReportConnectivity.py**.

3. Agregue el código siguiente para importar los módulos necesarios desde el SDK del servicio:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Agregue el código siguiente; para ello, sustituya el marcador de posición por `[IoTHub Device Connection String]` con la cadena de conexión del dispositivo de IoT Hub que creó en la sección anterior.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Agregue el código siguiente al archivo **ReportConnectivity.py** archivo para implementar la funcionalidad de dispositivos gemelos:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    El objeto **Cliente** expone todos los métodos necesarios para interactuar con dispositivos gemelos del dispositivo. El código anterior, una vez que inicialice el objeto **Cliente**, recupera el dispositivo gemelo del dispositivo, y actualiza su propiedad notificada con la información de conectividad.

1. Agregue el código siguiente al final de **ReportConnectivity.py** para implementar la función **iothub_client_sample_run**:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Ejecute la aplicación del dispositivo
   
    ```cmd/sh
    python ReportConnectivity.js
    ```
   
    Debería ver la confirmación de que se actualizaron los dispositivos gemelos.

    ![actualizar gemelos][2]

6. Ahora que el dispositivo ha informado sobre su información de conectividad, debe aparecer en ambas consultas. Vuelva atrás y ejecute las consultas de nuevo:
   
    ```cmd/sh
    python AddTagsAndQuery.js
    ```
   
    Esta vez **{Device Id}** debe aparecer en los resultados de ambas consultas.
   
    ![segunda consulta][3]

## <a name="next-steps"></a>pasos siguientes
En este tutorial, configuró un centro de IoT nuevo en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Ha agregado metadatos de dispositivo como etiquetas desde una aplicación back-end y ha escrito una aplicación de dispositivo simulado para notificar la información de conectividad de dispositivos en el dispositivo gemelo. También ha aprendido cómo consultar esta información mediante el Registro.

Use los siguientes recursos para obtener información sobre cómo:

* Enviar telemetría desde dispositivos con el tutorial [Introducción a IoT Hub][lnk-iothub-getstarted].
* Configurar dispositivos mediante las propiedades deseadas del dispositivo gemelo con el tutorial [Uso de las propiedades deseadas para configurar dispositivos][lnk-twin-how-to-configure].
* Controlar los dispositivos de forma interactiva (por ejemplo, encender un ventilador desde una aplicación controlada por el usuario), con el tutorial [Uso de métodos directos][lnk-methods-tutorial].

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
