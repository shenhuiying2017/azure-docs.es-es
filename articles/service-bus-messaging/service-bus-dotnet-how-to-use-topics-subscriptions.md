---
title: "Introducción a los temas y las suscripciones de Azure Service Bus | Microsoft Docs"
description: "Escriba una aplicación de consola de .NET Core en C# que use los temas y las suscripciones de mensajería de Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: 5391951c34498b3bd684cd2e223b8288d4040247
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="get-started-with-service-bus-topics"></a>Introducción a las colas de Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este tutorial se describen los pasos siguientes:

1. Creación de un espacio de nombres de Service Bus mediante Azure Portal.
2. Creación de un tema de Service Bus mediante Azure Portal.
3. Creación de una suscripción de Service Bus a dicho tema mediante Azure Portal.
4. Escriba una aplicación de consola de .NET Core para enviar un conjunto de mensajes al tema.
5. Escriba una aplicación de consola de .NET Core para recibir esos mensajes de la suscripción.

## <a name="prerequisites"></a>requisitos previos

1. [Visual Studio 2017 Update 3 (versión 15.3, 26730.01)](http://www.visualstudio.com/vs) o posterior.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), versión 2.0 o posterior.
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal

> [!NOTE] 
> También puede crear entidades de mensajería y un espacio de nombres de Service Bus con [PowerShell](/powershell/azure/get-started-azureps). Para más información, consulte [Uso de PowerShell para administrar recursos de Service Bus](service-bus-manage-with-ps.md).

Si ya ha creado un espacio de nombres de mensajería de Service Bus, vaya a la sección [Creación de un tema mediante Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Creación de un tema mediante Azure Portal

1. Inicie sesión en [Azure Portal][azure-portal].
2. En el panel de navegación izquierdo del portal, haga clic en **Service Bus** (si no ve **Service Bus**, haga clic en **Más servicios**) o en **Todos los recursos**. Haga clic en el espacio de nombres en el que desea crear el tema. 
3. Se abre la ventana de información general del espacio de nombres. Haga clic en **Temas**:
   
    ![de un tema][createtopic1]
4. Haga clic en **+Tema**.
   
    ![Seleccionar temas][createtopic2]
5. Escriba un nombre para el tema. Deje las restantes opciones con sus valores predeterminados.
   
    ![Seleccionar Nuevo][createtopic3]
6. En la parte inferior del cuadro de diálogo, haga clic en **Crear**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Creación de una suscripción al tema

1. En el panel de recursos del portal, haga clic en el espacio de nombres que creó en el paso 1, en **Temas** y, después, en el nombre del tema que creó en el paso 2.
2. En la parte superior del panel de información general, haga clic en **+Suscripción** para agregar una suscripción a este tema.

    ![Creación de una suscripción][createtopic4]

3. Escriba el nombre de la suscripción. Deje las restantes opciones con sus valores predeterminados.

## <a name="4-send-messages-to-the-topic"></a>4. Envío de mensajes al tema

Para enviar mensajes al tema, escriba una aplicación de consola en C# mediante Visual Studio.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

Inicie Visual Studio y cree un nuevo proyecto **Aplicación de consola (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Agregar el paquete NuGet de Service Bus

1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.
2. Haga clic en la pestaña **Examinar**, busque **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** y seleccione el elemento **WindowsAzure.ServiceBus**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.
   
    ![Seleccionar un paquete NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Escritura del código para enviar mensajes al tema

1. En Program.cs, agregue las siguientes instrucciones `using` en la parte superior de la definición del espacio de nombres, antes de la declaración de clase:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. En la clase `Program`, declare las variables siguientes. Establezca la variable `ServiceBusConnectionString` en la cadena de conexión que obtuvo al crear el espacio de nombres y establezca `TopicName` en el nombre que usó cuando creó el tema:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Reemplace el contenido predeterminado de `Main()` con la siguiente línea de código:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. Directamente después de `Main()`, agregue el siguiente método `MainAsync()` asincrónico que llama al método para enviar mensajes:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. Directamente después del método `MainAsync()`, agregue el siguiente método `SendMessagesAsync()` que realiza el trabajo de enviar el número de mensajes especificado por `numberOfMessagesToSend` (actualmente se establece en 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Esta es la apariencia que debería tener el archivo Program.cs del emisor.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Ejecute el programa y revise Azure Portal: haga clic en el nombre del tema en la ventana **Introducción** del espacio de nombres. Se muestra la pantalla **Essentials** del tema. En la suscripción que se enumera cerca de la parte inferior de la ventana, observe que el valor **Número de mensajes** de la suscripción ahora es **10**. Cada vez que se ejecuta la aplicación de remitente sin recuperar los mensajes (como se describe en la siguiente sección), este valor aumenta en 10. Tenga en cuenta también que el tamaño actual del tema aumenta el valor de **Actual** en la ventana **Essentials** cada vez que la aplicación agrega mensajes al tema.
   
      ![Tamaño del mensaje][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Recepción de mensajes de la suscripción

Para recibir los mensajes que acaba de enviar, cree otra aplicación de consola de .NET Core e instale el paquete NuGet **Microsoft.Azure.ServiceBus**, similar a la aplicación de remitente anterior.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Escritura de código para recibir mensajes de la suscripción

1. En Program.cs, agregue las siguientes instrucciones `using` en la parte superior de la definición del espacio de nombres, antes de la declaración de clase:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. En la clase `Program`, declare las variables siguientes. Establezca la variable `ServiceBusConnectionString` en la cadena de conexión que obtuvo al crear el espacio de nombres, establezca `TopicName` en el nombre que usó cuando creó el tema y `SubscriptionName` en el nombre que usó al crear la suscripción al tema:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Reemplace el contenido predeterminado de `Main()` con la siguiente línea de código:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Directamente después de `Main()`, agregue el siguiente método `MainAsync()` asincrónico que llama al método `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. Directamente después del método `MainAsync()`, agregue el método siguiente que registra el controlador de mensajes y recibe los mensajes enviados por la aplicación remitente:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Directamente después del método anterior, agregue el siguiente método `ProcessMessagesAsync()` para procesar los mensajes recibidos:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Por último, agregue el siguiente método para controlar las excepciones que pueden producirse:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Esta es la apariencia que debería tener el archivo Program.cs del receptor:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Ejecute el programa y vuelva a comprobar el portal. Tenga en cuenta que los valores de **Recuento de mensajes** y **Actual** ahora son **0**.
   
    ![Longitud del tema][topic-message-receive]

Felicidades. Con la biblioteca estándar. NET, ahora ha creado un tema y una suscripción, y enviado y recibido diez mensajes.

## <a name="next-steps"></a>pasos siguientes

Consulte nuestro [repositorio de GitHub con ejemplos](https://github.com/Azure/azure-service-bus/tree/master/samples), donde se muestran algunas de las características más avanzadas de la mensajería de Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
