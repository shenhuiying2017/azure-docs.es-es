---
title: Enrutamiento de mensajes con Azure IoT Hub (Python) | Microsoft Docs
description: "Cómo procesar mensajes de dispositivo a nube de Azure IoT Hub mediante reglas de enrutamiento y puntos de conexión personalizados para enviar mensajes a otros servicios de back-end."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: f467437afb4bf89e77668cfd3e8a824bfbde9e10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Enrutamiento de mensajes con IoT Hub (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial se basa en el tutorial [Introducción a Azure IoT Hub para .NET].  Este tutorial:

* Muestra cómo usar reglas de enrutamiento para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración.
* Ilustra cómo detectar mensajes interactivos que requieren acción inmediata en el back-end de soluciones para su procesamiento posterior.  Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM.  Por el contrario, los mensajes de punto de datos, como los datos de telemetría de temperatura, se envían a un motor de análisis.

Al final de este tutorial, ejecutará tres aplicaciones de consola de Python:

* **SimulatedDevice.py**, una versión modificada de la aplicación creada en el tutorial de [Introducción a Azure IoT Hub para .NET], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos a intervalos aleatorios. Esta aplicación usa el protocolo MQTT para comunicarse con IoT Hub.
* **ReadCriticalQueue.py** quita de la cola los mensajes críticos de la cola de la instancia de Service Bus adjunta a IoT Hub.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para instrucciones sobre cómo reemplazar el dispositivo de este tutorial con un dispositivo físico y sobre cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesitará lo siguiente:

* Una versión funcional y completa del tutorial [Introducción a Azure IoT Hub para .NET] .
* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* [Node.js 4.0 o posterior][lnk-node-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Esto es necesario para instalar la [herramienta Explorador de IoT Hub][lnk-iot-hub-explorer].
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

También se recomienda leer sobre [Azure Storage] y [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Envío de mensajes interactivos desde una aplicación de dispositivo
En esta sección, se modifica la aplicación de dispositivo que creó en el tutorial [Introducción a Azure IoT Hub para .NET] a fin de enviar ocasionalmente mensajes que requieren un procesamiento inmediato.

1. Utilice un editor de texto para abrir el archivo **SimulatedDevice.py**. Este archivo contiene el código para la aplicación **SimulatedDevice** que creó en el tutorial [Introducción a Azure IoT Hub para .NET].

2. Reemplace la función **iothub_client_telemetry_sample_run** por el código siguiente:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
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
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

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
   
    Con este método se agrega aleatoriamente la propiedad `"level": "critical"` y `"level": "storage"` a los mensajes que envía el dispositivo, lo que simula un mensaje que requiere una acción inmediata del back-end de aplicaciones o uno que necesita almacenarse permanentemente. La aplicación admite el enrutamiento de mensajes basado en el cuerpo del mensaje.
   
   > [!NOTE]
   > Puede usar propiedades de mensaje a fin de enrutar mensajes en diferentes escenarios, como el procesamiento en frío, además del ejemplo de procesamiento en caliente que se muestra aquí.

1. Guarde y cierre el archivo **SimulatedDevice.py**.

    > [!NOTE]
    > Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, debe implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Adición de colas a su IoT Hub y enrutamiento de mensajes a estas

En esta sección, se crea una cola de Service Bus y una cuenta de Storage, se conectan con el IoT Hub y se configura el IoT Hub para enviar mensajes a la cola en función de la presencia de una propiedad en el mensaje y todos los mensajes a la cuenta de Storage. Para obtener más información acerca de cómo procesar los mensajes de las colas de Service Bus, consulte [Introducción a las colas de Service Bus][lnk-sb-queues-node] y acerca de cómo administrar el almacenamiento, consulte [Documentación de Azure Storage][Azure Storage].

1. Cree una cola de Service Bus como se describe en [Introducción a las colas][lnk-sb-queues-node]. Tome nota del espacio de nombres y del nombre de la cola.

    > [!NOTE]
    > Las colas y los temas de Service Bus usados como puntos de conexión de IoT Hub no deben tener habilitadas las opciones **Sesiones** o **Detección de duplicados**. Si cualquiera de estas opciones está habilitada, el punto de conexión aparece como **Inaccesible** en Azure Portal.

1. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión** .

    ![Puntos de conexión en IoT Hub][30]

1. En la hoja **Puntos de conexión**, haga clic en **Agregar** en la parte superior para agregar la cola al centro de IoT. Ponga al punto de conexión el nombre **CriticalQueue** y use las listas desplegables para seleccionar **Cola de Service Bus**, el espacio de nombres de Service Bus en el que reside la cola y el nombre de la cola. Cuando haya terminado, haga clic en **Aceptar** en la parte inferior.  

    ![Adición de un punto de conexión][31]

1. Ahora haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. 

    ![Adición de una ruta][34]

    Escriba un nombre y seleccione **Mensajes del dispositivo** como origen de los datos. Elija **CriticalQueue** como punto de conexión personalizado como punto de conexión de regla de enrutamiento e introduzca `level="critical"` como cadena de consulta. Haga clic en **Guardar** en la parte inferior.

    ![Detalles de ruta][32]

    Asegúrese de que la ruta de reserva se establece en **Activado**. Esta es la configuración predeterminada del centro de IoT.

    ![Ruta de reserva][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcional) Lectura desde el punto de conexión de la cola

En esta sección, creará una aplicación de consola de Python que lee los mensajes críticos de IoT Service Bus. Consulte más información en [Introducción a las colas][lnk-sb-queues-node]. 

1. Abra un símbolo del sistema nuevo e instale el SDK del dispositivo Azure IoT Hub para Python. Tras la instalación, cierre el símbolo del sistema.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Si tiene problemas para instalar el paquete **azure-servicebus** o si quiere obtener más opciones de instalación, consulte el [paquete azure-servicebus de Python][lnk-python-service-bus].

1. Cree un archivo denominado **ReadCriticalQueue.py**. Ábralo en el editor/IDE de Python que prefiera (por ejemplo, IDLE).

1. Agregue el código siguiente para importar los módulos necesarios desde el SDK del dispositivo:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Agregue el código siguiente y sustituya el marcador de posición por los datos de conexión de Service Bus:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Agregue el código siguiente para conectarse a Service Bus y leer: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Finalmente, agregue la función principal. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Guarde y cierre el archivo **ReadCriticalQueue.py**. Ahora está preparado para ejecutar las aplicaciones.


## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. Abra un símbolo del sistema e instale el Explorador de IoT Hub. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Ejecute el siguiente comando en el símbolo del sistema para iniciar la supervisión de los mensajes del dispositivo a la nube. Use la cadena de conexión de su instancia de IoT Hub del marcador de posición después de `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Abra un nuevo símbolo del sistema y desplácese al directorio que contiene el archivo **SimulatedDevice.py**.

1. Ejecute el archivo **SimulatedDevice.py** que envía periódicamente los datos de telemetría a la instancia de IoT Hub. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Para ejecutar la aplicación **ReadCriticalQueue**, en un símbolo del sistema o en el shell, vaya al archivo **ReadCriticalQueue.py** y ejecute el comando siguiente:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Observe los mensajes de dispositivo en el símbolo del sistema donde se ejecuta el Explorador de IoT Hub de la sección anterior. Observe los mensajes `critical` en la aplicación **ReadCriticalQueue**.

    ![Mensajes de Python del dispositivo a la nube][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcional) Adición de un contenedor de almacenamiento a IoT Hub y enrutamiento de mensajes a este

En esta sección, se crea una cuenta de Storage, se conecta con el IoT Hub y se configura este para enviar mensajes a la cuenta en función de la presencia de una propiedad en el mensaje. Para más información acerca de cómo administrar el almacenamiento, consulte [Introducción a Azure Storage][Azure Storage].

 > [!NOTE]
   > Las cuentas de IoT Hub creadas en el nivel _F1 Gratis_ están limitadas a un **punto de conexión**. Si no está limitado a un **punto de conexión**, puede configurar **StorageContainer**, además de **CriticalQueue**, y ejecutar ambos simultáneamente.

1. Cree una cuenta de Storage como se describe en la [documentación de Azure Storage][lnk-storage]. Tome nota del nombre de la cuenta.

2. En Azure Portal, abra el centro de IoT y haga clic en **Puntos de conexión**.

3. En la hoja **Puntos de conexión**, seleccione el punto de conexión **CriticalQueue** y haga clic en **Eliminar**. Haga clic en **Sí** y, luego, en **Agregar**. Asigne el nombre **StorageContainer** al punto de conexión y use los menús desplegables para seleccionar **Contenedor de Azure Storage** y crear una **cuenta de Storage** y un **contenedor de Storage**.  Tome nota de los nombres.  Cuando haya terminado, haga clic en **Aceptar** en la parte inferior. 

 > [!NOTE]
   > Las cuentas de IoT Hub creadas en el nivel _F1 Gratis_ están limitadas a un **punto de conexión**. Si no está limitado a un **punto de conexión**, no es necesario eliminar **CriticalQueue**.

4. Haga clic en **Rutas** en IoT Hub. Haga clic en **Agregar** en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **Mensajes del dispositivo** como origen de los datos. Escriba `level="storage"` como condición y elija **StorageContainer** como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Haga clic en **Guardar** en la parte inferior.  

    Asegúrese de que la ruta de reserva se establece en **Activado**. Esta es la configuración predeterminada del centro de IoT.

1. Asegúrese de que la aplicación anterior **SimulatedDevice.py** todavía se está ejecutando. 

1. En Azure Portal, vaya a la cuenta de almacenamiento, en **Blob Service** y haga clic en **Examinar blobs...**.  Seleccione el contenedor, vaya al archivo JSON y haga clic en él. Después, haga clic en **Descargar** para ver los datos.


## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido a enviar de manera confiable mensajes del dispositivo a la nube mediante la funcionalidad de enrutamiento de mensajes de IoT Hub.

Para ver ejemplos de soluciones completas de un extremo a otro que usan IoT Hub, consulte [Azure IoT Suite][lnk-suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

Para obtener más información sobre el enrutamiento de mensajes en IoT Hub, consulte [Envío y recepción de mensajes con IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Introducción a Azure IoT Hub para .NET]: iot-hub-python-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot

[Control de errores transitorios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus