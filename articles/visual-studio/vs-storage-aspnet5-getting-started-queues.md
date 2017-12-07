---
title: "Introducción a Queue Storage y los servicios conectados de Visual Studio (ASP.NET Core) | Microsoft Docs"
description: "Introducción al uso de Azure Queue Storage en un proyecto de ASP.NET Core en Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: d83ad26e4f96e1a7670c5212b7e9ca8182b7cec4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Introducción a Queue Storage y a los servicios conectados de Visual Studio (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

En este artículo se describe cómo empezar a usar Azure Queue Storage en Visual Studio después de crear una cuenta de Azure Storage en un proyecto de ASP.NET Core mediante la característica **Servicios conectados** de Visual Studio. La operación **Servicios conectados** instala los paquetes de NuGet adecuados para acceder a Azure Storage en el proyecto y agrega la cadena de conexión de la cuenta de almacenamiento a los archivos de configuración del proyecto. (Vea [Documentación sobre Storage](https://azure.microsoft.com/documentation/services/storage/) para información general sobre Azure Storage).

El almacenamiento de cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Un único mensaje en cola puede tener un tamaño de hasta 64 kilobytes (KB) y una cola puede contener millones de mensajes, hasta el límite de capacidad total de una cuenta de almacenamiento. Vea también [Introducción a Azure Queue Storage mediante .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para más información sobre la manipulación de colas mediante programación.

Para comenzar, primero debe crear una cola de Azure en la cuenta de almacenamiento. En este artículo también se explica cómo crear una cola en C# y cómo realizar operaciones básicas de cola, como agregar, modificar, leer y quitar mensajes de cola.  El código usa la biblioteca cliente de Azure Storage para .NET. Para obtener más información acerca de ASP.NET, consulte [ASP.NET](http://www.asp.net).

Algunas de las API de Azure Storage son asincrónicas, y el código de este artículo asume que se usan métodos asincrónicos. Vea [Programación asincrónica](https://docs.microsoft.com/dotnet/csharp/async) para más información.

## <a name="access-queues-in-code"></a>Acceso a colas en el código

Para acceder a las colas de los proyectos de ASP.NET Core, incluya los elementos siguientes en los archivos de origen de C# que accedan a Azure Queue Storage. Use todo este código delante del código que aparece en las secciones siguientes.

1. Agregue las instrucciones `using` necesarias:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Obtenga un objeto `CloudStorageAccount` que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Obtenga un objeto `CloudQueueClient` para hacer referencia a los objetos de cola en la cuenta de almacenamiento:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Obtenga un objeto `CloudQueue` para hacer referencia a una cola específica:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Creación de una cola en código

Para crear la cola de Azure en el código, llame a "CreateIfNotExistsAsync".

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>un mensaje a una cola

Para insertar un mensaje en una cola existente, cree un objeto `CloudQueueMessage` y luego llame al método `AddMessageAsync`. Se puede crear un objeto `CloudQueueMessage` a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Leer un mensaje de una cola

Puede inspeccionar el mensaje situado en la parte delantera de una cola, sin quitarlo de la cola, mediante una llamada al método `PeekMessageAsync`:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Leer y eliminar un mensaje de una cola

Su código puede quitar un mensaje de una cola en dos pasos.

1. Llame a `GetMessageAsync` para obtener el siguiente mensaje en una cola. Un mensaje devuelto por `GetMessageAsync` se hace invisible a cualquier otro código de lectura de mensajes de esta cola. De forma predeterminada, este mensaje permanece invisible durante 30 segundos.
1. Para terminar de quitar el mensaje de la cola, llame a `DeleteMessageAsync`.

Este proceso de extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código siguiente llama a `DeleteMessageAsync` justo después de haber procesado el mensaje:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Opciones adicionales para quitar mensajes de la cola

Hay dos formas de personalizar la recuperación de mensajes de una cola. En primer lugar, puede obtener un lote de mensajes (hasta 32). En segundo lugar, puede establecer un tiempo de espera de la invisibilidad más largo o más corto para que el código disponga de más o menos tiempo para procesar cada mensaje. El siguiente ejemplo de código utiliza el método `GetMessages` para obtener veinte mensajes en una llamada. A continuación, procesa cada mensaje con un bucle `foreach`. También establece el tiempo de espera de la invisibilidad en cinco minutos para cada mensaje. Tenga en cuenta que el temporizador de cinco minutos empieza a contar para todos los mensajes al mismo tiempo, por lo que, después de pasar los cinco minutos, todos los mensajes que no se han eliminado volverán a estar visibles.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>la longitud de la cola

Puede obtener una estimación del número de mensajes existentes en una cola. El método `FetchAttributes` solicita a Queue service la recuperación de los atributos de la cola, incluido el número de mensajes. La propiedad `ApproximateMethodCount` devuelve el último valor recuperado por el método `FetchAttributes`, sin llamar a Queue service.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Uso del patrón Async-Await con API de cola comunes

En este ejemplo se muestra cómo usar el patrón Async-Await con API de cola comunes que terminan con `Async`. Cuando se usa un método asincrónico, el patrón Async-Await suspende la ejecución local hasta que se complete la llamada. Este comportamiento permite que el subproceso actual realice otro trabajo, lo que ayuda a evitar cuellos de botella en el rendimiento y mejora la capacidad de respuesta general de la aplicación.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Eliminación de una cola

Para eliminar una cola y todos los mensajes contenidos en ella, llame al método `Delete` en el objeto de cola:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
