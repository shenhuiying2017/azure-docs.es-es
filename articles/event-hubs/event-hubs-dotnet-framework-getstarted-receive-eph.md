---
title: "Recepción de eventos desde Azure Event Hubs mediante .NET Framework | Microsoft Docs"
description: Siga este tutorial para recibir eventos desde Azure Event Hubs mediante .NET Framework.
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: fe331199333d492dbc42c9125c9da96a44066ee1
ms.openlocfilehash: 4a3656d09f216e5c895bac5d7d680b44d76f4df8


---
# <a name="get-started-with-event-hubs-using-the-net-framework"></a>Introducción a Event Hubs mediante .NET Framework

## <a name="receive-messages-with-eventprocessorhost"></a>Recepción de mensajes con EventProcessorHost
[EventProcessorHost][EventProcessorHost] es una clase de .NET que simplifica la recepción de eventos desde Event Hubs mediante la administración de puntos de control persistentes y recepciones paralelas desde dichas instancias de Event Hubs. Con [EventProcessorHost][EventProcessorHost], puede dividir eventos entre varios destinatarios, incluso cuando están hospedados en distintos nodos. Este ejemplo muestra cómo usar [EventProcessorHost][EventProcessorHost] para un solo destinatario. En el ejemplo de [Scale out Event Processing with Event Hubs][Scale out Event Processing with Event Hubs] (Escalado horizontal del procesamiento de eventos) se muestra cómo usar [EventProcessorHost][EventProcessorHost] con varios destinatarios.

Para usar [EventProcessorHost][EventProcessorHost], debe tener una [cuenta de Azure Storage][Azure Storage account]:

1. Inicie sesión en [Azure Portal][Azure portal] y haga clic en **Nuevo** en la parte superior izquierda de la pantalla.
2. Haga clic en **Storage** y luego en **Cuenta de Storage**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. En la hoja **Crear cuenta de almacenamiento** , escriba un nombre para la cuenta de almacenamiento. Elija una suscripción de Azure, el grupo de recursos y la ubicación en la que se va a crear el recurso. A continuación, haga clic en **Crear**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. En la lista de cuentas de almacenamiento, haga clic en la cuenta de almacenamiento recién creada.
5. En la hoja de la cuenta de almacenamiento, haga clic en **Claves de acceso**. Copie el valor de **key1** para usarla más adelante en este tutorial.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)
6. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **Receptor**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
7. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes NuGet para la solución**.
8. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Asegúrese de que el nombre del proyecto (**Receiver**) se especifica en el cuadro **Versiones**. Haga clic en **Instalar**y acepte las condiciones de uso.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio descarga, instala y agrega una referencia al [Centro de eventos del Bus de servicio de Azure - Paquete de NuGet de EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.
9. Haga clic con el botón derecho en el proyecto **Receptor**, haga clic en **Agregar** y, después, haga clic en **Clase**. Asigne a la nueva clase el nombre **SimpleEventProcessor** y después haga clic en **Agregar** para crear la clase.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
10. Agregue las siguientes instrucciones en la parte superior del archivo SimpleEventProcessor.cs:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     using System.Diagnostics;
     ```
    
     A continuación, sustituya el código siguiente por el cuerpo de la clase:
    
     ```csharp
     class SimpleEventProcessor : IEventProcessor
     {
         Stopwatch checkpointStopWatch;
    
         async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
         {
             Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
             if (reason == CloseReason.Shutdown)
             {
                 await context.CheckpointAsync();
             }
         }
    
         Task IEventProcessor.OpenAsync(PartitionContext context)
         {
             Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
             this.checkpointStopWatch = new Stopwatch();
             this.checkpointStopWatch.Start();
             return Task.FromResult<object>(null);
         }
    
         async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
         {
             foreach (EventData eventData in messages)
             {
                 string data = Encoding.UTF8.GetString(eventData.GetBytes());
    
                 Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                     context.Lease.PartitionId, data));
             }
    
             //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
             if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
             {
                 await context.CheckpointAsync();
                 this.checkpointStopWatch.Restart();
             }
         }
     }
     ```
    
     **EventProcessorHost** llamará a esta clase para procesar los eventos recibidos del Centro de eventos. Tenga en cuenta que la clase `SimpleEventProcessor` usa un cronómetro para llamar periódicamente al método de punto de control en el contexto **EventProcessorHost** . Esto garantiza que, si se reinicia el destinatario, no perderá más de cinco minutos de trabajo de procesamiento.
11. En la clase **Program.cs**, agregue la siguiente instrucción `using` al principio del archivo:
    
     ```csharp
     using Microsoft.ServiceBus.Messaging;
     ```
    
     Después, reemplace el método `Main` de la clase `Program` con el código siguiente, sustituyendo el nombre del Centro de eventos y la cadena de conexión de nivel del espacio de nombres que ha guardado anteriormente, y la cuenta de almacenamiento y la clave que ha copiado en las secciones anteriores. 
    
     ```csharp
     static void Main(string[] args)
     {
       string eventHubConnectionString = "{Event Hub connection string}";
       string eventHubName = "{Event Hub name}";
       string storageAccountName = "{storage account name}";
       string storageAccountKey = "{storage account key}";
       string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
    
       string eventProcessorHostName = Guid.NewGuid().ToString();
       EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
       Console.WriteLine("Registering EventProcessor...");
       var options = new EventProcessorOptions();
       options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
       eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
    
       Console.WriteLine("Receiving. Press enter key to stop worker.");
       Console.ReadLine();
       eventProcessorHost.UnregisterEventProcessorAsync().Wait();
     }
     ```

> [!NOTE]
> Este tutorial usa una sola instancia de [EventProcessorHost][EventProcessorHost]. Para aumentar el rendimiento, se recomienda ejecutar varias instancias de [EventProcessorHost][EventProcessorHost], como se muestra en el ejemplo de [Scaled out event processing] (Escalado horizontal del procesamiento de eventos)[Scaled out event processing] (Escalado horizontal del procesamiento de eventos). En esos casos, las diferentes instancias se coordinan automáticamente entre sí con el fin de equilibrar la carga de los eventos recibidos. Si desea que varios destinatarios procesen *todos* los eventos, debe usar el concepto **ConsumerGroup** . Cuando se reciben eventos de distintos equipos, puede ser útil especificar nombres para las instancias de [EventProcessorHost][EventProcessorHost] según los equipos (o roles) en que se implementan. Para más información acerca de estos temas, consulte [Información general de Event Hubs][Event Hubs Overview] y [Guía de programación de Event Hubs][Event Hubs Programming Guide].
> 
> 

<!-- Links -->
[Event Hubs Overview]: event-hubs-overview.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede obtener más información visitando los siguientes vínculos:

* [Host del procesador de eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Información general de Event Hubs][Event Hubs overview]
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3


<!--HONumber=Feb17_HO1-->


