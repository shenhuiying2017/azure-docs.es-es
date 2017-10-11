---
title: "Introducción a las colas de Azure Service Bus | Microsoft Docs"
description: "Escriba una aplicación de consola en C# que use las colas de mensajería de Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.openlocfilehash: 99a377db6341d90d263b98e14227db61dd9beabd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-service-bus-queues"></a>Introducción a las colas de Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Lo que se logrará
En este tutorial se describen los pasos siguientes:

1. Creación de un espacio de nombres de Service Bus mediante Azure Portal
2. Creación de una cola de Service Bus mediante Azure Portal.
3. Escritura de una aplicación de consola para enviar un mensaje
4. Escritura de una aplicación de consola para recibir los mensajes enviados en el paso anterior.

## <a name="prerequisites"></a>Requisitos previos
1. [Visual Studio 2015 o posterior](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2017.
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal
Si ya ha creado un espacio de nombres de mensajería de Service Bus, vaya a la sección [Creación de una cola mediante Azure Portal](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Creación de una cola mediante Azure Portal
Si ya ha creado una cola de Service Bus, vaya a la sección [Envío de mensajes a la cola](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Envío de mensajes a la cola
Para enviar mensajes a la cola, se escribe una aplicación de consola en C# mediante Visual Studio.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

Inicie Visual Studio y cree un nuevo proyecto **Console app (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Agregar el paquete NuGet de Service Bus
1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.
2. Haga clic en la pestaña **Examinar**, busque **Microsoft Azure Service Bus** y seleccione el elemento **WindowsAzure.ServiceBus**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.
   
    ![Seleccionar un paquete NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Escritura de código para enviar un mensaje a la cola
1. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Agregue el siguiente código al método `Main`. Establezca la variable `connectionString` en la cadena de conexión que obtuvo al crear el espacio de nombres y establezca `queueName` en el nombre de cola que usó cuando creó la cola.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Así debería ser el archivo Program.cs.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Ejecute el programa y compruebe Azure Portal: haga clic en el nombre de la cola en la hoja **Introducción** del espacio de nombres. Se muestra la hoja **Essentials** de la cola. Tenga en cuenta que el valor de **Recuento de mensajes activos** debe ser ahora 1. Cada vez que se ejecuta la aplicación de remitente sin recuperar los mensajes, este valor aumenta en 1. Tenga también en cuenta que el tamaño actual de la cola aumenta cada vez que la aplicación agrega un mensaje a la misma.
   
      ![Tamaño del mensaje][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Recepción de mensajes de la cola

1. Para recibir los mensajes que acaba de enviar, cree una nueva aplicación de consola y agregue una referencia al paquete NuGet de Service Bus, similar a la aplicación de remitente anterior.
2. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Agregue el siguiente código al método `Main`. Establezca la variable `connectionString` en la cadena de conexión que se obtuvo al crear el espacio de nombres y establezca `queueName` en el nombre de cola que usó cuando creó la cola.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
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
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Ejecute el programa y vuelva a comprobar el portal. Tenga en cuenta que los valores de **Recuento de mensajes activos** y **Actual** deben ser ahora 0.
   
    ![Longitud de la cola][queue-message-receive]

¡Enhorabuena! Ha creado una cola, ha enviado un mensaje y ha recibido un mensaje.

## <a name="next-steps"></a>Pasos siguientes

Consulte nuestro [repositorio de GitHub con ejemplos](https://github.com/Azure/azure-service-bus/tree/master/samples), donde se muestran algunas de las características más avanzadas de la mensajería de Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
