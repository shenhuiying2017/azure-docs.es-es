---
title: "Envío de eventos a Azure Event Hubs mediante .NET Standard | Microsoft Docs"
description: "Introducción al envío de eventos a Event Hubs en .NET Standard"
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
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: dcb8bb1dcf287ecb2d91e19bf4df38765fe79114
ms.lasthandoff: 03/01/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Introducción al envío de mensajes a Event Hubs en .NET Standard

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender).

## <a name="what-will-be-accomplished"></a>Lo que se logrará

Este tutorial muestra cómo crear la solución existente **SampleSender** (dentro de esta carpeta). Puede ejecutar la solución tal cual está, reemplazando las cadenas `EhConnectionString`, `EhEntityPath` y `StorageAccount` por los valores del centro de eventos o seguir este tutorial para crear la suya propia.

En este tutorial, se escribirá una aplicación de consola de .NET Core para enviar mensajes a un centro de eventos.

## <a name="prerequisites"></a>Requisitos previos

1. [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com).

2. [Utillaje de .NET Core Visual Studio 2015 0 2017](http://www.microsoft.com/net/core).

3. Una suscripción de Azure.

4. Un espacio de nombres de Event Hubs.

## <a name="send-messages-to-an-event-hub"></a>Envío de mensajes a un centro de eventos

Para enviar mensajes a un centro de eventos, escribiremos una aplicación de consola en C# con Visual Studio.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

* Inicie Visual Studio y cree una aplicación de consola de .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Agregue el paquete NuGet de Event Hubs.

* Agregue el paquete NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al proyecto.

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escritura de código para enviar mensajes al centro de eventos

1. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Agregue constantes a la clase `Program` para la cadena de conexión de Event Hubs y la ruta de la entidad (nombre de centro de eventos individual). Reemplace los marcadores de posición entre corchetes por los valores adecuados obtenidos al crear el centro de eventos.

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Agregue un nuevo método denominado `MainAsync` a la clase `Program` de manera similar a la siguiente:

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. Agregue un nuevo método denominado `SendMessagesToEventHub` a la clase `Program` de manera similar a la siguiente:

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Agregue el siguiente código al método `Main` de la clase `Program`.

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Este es el aspecto que debería tener Program.cs.

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
    
                    await Task.Delay(10);
                }
    
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. Ejecute el programa y asegúrese de que no hay ningún error.
  
¡Enhorabuena! Ya ha enviado mensajes a un centro de eventos.

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Recepción de eventos de Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)
