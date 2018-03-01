---
title: "Programación de trabajos con Azure IoT Hub (Python) | Microsoft Docs"
description: "Cómo programar un trabajo de IoT Hub de Azure para invocar un método directo en varios dispositivos. El SDK de IoT de Azure para Python se usa para implementar las aplicaciones de dispositivo simulado y una aplicación de servicio para ejecutar el trabajo."
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: 
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 3b6daf1ce1116ea3787550f9dac8d90b3751f9af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Programación y difusión de trabajos (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

IoT Hub de Azure es un servicio completamente administrado que permite a una aplicación back-end crear y realizar un seguimiento de los trabajos que programan y actualizan millones de dispositivos.  Los trabajos pueden utilizarse para las siguientes acciones:

* Actualizar las propiedades deseadas
* Actualizar etiquetas
* Invocar métodos directos

Conceptualmente, un trabajo contiene una de estas acciones y realiza un seguimiento del progreso de ejecución en un conjunto de dispositivos, que define una consulta de dispositivo gemelo.  Por ejemplo, una aplicación back-end puede usar un trabajo para invocar un método de reinicio en 10 000 dispositivos, especificado por una consulta de dispositivos gemelos y programada en el futuro.  Esa aplicación puede después seguir el progreso cuando cada uno de estos dispositivos reciben y ejecutan el método de reinicio.

Más información sobre estas funcionalidades en estos artículos:

* Dispositivo gemelo y propiedades: [Introducción a los dispositivos gemelos][lnk-get-started-twin] y [Tutorial: Uso de las propiedades deseadas para configurar dispositivos][lnk-twin-props]
* Métodos directos: [Guía del desarrollador de IoT Hub: métodos directos][lnk-dev-methods] y [Tutorial: Uso de métodos directos][lnk-c2d-methods]

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de dispositivo simulado en Python con un método directo, que habilita **lockDoor**, que la solución de back-end puede llamar.
* Crear una aplicación de consola de Python que llame al método directo **lockDoor** en la aplicación de dispositivo simulado mediante un trabajo y que actualice las propiedades deseadas mediante un trabajo de dispositivo.

Al final de este tutorial, tiene dos aplicaciones de Python:

**simDevice.py**, que se conecta a IoT Hub con la identidad del dispositivo y recibe un método directo **lockDoor**.

**scheduleJobService.py**, que llama a un método directo en la aplicación de dispositivo simulado y actualiza las propiedades deseadas del dispositivo gemelo mediante un trabajo.

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

> [!NOTE]
> El **SDK de IoT de Azure para Python** no admite directamente la funcionalidad de **trabajos**. En su lugar, este tutorial ofrece una solución alternativa con temporizadores y subprocesos asincrónicos. Para obtener más actualizaciones, consulte la lista de características del **SDK del cliente de servicio** en la página [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) (SDK de IoT de Azure para Python). 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de Python que responda a un método directo al que la nube llama, que desencadena un método **lockDoor** simulado.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iot-device-client**:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Con un editor de texto, cree un nuevo archivo **simDevice.py** en el directorio de trabajo.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **simDevice.py**. Reemplace `deviceConnectionString` con la cadena de conexión del dispositivo que creó anteriormente:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Agregue la siguiente devolución de llamada de función para controlar el método **lockDoor**:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Agregue otra devolución de llamada de función para controlar las actualizaciones de los dispositivos gemelos:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Agregue el código siguiente para registrar el controlador del método **lockDoor**. Incluya también la rutina `main`:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Guarde y cierre el archivo **simDevice.py**.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Programación de trabajos para llamar un método directo y actualización de las propiedades de un dispositivo gemelo
En esta sección, creará una aplicación de consola de Python que inicia un **lockDook** remoto en un dispositivo con un método directo y actualiza las propiedades del dispositivo gemelo.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iot-service-client**:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Con un editor de texto, cree un nuevo archivo **scheduleJobService.py** en el directorio de trabajo.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **scheduleJobService.py**:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Agregue la siguiente función que se usa para consultar los dispositivos:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Agregue los métodos siguientes para ejecutar los trabajos que llaman al método directo y al dispositivo gemelo:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Agregue el código siguiente para programar los trabajos y actualizar sus estados. Incluya también la rutina `main`:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
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
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Guarde y cierre el archivo **scheduleJobService.py**.


## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema, en el directorio de trabajo, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio:
   
    ```cmd/sh
    python simDevice.py
    ```

1. En otro símbolo del sistema, en el directorio de trabajo, ejecute el siguiente comando para desencadenar los trabajos a fin de bloquear la puerta y actualizar el dispositivo gemelo:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Verá las respuestas del dispositivo al método directo y la actualización de los dispositivos gemelos en la consola.

    ![resultados del dispositivo][1]

    ![resultados del servicio][2]


## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha utilizado un trabajo para programar un método directo para un dispositivo y la actualización de las propiedades del dispositivo gemelo.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remoto a través de la actualización de firmware de aire, consulte:

[Tutorial: Realización de una actualización de firmware][lnk-fwupdate]

Para continuar con la introducción a IoT Hub, consulte [Introducción a Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
