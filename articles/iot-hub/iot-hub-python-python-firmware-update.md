---
title: "Actualización de firmware de dispositivos con Azure IoT Hub (Python) | Microsoft Docs"
description: "Describe cómo usar la administración de dispositivos en IoT Hub de Azure para iniciar una actualización de firmware del dispositivo. Usará el SDK de Azure IoT para Python para implementar la aplicación de dispositivo simulado y una aplicación de servicio que desencadena la actualización del firmware."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: e7ca0d95da3fd36e6dad081bad0aa5a178436159
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Uso de la administración de dispositivos para iniciar una actualización de firmware de los dispositivos (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

En el tutorial de [Introducción a la administración de dispositivos][lnk-dm-getstarted], vimos cómo usar los primitivos de [dispositivo gemelo][lnk-devtwin] y [métodos directos][lnk-c2dmethod] para reiniciar de forma remota un dispositivo. Este tutorial utiliza a los mismos tipos primitivos de IoT Hub. Además, proporciona orientación y muestra cómo realizar una actualización de firmware simulada de extremo a extremo.  Este patrón se utiliza en la implementación de actualización de firmware para el ejemplo de dispositivo Intel Edison.

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de consola de Python que llame al método directo firmwareUpdate en la aplicación de dispositivo simulado a través de su IoT Hub.
* Cree una aplicación de dispositivo simulado que implemente un método directo **firmwareUpdate**. Este método inicia un proceso de varias fases que espera para descargar la imagen de firmware, la descarga y, por último, la aplica. Durante cada fase de la actualización, el dispositivo usa las propiedades notificadas para informar sobre el progreso.

Al final de este tutorial tendrá dos aplicaciones de consola de Python:

**dmpatterns_fwupdate_service.py**, que llama a un método directo en la aplicación de dispositivo simulado, muestra la respuesta y muestra periódicamente (cada 500 ms) las propiedades notificadas actualizadas.

**dmpatterns_fwupdate_device.py**, que se conecta a IoT Hub con la identidad del dispositivo creada anteriormente, recibe un método directo firmwareUpdate y se ejecuta mediante un proceso de varios estados para simular una actualización de firmware; entre ellas, la espera para la descarga de imágenes, la descarga de la nueva imagen y, finalmente, la aplicación de esa imagen.

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Desencadenamiento de una actualización de firmware remota en el dispositivo con un método directo
En esta sección, creará una aplicación de consola de Python que inicia una actualización de firmware remota en un dispositivo. La aplicación usa un método directo para iniciar la actualización y utiliza consultas de dispositivos gemelos para obtener periódicamente el estado de la actualización del firmware activa.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iothub-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Con un editor de texto cree, en su directorio de trabajo, un archivo **dmpatterns_getstarted_service.py**.

1. Agregue las siguientes instrucciones y variables "import" al comienzo del archivo **dmpatterns_getstarted_service.py**. Reemplace `IoTHubConnectionString` y `deviceId` por los valores anotados anteriormente:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Agregue la siguiente función para llamar al método directo y mostrar el valor de la propiedad notificada firmwareUpdate. Agregue también la rutina `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Guarde y cierre el archivo **dmpatterns_fwupdate_service.py**.


## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección:

* Creará una aplicación de consola de Python que responda a un método directo que se llama desde la nube
* Desencadenará una actualización de firmware simulada.
* Usará las propiedades notificadas para habilitar consultas de dispositivos gemelos a fin de identificar los dispositivos y cuándo se actualizó su firmware por última vez.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iothub-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Con un editor de texto, cree un archivo **dmpatterns_fwupdate_device.py**.

1. Agregue las siguientes instrucciones y variables "import" al comienzo del archivo **dmpatterns_fwupdate_device.py**. Reemplace `deviceConnectionString` por la cadena de conexión del dispositivo de IoT Hub:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Agregue las siguientes funciones que se usan para proporcionar actualizaciones de propiedades notificadas e implemente el método directo:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Agregue las siguientes funciones que simulan descargar y aplicar la imagen de firmware:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Agregue la siguiente función que inicializa las propiedades notificadas del dispositivo gemelo y espere a que se llame al método directo. Agregue también la rutina `main`:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling](https://msdn.microsoft.com/library/hh675232.aspx) (Tratamiento de errores temporales).
> 


## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. En otro símbolo del sistema, ejecute el siguiente comando para desencadenar el reinicio remoto y la consulta en el dispositivo gemelo para buscar la hora del último reinicio.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Verá la respuesta del dispositivo al método directo en la consola. A continuación, observe el cambio en las propiedades notificadas en la actualización del firmware.

    ![salida del programa][1]


## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha usado un método directo para desencadenar una actualización de firmware remota en un dispositivo y ha utilizado las propiedades notificadas para entender el progreso del proceso de actualización del firmware.

Para información sobre cómo ampliar la solución de IoT y programar llamadas de método en varios dispositivos, consulte el tutorial [Programación de trabajos en varios dispositivos][lnk-tutorial-jobs].

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
