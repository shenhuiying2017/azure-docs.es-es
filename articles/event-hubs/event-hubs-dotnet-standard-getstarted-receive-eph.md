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
ms.date: 03/03/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7506430f4ec5522165274f05a2fd5b77e3d6252d
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Introducción a la recepción de mensajes con el Host del procesador de eventos en .NET Standard

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver).

En este tutorial se muestra cómo escribir una aplicación de consola de .NET Core que recibe mensajes de un centro de eventos mediante **EventProcessorHost**. Puede ejecutar la solución de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) tal cual está, reemplazando las cadenas por los valores del centro de eventos y de la cuenta de almacenamiento, o seguir los pasos de este tutorial para crear la suya propia. 

## <a name="prerequisites"></a>Requisitos previos

1. [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2015, pero también se admite Visual Studio 2017.
2. [Herramientas de .NET Core Visual Studio 2015 o 2017](http://www.microsoft.com/net/core).
3. Una suscripción de Azure.
4. Un espacio de nombres de Event Hubs.
5. Una cuenta de Almacenamiento de Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Crear un espacio de nombres de Event Hubs y un centro de eventos  
  
El primer paso consiste en usar [Azure Portal](https://portal.azure.com) para crear un espacio de nombres de tipo Event Hubs y obtener las credenciales de administración que la aplicación necesita para comunicarse con el centro de eventos. Para crear un espacio de nombres y un centro de eventos, siga el procedimiento de [este artículo](event-hubs-create.md) y después continúe con los pasos siguientes.  

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure  

1. Inicie sesión en [Azure Portal](https://portal.azure.com).  
2. En el panel de navegación izquierdo del portal, haga clic en **Nuevo**, luego en **Storage** y, a continuación, en **Cuenta de Storage**.  
3. Complete los campos de la hoja de la cuenta de almacenamiento y, luego, haga clic en **Crear**.
  
    ![][1]

4. Una vez que aparezca el mensaje **Implementaciones correctas**, haga clic en el nombre de la nueva cuenta de almacenamiento y, en la hoja **Essentials**, haga clic en **Blobs**. Cuando se abra la hoja **Blob service**, haga clic en **+ Container** (Contenedor +) en la parte superior. Asigne un nombre al contenedor y cierre la hoja **Blob service**.  
5. Haga clic en **Claves de acceso** en la hoja izquierda y copie el nombre del contenedor de almacenamiento, la cuenta de almacenamiento y el valor de **key1**. Guarde estos valores en el Bloc de notas, o en cualquier otra ubicación temporal.  
    
## <a name="create-a-console-application"></a>Creación de una aplicación de consola

1. Inicie Visual Studio. En el menú Archivo, haga clic en **Nuevo** y en **Proyecto**. Cree una aplicación de consola de .NET Core.

    ![][2]

2. En el Explorador de soluciones, haga doble clic en el archivo **project.json** para abrirlo en el editor de Visual Studio.
3. Agregue la cadena `"portable-net45+win8"` a la declaración de `"imports"`, en la sección `"frameworks`". Esa sección debería aparecer ahora de esta forma. Esta cadena es necesaria debido a la dependencia de Azure Storage de OData:

    ```json
    "frameworks": {
      "netcoreapp1.0": {
        "imports": [
          "dnxcore50",
          "portable-net45+win8"
        ]
      }
    }
    ```

4. En el menú Archivo, haga clic en **Guardar todo**.

Tenga en cuenta que este tutorial muestra cómo escribir una aplicación de .NET Core, pero si quiere seleccionar como destino .NET Framework al completo, agregue la siguiente línea de código al archivo project.json, en la sección `"frameworks"`:

```json
"net451": {
},
``` 

## <a name="add-the-event-hubs-nuget-package"></a>Agregue el paquete NuGet de Event Hubs.

* Agregue los siguientes paquetes de NuGet al proyecto:
  * [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)
  * [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/)

## <a name="implement-the-ieventprocessor-interface"></a>Implemente la interfaz de IEventProcessor.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, haga clic en **Agregar** y después en **Clase**. Asigne el nombre **SimpleEventProcessor** a la nueva clase.

2. Abra el archivo SimpleEventProcessor.cs y agregue las siguientes instrucciones `using` en la parte superior del archivo.

    ```csharp
    using System.Text;
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. Implemente la interfaz `IEventProcessor`. Reemplace todo el contenido de la clase `SimpleEventProcessor` con el código siguiente:

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

## <a name="write-a-main-console-method-that-uses-the-simpleeventprocessor-class-to-receive-messages"></a>Escritura de un método de consola principal que usa la clase SimpleEventProcessor para recibir mensajes

1. Agregue las siguientes instrucciones `using` al principio del archivo Program.cs.
  
    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. Agregue constantes a la clase `Program` para la cadena de conexión de Event Hubs, el nombre del contenedor de almacenamiento y el nombre y la clave de la cuenta de almacenamiento. Agregue el código siguiente, reemplazando los marcadores de posición con sus valores correspondientes.

    ```csharp
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Agregue un nuevo método denominado `MainAsync` a la clase `Program`, de esta manera:

    ```csharp
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

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Agregue la siguiente línea de código al método `Main`:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Este es el aspecto que debería tener el archivo Program.cs:

    ```csharp
    namespace SampleEphReceiver
    {
    
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
    
                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. Ejecute el programa y asegúrese de que no hay ningún error.
  
¡Enhorabuena! Ahora recibe mensajes de un centro de eventos por medio del host de procesador de eventos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
