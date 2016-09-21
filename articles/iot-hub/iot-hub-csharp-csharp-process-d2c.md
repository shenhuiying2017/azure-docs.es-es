<properties
	pageTitle="Procesamiento de mensajes de dispositivo a la nube de IoT Hub | Microsoft Azure"
	description="Siga este tutorial para aprender sobre patrones útiles para procesar mensajes de dispositivo a nube del centro de IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="07/19/2016"
     ms.author="dobett"/>

# Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub mediante .Net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Otros tutoriales ([Introducción al Centro de IoT de Azure para .NET] y [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo del Centro de IoT.

Este tutorial se basa en el código que se muestra en el tutorial [Introducción al Centro de IoT de Azure para .NET] y muestra dos patrones escalables que se pueden usar para procesar mensajes de dispositivo a nube:

- Almacenamiento confiable de mensajes de dispositivo a nube de [Almacenamiento de blobs de Azure]. Un escenario común es el *análisis en frío*, en el que se almacenan datos de telemetría en blobs para usarlos como entrada en los procesos de análisis. Estos procesos pueden estar controlados por herramientas como [Data Factory de Azure] o la pila [HDInsight (Hadoop)].

- Procesamiento confiable de mensajes de dispositivo a nube *interactivos*. Los mensajes de dispositivo a nube son interactivos cuando son desencadenantes inmediatos de un conjunto de acciones en el back-end de la aplicación. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de *punto de datos* simplemente se envían a un motor de análisis. Por ejemplo, la telemetría de temperatura de un dispositivo que se almacena para su posterior análisis es un mensaje de punto de datos.

Puesto que Centro de IoT expone un punto de conexión compatible con los [Centros de eventos] para recibir mensajes de dispositivo a nube, este tutorial usa una instancia de [EventProcessorHost][lnk-event-hubs]. Esta instancia:

* Almacena de manera confiable mensajes de *punto de datos* en Almacenamiento de blobs de Azure.
* Reenvía mensajes de dispositivo a nube *interactivos* a una [cola del Bus de servicio] para su procesamiento inmediato.

El Bus de servicio ayuda a asegurar un procesamiento confiable de mensajes interactivos, ya que ofrece puntos de comprobación de cada mensaje y desduplicación basada en periodos de tiempo.

> [AZURE.NOTE] Una instancia de **EventProcessorHost** es solamente una de las formas de procesar los mensajes interactivos. Otras opciones incluyen [Azure Service Fabric][lnk-service-fabric] y [Análisis de transmisiones de Azure][lnk-stream-analytics].

Al final de este tutorial, ejecutará tres aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en el tutorial [Introducción al Centro de IoT de Azure para .NET], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos. Esta aplicación usa el protocolo AMQPS para comunicarse con el Centro de IoT.
* **ProcessDeviceToCloudMessages** utiliza la clase [EventProcessorHost] para recuperar mensajes desde el punto de conexión compatible con los Centros de eventos. A continuación, almacena los mensajes de punto de datos de forma confiable en Almacenamiento de blobs de Azure y envía mensajes interactivos a una cola de Bus de servicio.
* **ProcessD2CInteractiveMessages** quita los mensajes interactivos de la cola del Bus de servicio.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para obtener instrucciones sobre cómo reemplazar el dispositivo simulado de este tutorial con un dispositivo físico y sobre cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].

