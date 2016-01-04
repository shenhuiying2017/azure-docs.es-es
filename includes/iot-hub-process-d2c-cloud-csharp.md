## Procesamiento de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Windows que procesa mensajes de dispositivo a nube desde el Centro de IoT. El Centro de IoT expone un extremo compatible con [Centros de eventos ][Event Hubs Overview] para leer mensajes de dispositivo a nube. En este tutorial se usa [EventProcessorHost] para procesar estos mensajes en una aplicación de consola. Para obtener más información sobre cómo procesar los mensajes de los Centros de eventos, puede consultar el tutorial [Introducción a los Centros de eventos].

La principal dificultad a la hora de implementar un almacenamiento fiable de mensajes de puntos de datos o de reenviar los interactivos, es que el procesamiento de eventos de los Centros de eventos depende del consumidor de mensajes para ejecutar puntos de control de su progreso. Además, para lograr un alto rendimiento, al leer desde los Centros de eventos, los puntos de control deben tener lugar en lotes de gran tamaño, lo que crea la posibilidad de procesamiento duplicado de un gran número de mensajes. En este tutorial se muestra cómo sincronizar escrituras de almacenamiento de Azure y ventanas de desduplicación de Bus de servicio con puntos de control del host de procesador de eventos.

Para escribir mensajes de manera fiable en el almacenamiento de Azure, usaremos la característica [blobs en bloques][Azure Block Blobs] de confirmación de bloques individuales. El procesador de eventos acumula los mensajes en memoria hasta que llega la hora de ejecutar un punto de control (es decir, cuando el búfer acumulado es mayor que el tamaño máximo de bloque de 4 MB, o bien una vez que transcurren las ventanas de tiempo de desduplicación del Bus de servicio). A continuación, antes de ejecutar los puntos de control, se confirma un nuevo bloque en el blob.

El procesador de eventos usa desplazamientos de mensajes de Centros de eventos como identificadores de bloque. Esto permite realizar una comprobación de desduplicación antes de confirmar el nuevo bloque en el almacenamiento, vigilando la posibilidad de un bloqueo entre un bloque confirmado y el punto de control.

> [AZURE.NOTE]En este tutorial se usa una sola cuenta de almacenamiento para escribir todos los mensajes que se recuperan del Centro de IoT. Consulte [Instrucciones sobre escalabilidad de Almacenamiento de Azure] para decidir si necesita varias cuentas de Almacenamiento de Azure en su solución.

Para evitar duplicados en el procesamiento de mensajes interactivos, usamos la característica de desduplicación del Bus de servicio. Al marcar cada mensaje interactivo con un único `MessageId`, el Bus de servicio puede garantizar que, en la ventana de tiempo de desduplicación especificada, no se entregarán dos mensajes con el mismo `MessageId` a los receptores. Esta desduplicación, junto con la semántica de finalización de cada mensaje proporcionada por las colas del Bus de servicio, hace que el procesamiento fiable de los mensajes interactivos se convierta en una tarea bastante más fácil.

Para tener la seguridad de que no se reenvía ningún mensaje fuera de la ventana de desduplicación, sincronizamos el mecanismo de ejecución de puntos de control del host de procesador de eventos con la ventana de desduplicación de cola del Bus de servicio. Para ello, se fuerza un punto de control al menos una vez cada vez que transcurre una ventana de tiempo (en este tutorial, una hora).

> [AZURE.NOTE]En este tutorial se usa una cola del Bus de servicio para procesar todos los mensajes interactivos recibidos del Centro de IoT. Según las necesidades de su solución, consulte la [documentación del Bus de servicio] para obtener más información sobre cómo usar las colas del Bus de servicio.

### Aprovisionamiento de una cuenta de almacenamiento de Azure y una cola del Bus de servicio
Para usar [EventProcessorHost], debe tener una cuenta de almacenamiento de Azure. Puede usar una existente o seguir las instrucciones que se indican en [Acerca de Almacenamiento de Azure] para crear una. Tome nota de la cadena de conexión de la cuenta de almacenamiento.

También necesitará una cola del Bus de servicio para permitir el procesamiento fiable de los mensajes interactivos. Puede crear una cola mediante programación con una ventana de desduplicación de 1 hora, como se explica en [Uso de colas del Bus de servicio][Service Bus Queue], o bien usar el [Portal de Azure], siguiendo estos pasos:

1. Haga clic en **NUEVO** en la esquina inferior izquierda, y haga clic sucesivamente en **Servicios de aplicaciones**, **Bus de servicio**, **Cola** y **Personalizado**, y seleccione el nombre **d2ctutorial** y, luego, una ventana de desduplicación de 1 hora.

    ![][30]

2. Haga clic en el nombre de la cola (**d2ctutorial**), luego en **Configurar**, y cree dos directivas de acceso compartido, una llamada **enviar** con permisos de **envío** y otra llamada **escuchar** con permisos de **escucha**. Haga clic en **Guardar** en la parte inferior de la página.

    ![][31]

