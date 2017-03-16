---
title: "Recepción de eventos desde Azure Event Hubs mediante .NET Standard | Microsoft Docs"
description: "Introducción a la recepción de mensajes con EventProcessorHost en .NET Standard"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 8f0870c98f625a56cfddacd91b401206293122f1
ms.lasthandoff: 03/01/2017

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>Introducción a la recepción de mensajes con EventProcessorHost en .NET Standard

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver).

## <a name="what-will-be-accomplished"></a>Lo que se logrará

Este tutorial muestra cómo crear la solución existente **SampleEphReceiver** (dentro de esta carpeta). Puede ejecutar la solución tal cual está, reemplazando las cadenas `EhConnectionString`, `EhEntityPath` y `StorageAccount` por los valores del centro de eventos y de la cuenta de almacenamiento, o seguir este tutorial para crear la suya propia.

En este tutorial, se escribirá una aplicación de consola de .NET Core para recibir mensajes de un centro de eventos mediante **EventProcessorHost**.

## <a name="prerequisites"></a>Requisitos previos

1. [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com).

2. [Utillaje de .NET Core Visual Studio 2015 0 2017](http://www.microsoft.com/net/core).

3. Una suscripción de Azure.

4. Un espacio de nombres de Event Hubs.
    
## <a name="receive-messages-from-the-event-hub"></a>Recepción de mensajes del centro de eventos

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

1. Inicie Visual Studio y cree una aplicación de consola de .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Agregue el paquete NuGet de Event Hubs.

* Agregue los siguientes paquetes de NuGet al proyecto:
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

### <a name="implement-the-ieventprocessor-interface"></a>Implemente la interfaz de IEventProcessor.

1. Cree una nueva clase denominada "SimpleEventProcessor".

2. Agregue las siguientes instrucciones `using` en la parte superior del archivo SimpleEventProcessor.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. Implemente la interfaz `IEventProcessor`. La clase debería tener el siguiente aspecto:

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>Escritura de un método de consola principal que utilice `SimpleEventProcessor` para recibir mensajes de un centro de eventos

1. Agregue las siguientes instrucciones `using` al principio del archivo Program.cs.
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Agregue constantes a la clase `Program` para la cadena de conexión de Event Hubs, la ruta de acceso del centro de eventos, el nombre del contenedor de almacenamiento, y el nombre y la clave de la cuenta de almacenamiento. Reemplace los marcadores de posición por sus valores correspondientes.

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Agregue un nuevo método denominado `MainAsync` a la clase `Program` de manera similar a la siguiente:
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Agregue la siguiente línea de código al método `Main`:

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Este es el aspecto que debería tener el archivo Program.cs:

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. Ejecute el programa y asegúrese de que no hay ningún error.
  
¡Enhorabuena! Ahora ha recibido mensajes de un centro de eventos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
