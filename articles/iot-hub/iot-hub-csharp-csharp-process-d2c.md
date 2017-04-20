---
title: Procesamiento de mensajes de dispositivo a nube de IoT Hub de Azure mediante rutas (.NET) | Microsoft Docs
description: "Cómo procesar mensajes de dispositivo a nube de IoT Hub mediante reglas de enrutamiento y puntos de conexión personalizados para enviar mensajes a otros servicios de back-end."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: ff0b234f27e2d5068cc0dcdc73e32e60f8622633
ms.lasthandoff: 03/06/2017


---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introducción
Azure IoT Hub es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y un back-end de la solución. Otros tutoriales ([Introducción a IoT Hub] y [Envío de mensajes de nube a dispositivo con IoT Hub][lnk-c2d]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo de IoT Hub.

Este tutorial se basa en el tutorial [Introducción a IoT Hub] y le muestra cómo usar reglas de enrutamiento para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración. El tutorial ilustra cómo detectar mensajes que requieren acción inmediata en el back-end de soluciones para su procesamiento posterior. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de punto de datos simplemente se envían a un motor de análisis. Por ejemplo, la telemetría de temperatura de un dispositivo que se almacena para su posterior análisis es un mensaje de punto de datos.

Al final de este tutorial tendrá tres aplicaciones de consola de .NET:

* **SimulatedDevice**, una versión modificada de la aplicación creada en el tutorial de [Introducción a IoT Hub] , que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos. Esta aplicación usa el protocolo AMQP para comunicarse con IoT Hub.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría no críticos enviados por la aplicación de dispositivo simulado.
* **read-critical-queue** quita de la cola los mensajes críticos enviados por la aplicación de dispositivo simulado desde la cola de la instancia de Service Bus adjunta a IoT Hub.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para obtener instrucciones sobre cómo reemplazar el dispositivo simulado de este tutorial por un dispositivo físico y cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

También se dan por sentados ciertos conocimientos sobre [Azure Storage] y [Azure Service Bus].

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>Envío de mensajes interactivos desde una aplicación de dispositivo simulado
En esta sección, se modifica la aplicación de dispositivo simulado que creó en el tutorial [Introducción a IoT Hub] a fin de enviar ocasionalmente mensajes que requieren un procesamiento inmediato.

En Visual Studio, en el proyecto **SimulatedDevice**, sustituya el método `SendDeviceToCloudMessagesAsync` por el código siguiente:

```
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            messageString = "This is a critical message";
            levelValue = "critical";
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

Con este método se agrega aleatoriamente la propiedad `"level": "critical"` a los mensajes que envía el dispositivo, lo que simula un mensaje que requiere una acción inmediata del back-end de soluciones. La aplicación de dispositivo pasa esta información en las propiedades del mensaje, en lugar de en el cuerpo del mensaje, de manera que este IoT Hub puede enrutar el mensaje a su destino correcto.

> [!NOTE]
> Puede usar propiedades de mensaje a fin de enrutar mensajes en diferentes escenarios, como el procesamiento en frío, además del ejemplo de procesamiento en caliente que se muestra aquí.

> [!NOTE]
> Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, debe implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling](Control de errores transitorios).

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Adición de una cola a su IoT Hub y enrutamiento de mensajes a ella
En esta sección:

* Creará una cola de Service Bus.
* La conectará al centro de IoT.
* Configurará el centro de IoT para enviar mensajes a la cola en función de la presencia propiedades en el mensaje.

Para obtener más información acerca de cómo procesar los mensajes desde las colas de Service Bus, consulte [Introducción a las colas][Service Bus queue].

1. Cree una cola de Service Bus como se describe en [Introducción a las colas][Service Bus queue]. La cola debe estar en la misma suscripción y región que el centro de IoT Hub. Tome nota del espacio de nombres y del nombre de la cola.

2. En Azure Portal, abra el centro de IoT y haga clic en **Endpoints** (Puntos de conexión).
    
    ![Puntos de conexión en IoT Hub][30]

3. En la hoja **Endpoints** (Puntos de conexión), haga clic en **Add** (Agregar) en la parte superior para agregar la cola al centro de IoT. Ponga al punto de conexión el nombre **CriticalQueue** y use las listas desplegables para seleccionar **Cola de Service Bus**, el espacio de nombres de Service Bus en el que reside la cola y el nombre de la cola. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.
    
    ![Adición de un punto de conexión][31]
    
4. Ahora haga clic en **Routes** (Rutas) en IoT Hub. Haga clic en **Add** (Agregar) en la parte superior de la hoja para crear una regla que enrute los mensajes a la cola que acaba de agregar. Seleccione **DeviceTelemetry** como origen de los datos. Escriba `level="critical"` como condición y elija la cola que acaba de agregar como punto de conexión personalizado como punto de conexión de regla de enrutamiento. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.
    
    ![Adición de una ruta][32]
    
    Asegúrese de que la ruta de reserva se establece en **ON** (Activado). Este valor es la configuración predeterminada para un centro de IoT.
    
    ![Ruta de reserva][33]

## <a name="read-from-the-queue-endpoint"></a>Lectura desde el punto de conexión de la cola
En esta sección, se leen los mensajes desde el punto de conexión de la cola.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Ponga al proyecto el nombre **ReadCriticalQueue**.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadCriticalQueue** y, a continuación, seleccione **Administrar paquetes NuGet**. Esta acción hace que aparezca la ventana **Administrador de paquetes NuGet** .

3. Busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte las condiciones de uso. Esta operación permite descargar, instalar y agregar una referencia a Azure Service Bus, con todas sus dependencias.

4. En la parte superior del archivo **Program.cs**, agregue las siguientes instrucciones **using**:
   
    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Finalmente, agregue las líneas siguientes al método **Main** . Sustituya la cadena de conexión por los permisos de **escucha** de la cola:
   
    ```
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione **Iniciar** como acción para los proyectos **ReadDeviceToCloudMessages**, **SimulatedDevice** y **ReadCriticalQueue**.
2. Presione **F5** para iniciar las tres aplicaciones de consola. La aplicación **ReadDeviceToCloudMessages** tiene solo mensajes no críticos enviados desde la aplicación **SimulatedDevice**, y la aplicación **ReadCriticalQueue** tiene solo mensajes críticos.
   
   ![Tres aplicaciones de consola][50]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a enviar de manera confiable mensajes del dispositivo a la nube mediante la funcionalidad de enrutamiento de mensajes de IoT Hub.

El tutorial sobre [cómo enviar mensajes de dispositivo a la nube con IoT Hub][lnk-c2d] muestra cómo enviar mensajes a los dispositivos desde la solución de back-end.

Para ver ejemplos de soluciones completas de un extremo a otro que usan IoT Hub, consulte [Conjunto de aplicaciones de IoT de Azure][lnk-suite].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

Para obtener más información sobre el enrutamiento de mensajes en IoT Hub, consulte [Envío y recepción de mensajes con IoT Hub][lnk-devguide-messaging].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[Introducción a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Event Hubs Programming Guide]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Build multi-tier applications with Service Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