3. Haga clic en **Panel** en la parte superior, luego en **Información de conexión** en la parte inferior, y tome nota de las dos cadenas de conexión.

    ![][32]

### Creación del procesador de eventos

1. En la solución actual de Visual Studio, haga clic en **Archivo ->Agregar -> Proyecto** para crear un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **ProcessDeviceToCloudMessages**.

    ![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet**.

3. Busque `WindowsAzure.ServiceBus`, haga clic en **Instalar** y acepte las condiciones de uso.

    De esta forma, se descarga, instala y agrega una referencia al [paquete de NuGet del Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con todas sus dependencias.

4. Busque `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, haga clic en **Instalar** y acepte las condiciones de uso.

    Se descarga, instala y agrega una referencia al [paquete de NuGet EventProcessorHost del Centro de eventos del Bus de servicio de Azure](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

5. Haga clic con el botón derecho en el proyecto **ProcessDeviceToCloudMessages**, haga clic en **Agregar** y, luego, en **Clase**. Asigne a la nueva clase el nombre **StoreEventProcessor** y, luego, haga clic en **Aceptar** para crear la clase.

6. Agregue las siguientes instrucciones en la parte superior del archivo StoreEventProcessor.cs:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    A continuación, sustituya el código siguiente por el cuerpo de la clase:

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }

                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    **EventProcessorHost** llama a esta clase para procesar los mensajes de dispositivo a nube recibidos desde el Centro de IoT, e implementa la lógica principal para el almacenamiento fiable de los mensajes en un contenedor de blogs y el reenvío de los mensajes interactivos a la cola del Bus de servicio. El método `OpenAsync()`, inicializa la variable `currentBlockInitOffset`, que realiza el seguimiento del desplazamiento actual del primer mensaje que lee este procesador de eventos (recuerde que cada procesador es responsable de una sola partición).

    El método `ProcessEventsAsync()` recibe un lote de mensajes del Centro de IoT que se procesan del modo siguiente. Los mensajes interactivos se envían a la cola del Bus de servicio, mientras que los mensajes de puntos de datos se anexan al búfer de memoria llamado `toAppend`. Si el búfer de memoria alcanza el límite de bloques (es decir, 4 MB), o las ventanas de tiempo de desduplicación del Bus de servicio transcurrieron desde el último punto de control (en este tutorial, 1 hora), se desencadena un punto de control.

    El método `AppendAndCheckpoint()` genera primero un blockId para el bloque que se va a anexar. Puesto que el almacenamiento de Azure requiere que todos los identificadores de bloque tengan la misma longitud, el desplazamiento se rellena con ceros iniciales (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Luego, si ya existe un bloque con este identificador en el blog, el método lo sobrescribe con el actual.

> [AZURE.NOTE]Para simplificar el código, en este tutorial se usa un solo archivo de blob por partición para almacenar los mensajes. En una solución real se implementan los archivos gradualmente, de modo que se crean archivos adicionales cuando alcanzan un determinado tamaño (tenga en cuenta que el blob de Azure puede tener como máximo 195 Gb), o después de un determinado período de tiempo (por ejemplo, `fileName_{partitionId}_{date-time}`).

7. En la clase **Program**, agregue las siguientes instrucciones `using` en la parte superior:

        using Microsoft.ServiceBus.Messaging;

    A continuación, modifique el método **Main** para la clase **Program** como se muestra a continuación y sustituya la cadena de conexión **iothubowner** del Centro de IoT (del tutorial [Introducción a los Centros de eventos]), la cadena de conexión de almacenamiento y la cadena de conexión del Bus de servicio por los permisos de **envío** para la cola llamada **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]Por motivos de simplicidad, en este tutorial se usa una sola instancia de [EventProcessorHost]. Consulte [Guía de programación de los Centros de eventos] y el tutorial [Procesamiento de mensajes de dispositivo a nube] para obtener más información sobre el procesamiento de mensajes de dispositivo a nube.

## Recepción de mensajes interactivos
En esta sección, escribirá una aplicación de consola de Windows que recibe mensajes interactivos de la cola del Bus de servicio. Para obtener más información sobre cómo diseñar una solución con el Bus de servicio, consulte [Compilación de aplicaciones de varios con el Bus de servicio][].

1. En la solución actual de Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **ProcessD2cInteractiveMessages**.

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**.

    Se muestra la ventana Administrar paquetes de NuGet.

3. Busque `WindowsAzure.Service Bus`, haga clic en **Instalar** y acepte las condiciones de uso.

    De esta forma, se descarga, instala y agrega una referencia al [Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Por último, agregue las siguientes líneas al método **Main** y sustituya la cadena de conexión por los permisos de **escucha** para la cola llamada **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[Acerca de Almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introducción a los Centros de eventos]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Instrucciones sobre escalabilidad de Almacenamiento de Azure]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[documentación del Bus de servicio]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Guía de programación de los Centros de eventos]: ../event-hubs/event-hubs-programming-guide.md

[Azure preview portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[Portal de Azure]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Compilación de aplicaciones de varios con el Bus de servicio]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md


<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!----HONumber=Nov15_HO3-->