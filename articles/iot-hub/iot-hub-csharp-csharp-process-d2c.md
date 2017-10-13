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
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 1d2b52ea005ab520bf294efa603512c00a92ee63
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas (.NET)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Este tutorial se basa en el tutorial [Introducción a IoT Hub]. Este tutorial:

* Muestra cómo usar reglas de enrutamiento para enviar mensajes del dispositivo a la nube de manera sencilla y basada en la configuración.
* Ilustra cómo detectar mensajes interactivos que requieren acción inmediata en el back-end de soluciones para su procesamiento posterior. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de punto de datos, como los datos de telemetría de temperatura, se envían a un motor de análisis.

Al final de este tutorial tendrá tres aplicaciones de consola de .NET:

* **SimulatedDevice**, una versión modificada de la aplicación creada en el tutorial de [Introducción a IoT Hub], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría no críticos enviados por la aplicación de dispositivo.
* **ReadCriticalQueue** quita de la cola los mensajes críticos enviados por la aplicación de dispositivo desde una cola de Service Bus. Esta cola se asocia a la instancia de IoT Hub.

> [!NOTE]
> El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para obtener instrucciones sobre cómo reemplazar el dispositivo simulado de este tutorial por un dispositivo físico, consulte el [Centro para desarrolladores de Azure IoT].

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en tan solo unos minutos.

También se dan por sentados ciertos conocimientos sobre [Azure Storage] y [Azure Service Bus].

## <a name="send-interactive-messages"></a>Envío de mensajes interactivos

Modifique la aplicación de dispositivo que ha creado en el tutorial [Introducción a IoT Hub] a fin de enviar ocasionalmente mensajes interactivos.

En Visual Studio, en el proyecto **SimulatedDevice**, sustituya el método `SendDeviceToCloudMessagesAsync` por el código siguiente:

```csharp
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

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>Enrutamiento de mensajes a una cola en IoT Hub

En esta sección:

* Creará una cola de Service Bus.
* La conectará al centro de IoT.
* Configurará el centro de IoT para enviar mensajes a la cola en función de la presencia propiedades en el mensaje.

Para obtener más información acerca de cómo procesar los mensajes desde las colas de Service Bus, consulte [Introducción a las colas][Service Bus queue].

1. Cree una cola de Service Bus como se describe en [Introducción a las colas][Service Bus queue]. La cola debe estar en la misma suscripción y región que el centro de IoT Hub. Tome nota del espacio de nombres y del nombre de la cola.

    > [!NOTE]
    > Las colas y los temas de Service Bus usados como puntos de conexión de IoT Hub no deben tener habilitadas las opciones **Sesiones** o **Detección de duplicados**. Si cualquiera de estas opciones está habilitada, el punto de conexión aparece como **Inaccesible** en Azure Portal.

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
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Finalmente, agregue las líneas siguientes al método **Main** . Sustituya la cadena de conexión por los permisos de **escucha** de la cola:
   
    ```csharp
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
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[Introducción a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