Este tutorial se puede aplicar directamente a otras formas de consumir mensajes compatibles con Centros de eventos como, por ejemplo, proyectos de [HDInsight (Hadoop)]. Consulte la [Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube] para más información.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. <br/>Si no tiene una suscripción de Azure, puede crear una [cuenta gratis](https://azure.microsoft.com/free/) en tan solo unos minutos.

También se dan por sentados ciertos conocimientos sobre [Almacenamiento de Azure] y [Bus de servicio de Azure].


## Envío de mensajes interactivos desde un dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción a IoT Hub] para enviar mensajes de dispositivo a nube interactivos a IoT Hub.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Este método es similar al método **SendDeviceToCloudMessagesAsync** del proyecto **SimulatedDevice**. Las únicas diferencias son que ahora define la propiedad del sistema **MessageId** y una propiedad de usuario denominada **messageType**. El código asigna un identificador único global (GUID) a la propiedad **MessageId**. Service Bus puede utilizar este identificador para desduplicar los mensajes que recibe. En el ejemplo se usa la propiedad **messageType** para distinguir los mensajes interactivos de los mensajes de punto de datos. La aplicación pasa esta información en las propiedades del mensaje, no en el cuerpo, para que el procesador de eventos no tenga que deserializar el mensaje para realizar su enrutamiento.

    > [AZURE.NOTE] Es importante crear el valor de **MessageId** usado para desduplicar los mensajes interactivos en el código de dispositivo. Las comunicaciones de red intermitentes, u otros errores, podrían provocar varias retransmisiones del mismo mensaje desde ese dispositivo. También puede utilizar un identificador de mensaje semántico, como un hash de los campos de datos relevantes del mensaje, en lugar de un GUID.

2. Agregue el siguiente método en el método **Main**, inmediatamente delante de la línea `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, debe implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

## Procesamiento de mensajes de dispositivo a la nube

En esta sección, creará una aplicación de consola de Windows que procesa mensajes de dispositivo a nube desde IoT Hub. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ] que permite que una aplicación lea los mensajes de dispositivo a nube. En este tutorial se utiliza la clase [EventProcessorHost] para procesar estos mensajes en una aplicación de consola. Para más información sobre cómo procesar los mensajes de los Centros de eventos, consulte el tutorial [Introducción a los Centros de eventos].

La principal dificultad a la que se enfrenta a la hora de implementar un almacenamiento confiable de mensajes de puntos de datos o de reenviar los mensajes interactivos, es que el procesamiento de eventos depende del consumidor de mensajes para ejecutar puntos de control de su progreso. Además, para lograr un alto rendimiento, al leer desde Event Hubs debería ejecutar puntos de control en lotes grandes. Este enfoque crea la posibilidad de realizar un procesamiento duplicado para un gran número de mensajes si se produce un error y tiene que volver al punto de control anterior. En este tutorial verá cómo sincronizar escrituras de Azure Storage y ventanas de desduplicación de Service Bus con puntos de control de la clase **EventProcessorHost**.

Para escribir mensajes de manera confiable en Almacenamiento de Azure, en el ejemplo se utiliza la característica de confirmación de bloques individuales de [blobs en bloques][Azure Block Blobs]. El procesador de eventos acumula mensajes en la memoria hasta que llega el momento de ejecutar un punto de control. Por ejemplo, una vez que el búfer de mensajes acumulado alcanza el tamaño máximo de bloque de 4 MB o después de que haya transcurrido la ventana de tiempo de desduplicación de Service Bus. Después, antes de ejecutar los puntos de control, el código confirma un nuevo bloque en el blob.

El procesador de eventos usa desplazamientos de mensajes de Centros de eventos como identificadores de bloque. Este mecanismo permite al procesador de eventos realizar una comprobación de desduplicación antes de confirmar el nuevo bloque en el almacenamiento, con lo que se vigila la posibilidad de que se produzca un bloqueo entre la confirmación de un bloque y el punto de control.

> [AZURE.NOTE] En este tutorial se usa una sola cuenta de almacenamiento para escribir todos los mensajes que se recuperan del Centro de IoT. Consulte las [instrucciones de escalabilidad de Almacenamiento de Azure] para decidir si necesita utilizar varias cuentas de Almacenamiento de Azure en su solución.

La aplicación utiliza la característica de desduplicación de Service Bus para evitar duplicados cuando procesa mensajes interactivos. El dispositivo simulado marca cada mensaje interactivo con un único **MessageId**. Estos identificadores permiten a Service Bus garantizar que, en la ventana de tiempo de desduplicación especificado, no se entregarán dos mensajes con el mismo **MessageId** a los receptores. Esta desduplicación, junto con la semántica de finalización de cada mensaje que proporcionan las colas del Bus de servicio, facilita la implementación de un procesamiento confiable de los mensajes interactivos.

Para tener la seguridad de que no se reenvía ningún mensaje fuera de la ventana de desduplicación, el código sincroniza el mecanismo de ejecución de puntos de control de la clase **EventProcessorHost** con la ventana de desduplicación de cola del Bus de servicio. Esta sincronización se realiza forzando un punto de control al menos una vez cada vez que transcurre una ventana de tiempo de desduplicación (en este tutorial, una hora).

> [AZURE.NOTE] En este tutorial se usa una cola del Bus de servicio para procesar todos los mensajes interactivos recibidos del Centro de IoT. Consulte la [documentación del Bus de servicio de Azure] para más información sobre cómo utilizar las colas del Bus de servicio para cumplir los requisitos de escalabilidad de su solución.

### Aprovisionamiento de una cuenta de almacenamiento de Azure y una cola del Bus de servicio
Para poder utilizar la clase [EventProcessorHost], debe tener una cuenta de Azure Storage que permita que la clase **EventProcessorHost** registre la información del punto de control. Puede utilizar una cuenta de almacenamiento que ya exista o seguir las instrucciones que se indican en [Acerca de las cuentas de almacenamiento de Azure] para crear una nueva. Tome nota de la cadena de conexión de la cuenta de almacenamiento.

> [AZURE.NOTE] Al copiar y pegar la cadena de conexión de la cuenta de almacenamiento, asegúrese de que no contenga ningún espacio.

También necesitará una cola del Bus de servicio para habilitar el procesamiento confiable de los mensajes interactivos. Puede crear una cola mediante programación con una ventana de desduplicación de una hora, tal y como se explica en [Utilización de las colas del Bus de servicio][Service Bus queue]. Como alternativa, puede usar el [Portal de Azure clásico][lnk-classic-portal] siguiendo estos pasos:

1. Haga clic en la opción **Nuevo** de la esquina inferior izquierda. Después, haga clic en **Servicios de aplicaciones** > **Bus de servicio** > **Cola** > **Creación personalizada**. Escriba el nombre **d2ctutorial**, seleccione una región y use un espacio de nombres existente o cree uno nuevo. En la siguiente página, seleccione **Habilitar detección de duplicados**, y establezca el valor de **Período de tiempo de historial de detección de duplicados** en una hora. Haga clic en la marca de verificación de la esquina inferior derecha para guardar la configuración de la cola.

    ![Creación de colas en el Portal de Azure][30]

2. En la lista de colas del Bus de servicio, haga clic en **d2ctutorial** y luego en **Configurar**. Cree dos directivas de acceso compartido, una llamada **enviar** con permisos de **envío** y otra llamada **escuchar** con permisos de **escucha**. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.

    ![Configuración de colas en el Portal de Azure][31]

3. Haga clic en la pestaña **Panel** de la parte superior y, luego, en **Información de conexión**, que se encuentra en la parte inferior. Anote las dos cadenas de conexión.

    ![Panel de colas en el Portal de Azure][32]

### Creación del procesador de eventos

1. En la solución actual de Visual Studio, haga clic en **Archivo** > **Agregar** > **Nuevo proyecto** para crear un proyecto de Visual C# para Windows con la plantilla de proyecto de **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Denomine el proyecto **ProcessDeviceToCloudMessages** y haga clic en **Aceptar**.

    ![Nuevo proyecto en Visual Studio][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ProcessDeviceToCloudMessages** y, luego, haga clic en **Administrar paquetes NuGet**. Aparece el cuadro de diálogo **Administrador de paquetes NuGet**.

3. Busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte las condiciones de uso. Esta operación permite descargar, instalar y agregar una referencia al [paquete NuGet de Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con todas sus dependencias.

4. Busque **Centro de eventos del Bus de servicio de Microsoft Azure: EventProcessorHost**, haga clic en **Instalar** y acepte los términos de uso. Esta operación permite descargar, instalar y agregar una referencia a [Azure Service Bus Event Hub - Paquete NuGet de EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

5. Haga clic con el botón derecho en el proyecto **ProcessDeviceToCloudMessages**, haga clic en **Agregar** y, luego, en **Clase**. Asigne a la nueva clase el nombre **StoreEventProcessor** y, luego, haga clic en **Aceptar** para crear la clase.

6. Agregue las siguientes instrucciones en la parte superior del archivo StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Sustituya el código siguiente por el cuerpo de la clase:

    ```
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
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
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
    ```

    La clase **EventProcessorHost** llama a esta clase para procesar los mensajes del dispositivo a la nube recibidos del Centro de IoT. El código de esta clase implementa la lógica para almacenar los mensajes de manera confiable en un contenedor de blobs y reenviar los mensajes interactivos a la cola del Bus de servicio.

    El método **OpenAsync** inicializa la variable **currentBlockInitOffset**, que realiza el seguimiento del desplazamiento actual del primer mensaje que leyó este procesador de eventos. Recuerde que cada procesador es responsable de una sola partición.

    El método **ProcessEventsAsync** recibe un lote de mensajes del Centro de IoT y los procesa del modo siguiente: envía mensajes interactivos a la cola del Bus de servicio y anexa mensajes de punto de datos al búfer de memoria denominado **toAppend**. Si el búfer de memoria alcanza el límite de 4 MB o si transcurre la ventana de tiempo de desduplicación (en este tutorial, una hora después de un punto de control), la aplicación desencadenará un punto de control.

    El método **AppendAndCheckpoint** genera primero un identificador de bloque para el bloque que se va a anexar. El Almacenamiento de Azure requiere que todos los identificadores de bloque tengan la misma longitud, por lo que el método rellena el desplazamiento con ceros iniciales (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Luego, si ya existe un bloque con este identificador en el blob, el método lo sobrescribe con el contenido actual del búfer.

    > [AZURE.NOTE] Para simplificar el código, en este tutorial se usa un solo archivo de blob por partición para almacenar los mensajes. En una solución real, los archivos se implementarían gradualmente, de modo que se crean archivos adicionales cuando alcanzan un determinado tamaño. Recuerde que el blob en bloques de Azure puede tener como máximo 195 GB de datos.

8. En la clase **Program**, agregue las instrucciones **using** siguientes en la parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifique el método **Main** de la clase **Program** como se muestra a continuación. Reemplace la **{cadena de conexión de iot hub}** por la cadena de conexión **iothubowner** del tutorial [Introducción a IoT Hub]. Reemplace la cadena de conexión de almacenamiento por la cadena de conexión que anotó al comienzo de esta sección. Reemplace la cadena de conexión de Service Bus por los permisos de **envío** para la cola llamada **d2ctutorial** que anotó al principio de esta sección:

    ```
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
    ```

    > [AZURE.NOTE] Para simplificar, en este tutorial se usa una sola instancia de la clase [EventProcessorHost]. Para más información, consulte la [Guía de programación de Centros de eventos].

## Recepción de mensajes interactivos
En esta sección, escribirá una aplicación de consola de Windows que recibe los mensajes interactivos de la cola de Service Bus. Para más información sobre cómo diseñar una solución con el Bus de servicio, consulte [Compilación de aplicaciones de varios niveles con el Bus de servicio][].

1. En la solución actual de Visual Studio, cree un proyecto de aplicación de Visual C# para Windows con la plantilla de proyecto de **Aplicación de consola**. Asigne al proyecto el nombre **ProcessD2CInteractiveMessages**.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ProcessD2CInteractiveMessages** y luego haga clic en **Administrar paquetes NuGet**. Esta acción hace que aparezca la ventana **Administrador de paquetes NuGet**.

3. Busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte las condiciones de uso. Esta operación permite descargar, instalar y agregar una referencia a [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) con todas sus dependencias.

4. En la parte superior del archivo **Program.cs**, agregue las siguientes instrucciones **using**:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por último, agregue las líneas siguientes al método **Main**: Sustituya la cadena de conexión por los permisos de **escucha** de la cola llamada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

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
    ```

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y luego **Iniciar** como la acción para los proyectos **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **ProcessD2CInteractiveMessages**.

2.	Presione **F5** para iniciar las tres aplicaciones de consola. La aplicación **ProcessD2CInteractiveMessages** debe procesar cada mensaje interactivo enviado desde la aplicación **SimulatedDevice**.

  ![Tres aplicaciones de consola][50]

> [AZURE.NOTE] Para ver las actualizaciones en el archivo de blob, debe reducir la constante **MAX\_BLOCK\_SIZE** de la clase **StoreEventProcessor** a un valor inferior, como **1024**. Este cambio resulta útil ya que se tarda algún tiempo en alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con un tamaño de bloque menor no tiene que esperar tanto tiempo para ver el blob que se crea y se actualiza. Aunque un tamaño de bloque mayor hace que la aplicación sea más escalable.

## Pasos siguientes

En este tutorial, ha aprendido a procesar de manera confiable mensajes de dispositivo a nube interactivos y de punto de datos mediante la clase [EventProcessorHost].

El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-c2d] muestra cómo enviar mensajes a los dispositivos desde el back-end.

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT][lnk-suite].

Para más información sobre cómo desarrollar soluciones con el Centro de IoT, consulte la [Guía del desarrollador del Centro de IoT de Azure].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Almacenamiento de blobs de Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Data Factory de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[cola del Bus de servicio]: ../service-bus/service-bus-dotnet-get-started-with-queues.md

[Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube]: iot-hub-devguide.md#d2c

[Almacenamiento de Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[documentación del Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador del Centro de IoT de Azure]: iot-hub-devguide.md
[Introducción a IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Acerca de las cuentas de almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introducción a los Centros de eventos]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[instrucciones de escalabilidad de Almacenamiento de Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Centros de eventos ]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Centros de eventos]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guía de programación de Centros de eventos]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Compilación de aplicaciones de varios niveles con el Bus de servicio]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0907_2016-->