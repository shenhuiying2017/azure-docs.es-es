---
title: "Creación de un programa que usa colas de Azure Service Bus | Microsoft Docs"
description: "Cómo escribir una aplicación de consola en C# para la mensajería del Bus de servicio"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 83649bdad1d369cdfe4edf3c2bdaa67180db8668
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-service-bus-queues"></a>Introducción a las colas del Bus de servicio
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Lo que se logrará
En este tutorial, se llevará a cabo lo siguiente:

1. Creación de un espacio de nombres del Bus de servicio mediante el Portal de Azure
2. Creación de una cola de mensajería del Bus de servicio mediante el Portal de Azure
3. Escritura de una aplicación de consola para enviar un mensaje
4. Escritura de una aplicación de consola para recibir mensajes

## <a name="prerequisites"></a>Requisitos previos
1. [Visual Studio 2015 o posterior](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2015.
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante el Portal de Azure
Si ya tiene creado un espacio de nombres de Service Bus, vaya a la sección [Creación de una cola mediante Azure Portal](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Creación de una cola mediante el Portal de Azure
Si ya ha creado una cola de Service Bus, vaya a la sección [Envío de mensajes a la cola](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Envío de mensajes a la cola
Para enviar mensajes a la cola, escribirá una aplicación de consola en C# con Visual Studio.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

- Inicie Visual Studio y cree una aplicación de consola.

### <a name="add-the-service-bus-nuget-package"></a>Agregar el paquete NuGet del bus de servicio
1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.
2. Haga clic en la pestaña **Examinar** y, después, busque y seleccione el elemento **Microsoft Azure Service Bus**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.
   
    ![Seleccionar un paquete NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Escritura de código para enviar un mensaje a la cola
1. Agregue la siguiente instrucción using al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Agregue el siguiente código al método `Main`, establezca la variable **connectionString** en la cadena de conexión que se obtuvo al crear el espacio de nombres y establezca **queueName** en el nombre de cola que usó cuando la creó.
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    Este es el aspecto que debería tener Program.cs.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. Ejecute el programa y compruebe el Portal de Azure. Haga clic en el nombre de la cola en la hoja **Información general** del espacio de nombres. Tenga en cuenta que el valor de **Recuento de mensajes activos** debe ser ahora 1.
   
      ![Recuento de mensajes][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Recepción de mensajes de la cola
1. Cree una nueva aplicación de consola y agregue una referencia al paquete NuGet del Bus de servicio, al igual que con la aplicación de envío anterior.
2. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Agregue el siguiente código al método `Main`, establezca la variable **connectionString** en la cadena de conexión que se obtuvo al crear el espacio de nombres y establezca **queueName** en el nombre de cola que usó cuando la creó.
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.ReadLine();
    ```
   
    Este es el aspecto que debería tener el archivo Program.cs:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. Ejecute el programa y compruebe el portal. Tenga en cuenta que el valor de **Longitud de la cola** debería ser ahora 0.
   
    ![Longitud de la cola][queue-message-receive]

¡Enhorabuena! Ha creado una cola, ha enviado un mensaje y ha recibido un mensaje.

## <a name="next-steps"></a>Pasos siguientes
Consulte nuestro [repositorio de GitHub con ejemplos](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) que muestran algunas de las características más avanzadas de mensajería de Azure Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

