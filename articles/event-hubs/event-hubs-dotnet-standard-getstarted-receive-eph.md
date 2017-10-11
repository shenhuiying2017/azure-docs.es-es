---
title: "Recepción de eventos desde Azure Event Hubs mediante .NET Standard | Microsoft Docs"
description: "Introducción a la recepción de mensajes con EventProcessorHost en .NET Standard"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: cc62792dad0284f9514664795fdfb32e94a85943
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-standard"></a>Introducción a la recepción de mensajes con el Host del procesador de eventos en .NET Standard

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver).

En este tutorial se muestra cómo escribir una aplicación de consola de .NET Core que recibe mensajes de un centro de eventos mediante **EventProcessorHost**. Puede ejecutar la solución de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) tal como está, reemplazando las cadenas por los valores de la cuenta de almacenamiento y centro de eventos. También puede seguir los pasos de este tutorial para crear el suyo propio.

## <a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2017, pero también se admite Visual Studio 2015.
* [Herramientas de .NET Core Visual Studio 2015 o 2017](http://www.microsoft.com/net/core).
* Una suscripción de Azure.
* Un espacio de nombres de Azure Event Hubs.
* Una cuenta de almacenamiento de Azure.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creación de un espacio de nombres de Event Hubs y un centro de eventos  

El primer paso consiste en usar [Azure Portal](https://portal.azure.com) para crear un espacio de nombres de tipo Event Hubs y obtener las credenciales de administración que la aplicación necesita para comunicarse con el centro de eventos. Para crear un espacio de nombres y un centro de eventos, siga el procedimiento de [este artículo](event-hubs-create.md) y después continúe con los pasos siguientes.  

## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).  
2. En el panel de navegación izquierdo del portal, haga clic en **Nuevo**, luego en **Almacenamiento** y, a continuación, en **Cuenta de almacenamiento**.  
3. Complete los campos de la hoja de la cuenta de almacenamiento y, luego, haga clic en **Crear**.

    ![Crear cuenta de almacenamiento][1]

4. Una vez que aparezca el mensaje **Implementaciones correctas**, haga clic en el nombre de la nueva cuenta de almacenamiento. En la hoja **Essentials**, haga clic en **Blobs**. Cuando se abra la hoja **Blob service**, haga clic en **+ Container** (Contenedor +) en la parte superior. Asigne un nombre al contenedor y cierre la hoja **Blob Service**.  
5. Haga clic en **Claves de acceso** en la hoja izquierda y copie el nombre del contenedor de almacenamiento, la cuenta de almacenamiento y el valor de **key1**. Guarde estos valores en el Bloc de notas, o en cualquier otra ubicación temporal.  

## <a name="create-a-console-application"></a>Creación de una aplicación de consola

Inicie Visual Studio. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**. Cree una aplicación de consola de .NET Core.

![Nuevo proyecto][2]

## <a name="add-the-event-hubs-nuget-package"></a>Agregue el paquete NuGet de Event Hubs.

Agregue los paquetes NuGet de la biblioteca estándar .NET [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) y [`Microsoft.Azure.EventHubs.Processor`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) a su proyecto siguiendo estos pasos: 

1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.
2. Haga clic en la pestaña **Examinar** y, después, busque "Microsoft.Azure.EventHubs" y seleccione el paquete **Microsoft.Azure.EventHubs**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.
3. Repita los pasos 1 y 2 e instale el paquete **Microsoft.Azure.EventHubs.Processor**.

## <a name="implement-the-ieventprocessor-interface"></a>Implemente la interfaz de IEventProcessor.

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto, haga clic en **Agregar** y después en **Clase**. Asigne el nombre **SimpleEventProcessor** a la nueva clase.

2. Abra el archivo SimpleEventProcessor.cs y agregue las siguientes instrucciones `using` en la parte superior del archivo.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
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
    using System.Threading.Tasks;
    ```

2. Agregue constantes a la clase `Program` para la cadena de conexión del centro de eventos, el nombre del centro de eventos, el nombre del contenedor de la cuenta de almacenamiento, y el nombre de la cuenta de almacenamiento y la clave de la cuenta de almacenamiento. Agregue el código siguiente, reemplazando los marcadores de posición con sus valores correspondientes.

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

¡Enhorabuena! Recibió mensajes de un centro de eventos mediante el host de procesador de eventos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcore.png
