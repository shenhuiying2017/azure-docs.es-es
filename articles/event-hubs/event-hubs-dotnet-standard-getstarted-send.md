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
ms.date: 03/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 98de5528d8c74630153bf6908c590b3e0a89d870
ms.lasthandoff: 03/22/2017

---

# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Introducción al envío de mensajes a Azure Event Hubs en .NET Standard

> [!NOTE]
> Este ejemplo está disponible en [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender).

En este tutorial se muestra cómo escribir una aplicación de consola de .NET Core que envía un conjunto de mensajes a un centro de eventos. Puede ejecutar la solución de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) tal cual está, reemplazando las cadenas`EhConnectionString` y `EhEntityPath` por los valores del centro de eventos de Event Hubs. También puede seguir los pasos de este tutorial para crear el suyo propio.

## <a name="prerequisites"></a>Requisitos previos

* [Microsoft Visual Studio 2015 o 2017](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2015, pero también se admite Visual Studio 2017.
* [Herramientas de .NET Core Visual Studio 2015 o 2017](http://www.microsoft.com/net/core).
* Una suscripción de Azure.
* Un espacio de nombres de Event Hubs.

Para enviar mensajes a un centro de eventos de Event Hubs, escribiremos una aplicación de consola en C# con Visual Studio.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Creación de un espacio de nombres de Event Hubs y un centro de eventos

El primer paso consiste en usar [Azure Portal](https://portal.azure.com) para crear un espacio de nombres de tipo Event Hubs y obtener las credenciales de administración que la aplicación necesita para comunicarse con el centro de eventos. Para crear un espacio de nombres y un centro de eventos, siga el procedimiento de [este artículo](event-hubs-create.md) y después continúe con los pasos siguientes.

## <a name="create-a-console-application"></a>Creación de una aplicación de consola

Inicie Visual Studio. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**. Cree una aplicación de consola de .NET Core.

![Nuevo proyecto][1]

## <a name="add-the-event-hubs-nuget-package"></a>Agregue el paquete NuGet de Event Hubs.

Agregue el paquete NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al proyecto.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escritura de código para enviar mensajes al centro de eventos

1. Agregue las siguientes instrucciones `using` al principio del archivo Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    ```

2. Agregue constantes a la clase `Program` para la cadena de conexión de Event Hubs y la ruta de la entidad (nombre de centro de eventos individual). Reemplace los marcadores de posición entre corchetes por los valores adecuados obtenidos al crear el centro de eventos.

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Agregue un nuevo método denominado `MainAsync` a la clase `Program`, de esta manera:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Agregue un nuevo método denominado `SendMessagesToEventHub` a la clase `Program` de manera similar a la siguiente:

    ```csharp
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

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Este es el aspecto que debería tener Program.cs.

    ```csharp
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
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
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

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png

