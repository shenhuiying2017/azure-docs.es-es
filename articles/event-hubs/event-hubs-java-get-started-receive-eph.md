---
title: Recepción de eventos desde Azure Event Hubs mediante Java | Microsoft Docs
description: Introducción a la recepción de eventos desde Event Hubs mediante Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: bf87bed80c142bce6229ad858a33a1c6ede63a23
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Recepción de eventos desde Azure Event Hubs mediante Java

Event Hubs es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en Event Hubs, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para más información, consulte [Información general de Event Hubs][Event Hubs overview].

Este tutorial muestra cómo recibir eventos en un centro de eventos mediante una aplicación de consola escrita en Java.

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, debe cumplir los siguientes requisitos previos:

* Un entorno de desarrollo de Java. En este tutorial, se da por hecho que se va a trabajar con [Eclipse](https://www.eclipse.org/).
* Una cuenta de Azure activa. Si no tiene una suscripción a Azure, cree una [cuenta gratuita][] antes de empezar.

El código de este tutorial se basa en el [código EventProcessorSample en GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que se puede examinar para ver toda la aplicación en funcionamiento.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recepción de mensajes con EventProcessorHost en Java

**EventProcessorHost** es una clase de Java que simplifica la recepción de eventos desde Event Hubs mediante la administración de puntos de control persistentes y recepciones paralelas desde tales Centros de eventos. Con EventProcessorHost, puede dividir eventos entre varios destinatarios, incluso cuando están hospedados en distintos nodos. Este ejemplo muestra cómo usar EventProcessorHost para un solo destinatario.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

Para usar EventProcessorHost, debe tener una [cuenta de Azure Storage][Azure Storage account]:

1. Inicie sesión en [Azure Portal][Azure portal] y haga clic en **+ Crear un recurso** en la parte izquierda de la pantalla.
2. Haga clic en **Storage** y luego en **Cuenta de Storage**. En la ventana **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento. Complete el resto de los campos, seleccione la región que desee y, finalmente, haga clic en **Crear**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Haga clic en la cuenta de almacenamiento recién creada y, a continuación, en **Claves de acceso**:
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Copie el valor key1 en una ubicación temporal para utilizarla más adelante en este tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Creación de un proyecto de Java mediante EventProcessorHost

La biblioteca de cliente de Java para Event Hubs está disponible para su uso en proyectos de Maven en el [repositorio central de Maven][Maven Package], y se puede hacer referencia a ella mediante la siguiente declaración de dependencia en el archivo de proyecto de Maven. La versión actual es 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>1.0.0</version>
</dependency>
```

Para distintos tipos de entornos de compilación, puede obtener explícitamente los últimos archivos JAR publicados del [repositorio central de Maven][Maven Package].  

1. Para el ejemplo siguiente, primero cree un nuevo proyecto de Maven para una aplicación de consola o shell en su entorno de desarrollo de Java favorito. La clase se denomina `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Cree una clase nueva denominada `EventProcessorSample`con el código siguiente. Reemplace los siguientes marcadores de posición por los valores que usó al crear el centro de eventos y la cuenta de almacenamiento:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Cree una clase final más llamada `EventProcessor`, con el código siguiente:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. In a real implementation, this
        // is the place to do initialization so that events can be processed when they arrive, such as opening a database
        // connection.
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. The reason argument indicates whether the shut down
        // is because another host has stolen the lease for this partition or due to error or host shutdown. In a real implementation,
        // this is the place to do cleanup for resources that were opened in onOpen.
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        // It is NOT called for exceptions thrown out of onOpen/onClose/onEvents. EventProcessorHost is responsible for recovering from
        // the error, if possible, or shutting the event processor down if not, in which case there will be a call to onClose. The
        // notification provided to onError is primarily informational.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. The maximum number of events in a batch
        // can be controlled via EventProcessorOptions. Also, if the "invoke processor after receive timeout" option is set to true,
        // this method will be called with null when a receive timeout occurs.
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                // It is important to have a try-catch around the processing of each event. Throwing out of onEvents deprives
                // you of the chance to process any remaining events in the batch. 
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. Checkpointing is usually not a fast operation, so there is a tradeoff
                    // between checkpointing frequently (to minimize the number of events that will be reprocessed after a crash, or
                    // if the partition lease is stolen) and checkpointing infrequently (to reduce the impact on event processing
                    // performance). Checkpointing every five events is an arbitrary choice for this sample.
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

> [!NOTE]
> Este tutorial usa una sola instancia de EventProcessorHost. Para aumentar la capacidad de procesamiento, se recomienda ejecutar varias instancias de EventProcessorHost, preferiblemente en máquinas independientes.  Esto proporciona redundancia también. En esos casos, las diferentes instancias se coordinan automáticamente entre sí con el fin de equilibrar la carga de los eventos recibidos. Si desea que varios destinatarios procesen *todos* los eventos, debe usar el concepto **ConsumerGroup** . Cuando se reciben eventos de distintos equipos, puede ser útil especificar nombres para las instancias de EventProcessorHost según los equipos (o roles) en que se implementan.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[cuenta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio