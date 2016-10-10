<properties
	pageTitle="Procesamiento de mensajes de dispositivo a la nube de IoT Hub (Java) | Microsoft Azure"
	description="Siga este tutorial de Java para aprender patrones útiles de procesamiento de mensajes de dispositivo a nube de IoT Hub."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub mediante Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Otros tutoriales ([Introducción al Centro de IoT de Azure para .NET] y [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo del Centro de IoT.

Este tutorial se basa en el código que se muestra en el tutorial [Introducción al Centro de IoT de Azure para .NET] y muestra dos patrones escalables que se pueden usar para procesar mensajes de dispositivo a nube:

- Almacenamiento confiable de mensajes de dispositivo a nube de [Almacenamiento de blobs de Azure]. Un escenario común es el *análisis en frío*, en el que se almacenan datos de telemetría en blobs para usarlos como entrada en los procesos de análisis. Estos procesos pueden estar controlados por herramientas como [Data Factory de Azure] o la pila [HDInsight (Hadoop)].

- Procesamiento confiable de mensajes de dispositivo a nube *interactivos*. Los mensajes de dispositivo a nube son interactivos cuando son desencadenantes inmediatos de un conjunto de acciones en el back-end de la aplicación. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de *punto de datos* simplemente se envían a un motor de análisis. Por ejemplo, la telemetría de temperatura de un dispositivo que se almacena para su posterior análisis es un mensaje de punto de datos.

Puesto que Centro de IoT expone un punto de conexión compatible con los [Centros de eventos] para recibir mensajes de dispositivo a nube, este tutorial usa una instancia de [EventProcessorHost][lnk-event-hubs]. Esta instancia:

* Almacena de manera confiable mensajes de *punto de datos* en Azure Blob Storage.
* Reenvía mensajes de dispositivo a nube *interactivos* a una [cola del Bus de servicio] para su procesamiento inmediato.

El Bus de servicio ayuda a asegurar un procesamiento confiable de mensajes interactivos, ya que ofrece puntos de comprobación de cada mensaje y desduplicación basada en periodos de tiempo.

> [AZURE.NOTE] Una instancia de **EventProcessorHost** es solamente una de las formas de procesar los mensajes interactivos. Otras opciones incluyen [Azure Service Fabric][lnk-service-fabric] y [Análisis de transmisiones de Azure][lnk-stream-analytics].

Al final de este tutorial, ejecutará tres aplicaciones de consola de Java:

* **simulated-device**, una versión modificada de la aplicación creada en el tutorial [Introducción a IoT Hub], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos. Esta aplicación usa el protocolo AMQPS para comunicarse con el Centro de IoT.
* **process-d2c-messages** usa la clase [EventProcessorHost] para recuperar mensajes desde el punto de conexión compatible con Event Hubs. A continuación, almacena los mensajes de punto de datos de forma confiable en Azure Blob Storage y envía mensajes interactivos a una cola de Service Bus.
* **process-interactive-messages** quita los mensajes interactivos de la cola de Service Bus.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para obtener instrucciones sobre cómo reemplazar el dispositivo simulado de este tutorial con un dispositivo físico y sobre cómo conectar dispositivos a IoT Hub, consulte el [Centro para desarrolladores de Azure IoT].

Este tutorial se puede aplicar directamente a otras formas de consumir mensajes compatibles con Centros de eventos como, por ejemplo, proyectos de [HDInsight (Hadoop)]. Consulte la [Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube] para más información.

Para completar este tutorial, necesitará lo siguiente:

+ Una versión funcional y completa del tutorial [Introducción a IoT Hub].

+ Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.

+ Maven 3. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Maven para este tutorial en Windows o Linux.

+ Una cuenta de Azure activa. <br/>Si no tiene una suscripción de Azure, puede crear una [cuenta gratis](https://azure.microsoft.com/free/) en tan solo unos minutos.

También se dan por sentados ciertos conocimientos sobre [Almacenamiento de Azure] y [Bus de servicio de Azure].


## Envío de mensajes interactivos desde un dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción a IoT Hub] para enviar mensajes de dispositivo a nube interactivos a IoT Hub.

1. Con un editor de texto, abra el archivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java. Este archivo contiene el código para la aplicación **simulated-device** que creó en el tutorial [Introducción a IoT Hub].

2. Agregue la siguiente clase anidada a la clase **App**:

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Esta clase es similar a la clase **MessageSender** del proyecto **simulated-device**. Las únicas diferencias son que ahora define la propiedad del sistema **MessageId** y una propiedad personalizada llamada **messageType**. El código asigna un identificador único universal (UUID) a la propiedad **MessageId**. Service Bus puede utilizar este identificador para desduplicar los mensajes que recibe. En el ejemplo se usa la propiedad **messageType** para distinguir los mensajes interactivos de los mensajes de punto de datos. La aplicación pasa esta información en las propiedades del mensaje, no en el cuerpo, para que el procesador de eventos no tenga que deserializar el mensaje para realizar su enrutamiento.

    > [AZURE.NOTE] Es importante crear el valor de **MessageId** usado para desduplicar los mensajes interactivos en el código de dispositivo. Las comunicaciones de red intermitentes, u otros errores, podrían provocar varias retransmisiones del mismo mensaje desde ese dispositivo. También puede utilizar un identificador de mensaje semántico, como un hash de los campos de datos relevantes del mensaje, en lugar de un UUID.

3. Modifique el método **main** para enviar mensajes interactivos y de punto de datos, tal y como se muestra en el siguiente fragmento de código:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Guarde y cierre el archivo simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java.

    > [AZURE.NOTE] Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, debe implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

5. Para compilar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```
    mvn clean package -DskipTests
    ```

## Procesamiento de mensajes de dispositivo a la nube

En esta sección, creará una aplicación de consola de Java que procesa los mensajes de dispositivo a nube desde IoT Hub. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ] que permite que una aplicación lea los mensajes de dispositivo a nube. En este tutorial se utiliza la clase [EventProcessorHost] para procesar estos mensajes en una aplicación de consola. Para más información sobre cómo procesar los mensajes de los Centros de eventos, consulte el tutorial [Introducción a los Centros de eventos].

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

1. Haga clic en la opción **Nuevo** de la esquina inferior izquierda. Después, haga clic en **Servicios de aplicaciones** > **Bus de servicio** > **Cola** > **Creación personalizada**. Escriba el nombre **d2ctutorial**, seleccione una región y use un espacio de nombres existente o cree uno nuevo. Tome nota del nombre del espacio de nombres; lo necesitará más adelante en este tutorial. En la siguiente página, seleccione **Habilitar detección de duplicados**, y establezca el valor de **Período de tiempo de historial de detección de duplicados** en una hora. Haga clic en la marca de verificación de la esquina inferior derecha para guardar la configuración de la cola.

    ![Creación de colas en el Portal de Azure][30]

2. En la lista de colas del Bus de servicio, haga clic en **d2ctutorial** y luego en **Configurar**. Cree dos directivas de acceso compartido, una llamada **enviar** con permisos de **envío** y otra llamada **escuchar** con permisos de **escucha**. Tome nota de los valores de **Clave principal** de las dos directivas; los necesitará más adelante en este tutorial. Cuando haya terminado, haga clic en **Guardar** de la parte inferior.

    ![Configuración de colas en el Portal de Azure][31]

### Creación del procesador de eventos

En esta sección, creará una aplicación de Java para procesar los mensajes desde el punto de conexión compatible con Event Hubs.

La primera tarea consiste en agregar un proyecto de Maven llamado **process-d2c-messages**, que recibe mensajes de dispositivo a la nube del punto de conexión compatible con Event Hubs de IoT Hub y enruta los mensajes a otros servicios back-end.

1. En la carpeta iot-java-get-started creada en la sección [Introducción a IoT Hub], cree un proyecto de Maven llamado **process-d2c-messages** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta process-d2c-messages.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta process-d2c-messages y agregue la siguiente dependencia al nodo **dependencies**. Estas dependencias permiten usar los paquetes azure-eventhubs, azure-eventhubs-eph y azure-servicebus a la aplicación para interactuar con su centro de IoT y la cola Service Bus:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

La siguiente tarea consiste en agregar una clase **ErrorNotificationHandler** al proyecto.

1. Use un editor de texto para crear un archivo process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\ErrorNotificationHandler.java. Agregue el código siguiente al archivo para mostrar los mensajes de error de la instancia de **EventProcesssorHost**:

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Guarde y cierre el archivo ErrorNotificationHandler.java.

Ahora puede agregar una clase que implementa la interfaz **IEventProcessor**. La clase **EventProcessorHost** llama a esta clase para procesar los mensajes del dispositivo a la nube recibidos del Centro de IoT. El código de esta clase implementa la lógica para almacenar los mensajes de manera confiable en un contenedor de blobs y reenviar los mensajes interactivos a la cola del Bus de servicio.

El método **onEvents** establece la variable **latestEventData**, que supervisa el número de secuencia y el desplazamiento de los mensajes más recientes leídos por este procesador de eventos. Recuerde que cada procesador es responsable de una sola partición. A continuación, el método **onEvents** recibe un lote de mensajes de IoT Hub y los procesa del modo siguiente: envía mensajes interactivos a la cola de Service Bus y anexa mensajes de punto de datos al búfer de memoria **toAppend**. Si el búfer de memoria alcanza el límite de bloque de 4 MB o si transcurre el intervalo de tiempo de desduplicación (en este tutorial, una hora desde el último punto de control), la aplicación desencadenará un punto de control.

El método **AppendAndCheckpoint** genera primero un valor de **blockid** para el bloque que se va a anexar al blob. Azure Storage requiere que todos los identificadores de bloque tengan la misma longitud, por lo que el método rellena el desplazamiento con ceros iniciales. Luego, si ya existe un bloque con este identificador en el blob, el método lo sobrescribe con el contenido actual del búfer.

> [AZURE.NOTE] Para simplificar el código, en este tutorial se usa un solo archivo de blob por partición para almacenar los mensajes. En una solución real, los archivos se implementarían gradualmente, de modo que se crean archivos adicionales cuando alcanzan un determinado tamaño. Recuerde que el blob en bloques de Azure puede tener como máximo 195 GB de datos.

La siguiente tarea consiste en implementar la interfaz **IEventProcessor**:

1. Use un editor de texto para crear un archivo process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\EventProcessor.java.

2. Agregue las siguientes instrucciones de importación y la definición de clase al archivo EventProcessor.java. La clase **EventProcessor** implementa la interfaz **IEventProcessor** que define el comportamiento del cliente de Event Hubs:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Agregue los siguientes métodos a la clase **EventProcessor** para implementar la interfaz **IEventProcessor**:

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Agregue las siguientes variables de nivel de clase a la clase **EventProcessor**:

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Agregue un método **AppendAndCheckPoint** con la firma siguiente a la clase **EventProcessor**:

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Agregue el código siguiente al método **AppendAndCheckPoint** para obtener el número de secuencia y el desplazamiento de mensajes actual de la partición:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. En el método **AppendAndCheckPoint**, use el valor de desplazamiento actual para crear una instancia **BlockEntry** para el bloque siguiente que se guardará en el blob:

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. En el método **AppendAndCheckPoint**, cargue el último conjunto de mensajes en el blob en bloques y recupere la lista de bloques:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. En el método **AppendAndCheckPoint**, cree el bloque inicial en un blob nuevo o anexe el bloque al blob existente:

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Por último, en el método **AppendAndCheckPoint**, cree un punto de control en la partición y prepárese para guardar el siguiente bloque de mensajes:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. En el método **onEvents**, agregue el siguiente código para recibir mensajes del punto de conexión de IoT Hub y reenviar mensajes interactivos a la cola de Service Bus. A continuación, llame al método **AppendAndCheckPoint** cuando el bloque esté lleno o se agote el tiempo de espera:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Por último, en el método **onEvents**, agregue una cláusula 'else if' para llamar a **AppendAndCheckPoint** si el tiempo de espera se agota y no hay ningún mensaje procedente de IoT Hub:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Guarde los cambios en el archivo EventProcessor.java.

La tarea final del proyecto **process-d2c-messages** consiste en agregar código al método **main**, que crea una instancia de **EventProcessorHost**.

1. Con un editor de texto, abra el archivo process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Agregue la siguiente variable de nivel de clase a la clase **App**. Reemplace **{yourstorageaccountconnectionstring}** por la cadena de conexión de la cuenta de Azure Storage que anotó anteriormente en la sección [Aprovisionamiento de una cuenta de Azure Storage y una cola de Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Agregue las siguientes variables de nivel de clase a la clase **App** y reemplace **{yourservicebusnamespace}** por el espacio de nombres de Service Bus y **{yourservicebussendkey}** por la clave **send** de la cola. Anteriormente anotó el espacio de nombres y los valores de la clave **listen** en la sección [Aprovisionamiento de una cuenta de Azure Storage y una cola de Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Agregue las siguientes variables de nivel de clase a la clase **App**. Reemplace **{youreventhubcompatibleendpoint}** por el nombre del punto de conexión compatible con Event Hubs. El nombre del punto de conexión es similar a **ihs...namespace**, por lo que deberá quitar el prefijo **sb://** y el sufijo **.servicebus.windows.net/**. Reemplace **{youreventhubcompatiblename}** por el nombre compatible con Event Hubs. Reemplace **{youriothubkey}** por la clave **iothubowner**. Tomó nota de estos valores en la sección [Creación de un centro de IoT][lnk-create-an-iot-hub] en el tutorial *Introducción a Azure IoT Hub para Java*:

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modifique la signatura del método **main** de la siguiente manera:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Agregue el código siguiente al método **main** para obtener una referencia al contenedor de blobs que almacena los mensajes:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Agregue el código siguiente al método **main** para obtener una referencia al servicio Service Bus:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. En el método **main**, configure y cree una instancia de **EventProcessorHost**. La opción **setInvokeProcessorAfterReceiveTimeout** garantiza que la instancia de **EventProcessorHost** invocará el método **onEvents** en la interfaz **IEventProcessor** aunque no haya mensajes para procesar. Después, el método **onEvents** invoca siempre el método **AppendAndCheckPoint** cuando el tiempo de espera se agota.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. En el método **main**, registre la implementación de **IEventProcessor** con la instancia de **EventProcessorHost**:

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Por último, agregue al método **main** lógica para apagar la instancia de **EventProcessorHost**:

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Guarde y cierre el archivo process-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

13. Para compilar la aplicación **process-d2c-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema, en la carpeta process-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```

## Recepción de mensajes interactivos

En esta sección, escribirá una aplicación de consola de Java que recibe los mensajes interactivos de la cola de Service Bus.

La primera tarea consiste en agregar un proyecto de Maven llamado **process-interactive-messages** que recibe los mensajes enviados en la cola de Service Bus desde las instancias de **EventProcessor**.

1. En la carpeta iot-java-get-started creada en la sección [Introducción a IoT Hub], cree un proyecto de Maven llamado **process-interactive-messages** mediante el comando siguiente en el símbolo del sistema. Observe que este es un comando único y largo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, vaya a la nueva carpeta process-interactive-messages.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta process-interactive-messages y agregue la siguiente dependencia al nodo **dependencies**. Esta dependencia permite usar el paquete azure-servicebus en la aplicación para interactuar con la cola de Service Bus:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

La siguiente tarea consiste en agregar código para recuperar mensajes de la cola de Service Bus.

1. Con un editor de texto, abra el archivo process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

2. Agregue las siguientes instrucciones `import` al archivo:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Agregue las siguientes variables de nivel de clase a la clase **App** y reemplace **{yourservicebusnamespace}** por el espacio de nombres de Service Bus y **{yourservicebuslistenkey}** por la clave **listen** de la cola. Anteriormente anotó el espacio de nombres y los valores de la clave **listen** en la sección [Aprovisionamiento de una cuenta de Azure Storage y una cola de Service Bus](#provision-an-azure-storage-account-and-a-service-bus-queue):

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Agregue la siguiente clase anidada a la clase **App** para recibir mensajes de la cola:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modifique la signatura del método **main** de la siguiente manera:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. En el método **main**, agregue el código siguiente para empezar a escuchar mensajes nuevos:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Guarde y cierre el archivo process-interactive-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

8. Para compilar la aplicación **process-interactive-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta process-interactive-messages:

    ```
    mvn clean package -DskipTests
    ```

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las tres aplicaciones.

1.	Para ejecutar la aplicación **process-interactive-messages**, en un símbolo del sistema o en el shell, vaya a la carpeta process-interactive-messages y ejecute el siguiente comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ejecución de process-interactive-messages][processinteractive]

2.	Para ejecutar la aplicación **process-d2c-messages**, en un símbolo del sistema o en el shell, vaya a la carpeta process-d2c-messages y ejecute el siguiente comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ejecución de process-d2c-messages][processd2c]

3.	Para ejecutar la aplicación **simulated-device**, en un símbolo del sistema o en el shell, vaya a la carpeta simulated-device y ejecute el siguiente comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ejecución de simulated-device][simulateddevice]

> [AZURE.NOTE] Para ver las actualizaciones en el archivo de blob, debe reducir la constante **MAX\_BLOCK\_SIZE** de la clase **StoreEventProcessor** a un valor inferior, como **1024**. Este cambio resulta útil ya que se tarda algún tiempo en alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con un tamaño de bloque menor no tiene que esperar tanto tiempo para ver el blob que se crea y se actualiza. Aunque un tamaño de bloque mayor hace que la aplicación sea más escalable.

## Pasos siguientes

En este tutorial, ha aprendido a procesar de manera confiable mensajes de dispositivo a nube interactivos y de punto de datos mediante la clase [EventProcessorHost].

El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-c2d] muestra cómo enviar mensajes a los dispositivos desde el back-end.

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT][lnk-suite].

Para más información sobre cómo desarrollar soluciones con el Centro de IoT, consulte la [Guía del desarrollador del Centro de IoT de Azure].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Almacenamiento de blobs de Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Data Factory de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[cola del Bus de servicio]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube]: iot-hub-devguide.md#d2c

[Almacenamiento de Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/
[documentación del Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guía del desarrollador del Centro de IoT de Azure]: iot-hub-devguide.md
[Introducción a IoT Hub]: iot-hub-java-java-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-java-java-getstarted.md
[Centro para desarrolladores de Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Acerca de las cuentas de almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introducción a los Centros de eventos]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[instrucciones de escalabilidad de Almacenamiento de Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Centros de eventos ]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Centros de eventos]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Event Hubs Programming Guide]: https://github.com/Azure/azure-event-hubs/blob/master/java/readme.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

<!---HONumber=AcomDC_0928_2016-->