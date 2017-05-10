---
title: "Introducción a las API estándar de .NET de Azure Event Hubs | Microsoft Docs"
description: "Introducción a la API estándar de .NET"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: a173f8e4-556c-42b8-b856-838189f7e636
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: jotaub
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 4e05b1cc41038b2239f9314c17b93d20eed33844
ms.contentlocale: es-es
ms.lasthandoff: 04/18/2017

---

# <a name="event-hubs-net-standard-api-overview"></a>Introducción a la API estándar de .NET de Event Hubs
Este artículo resume algunas de las API de cliente estándar de .NET de Event Hubs. Actualmente existen dos bibliotecas de cliente estándar .NET:
* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
  *  Esta biblioteca proporciona todas las operaciones básicas en tiempo de ejecución.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
  * Esta biblioteca agrega una funcionalidad adicional que permite realizar el seguimiento de los eventos procesados y es la manera más fácil de leer de un centro de eventos.

## <a name="event-hub-client"></a>Cliente de centro de eventos
[**EventHubClient**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) es el objeto principal que se usa para enviar eventos, crear destinatarios y obtener información de tiempo de ejecución. Este cliente está vinculado a un centro de eventos determinado y crea una nueva conexión con el punto de conexión de Event Hubs.

### <a name="create-an-event-hub-client"></a>Creación de un cliente de centro de eventos
Se crea un objeto [**EventHubClient**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) a partir de una cadena de conexión. Puede ver la manera más sencilla de crear una instancia de un nuevo cliente en el ejemplo siguiente:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("{Event Hub connection string}");
```

Para modificar mediante programación la cadena de conexión, puede usar la clase [**EventHubsConnectionStringBuilder**](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) y pasar la cadena de conexión como un parámetro a [**EventHubClient.CreateFromConnectionString**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("{Event Hub connection string}")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Envío de eventos
Para enviar eventos a un centro de eventos, use la clase [**EventData**](/dotnet/api/microsoft.azure.eventhubs.eventdata). El cuerpo debe ser una matriz `byte` o un segmento de matriz `byte`.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Recepción de eventos
La manera recomendada de recibir eventos desde Event Hubs es mediante la clase [**EventProcessorHost**](##Event-Processor-Host-APIs), que proporciona una funcionalidad que permite realizar automáticamente un seguimiento del desplazamiento y de la información de la partición. Sin embargo, hay algunas situaciones en las que puede que desee utilizar la flexibilidad de la biblioteca básica de Event Hubs para recibir eventos.

#### <a name="create-a-receiver"></a>Creación de un receptor
Los receptores están vinculados a particiones específicas por lo que para recibir todos los eventos de un centro de eventos, deberá crear varias instancias. Por lo general, se recomienda obtener la información de partición mediante programación, en lugar de codificar de forma rígida los identificadores de esta. Para ello, puede usar el método [**GetRuntimeInformationAsync**](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync).

```csharp

// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition ids
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Dado que los eventos no se quitan nunca de un centro de eventos (solo expiran), deberá especificar el punto de partida adecuado. En el ejemplo siguiente se muestran posibles combinaciones.

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant 'PartitionReceiver.EndOfStream' will only receive all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Consumo de un evento
```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}        
```

## <a name="event-processor-host-apis"></a>API de host del procesador de eventos
Estas API ofrecen resistencia a los procesos de trabajo que pueden dejar de estar disponibles, distribuyendo particiones entre trabajos disponibles.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{Event Hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes of the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

La siguiente es una implementación de ejemplo de [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

```csharp
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
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de los escenarios de los centros de eventos, visite estos vínculos:

* [¿Qué es Centros de eventos de Azure?](event-hubs-what-is-event-hubs.md)
* [API disponibles de Event Hubs](event-hubs-api-overview.md)

A continuación se incluyen referencias de API de .NET:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
