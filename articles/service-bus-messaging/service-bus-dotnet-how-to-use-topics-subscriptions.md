---
title: "Introducción a los temas y las suscripciones de Azure Service Bus | Microsoft Docs"
description: "Escriba una aplicación de consola en C# que use los temas y las suscripciones de mensajería de Service Bus."
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
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Introducción a las colas de Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este tutorial se describen los pasos siguientes:

1. Creación de un espacio de nombres de Service Bus mediante Azure Portal.
2. Creación de un tema de Service Bus mediante Azure Portal.
3. Creación de una suscripción de Service Bus a dicho tema mediante Azure Portal.
4. Escritura de una aplicación de consola para enviar un mensaje al tema.
5. Escritura de una aplicación de consola para recibir dicho mensaje de la suscripción.

## <a name="prerequisites"></a>Requisitos previos

1. [Visual Studio 2015 o posterior](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2017.
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal

Si ya ha creado un espacio de nombres de mensajería de Service Bus, vaya a la sección [Creación de un tema mediante Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Creación de un tema mediante Azure Portal

1. Inicie sesión en [Azure Portal][azure-portal].
2. En el panel de navegación izquierdo del portal, haga clic en **Service Bus** (si no ve **Service Bus**, haga clic en **Más servicios**).
3. Haga clic en el espacio de nombres en el que desea crear el tema. Aparece la hoja de información general del espacio de nombres:
   
    ![Creación de un tema][createtopic1]
4. En la hoja **Espacio de nombres de Service Bus**, haga clic en **Temas** y, después, en **Agregar tema**.
   
    ![Seleccionar temas][createtopic2]
5. Escriba el nombre del tema y desactive la opción **Habilitar particiones**. Deje las restantes opciones con sus valores predeterminados.
   
    ![Seleccionar Nuevo][createtopic3]
6. Haga clic en **Crear**en la parte inferior de la hoja.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Creación de una suscripción al tema

1. En el panel recursos del portal, haga clic en el espacio de nombres que creó en el paso 1 y, después, en el nombre del tema que creó en el paso 2.
2. En la parte superior del panel de información general, haga clic en el signo más junto a **Suscripción** para agregar una suscripción a este tema.

    ![Creación de una suscripción][createtopic4]

3. Escriba el nombre de la suscripción. Deje las restantes opciones con sus valores predeterminados.

## <a name="4-send-messages-to-the-topic"></a>4. Envío de mensajes al tema

Para enviar mensajes al tema, se escribe una aplicación de consola en C# mediante Visual Studio.

### <a name="create-a-console-application"></a>Creación de una aplicación de consola

Inicie Visual Studio y cree un nuevo proyecto **Console app (.NET Framework)**.

### <a name="add-the-service-bus-nuget-package"></a>Agregar el paquete NuGet de Service Bus

1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.
2. Haga clic en la pestaña **Examinar**, busque **WindowsAzure.ServiceBus** y seleccione el elemento **WindowsAzure.ServiceBus**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.
   
    ![Seleccionar un paquete NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Escritura de código para enviar un mensaje al tema

1. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Agregue el siguiente código al método `Main`. Establezca la variable `connectionString` en la cadena de conexión que obtuvo al crear el espacio de nombres y establezca `topicName` en el nombre que usó cuando creó el tema.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Ejecute el programa y compruebe Azure Portal: haga clic en el nombre del tema en la hoja **Introducción** del espacio de nombres. Se muestra la hoja **Essentials** del tema. En las suscripciones que se enumeran cerca de la parte inferior de la hoja, observe que el valor **Número de mensajes** de cada suscripción ahora debe ser 1. Cada vez que se ejecuta la aplicación de remitente sin recuperar los mensajes (como se describe en la siguiente sección), este valor aumenta en 1. Tenga en cuenta también que el tamaño actual del tema aumenta el valor de **Actual** en la hoja **Essentials** cada vez que la aplicación agrega un mensaje al tema o a la suscripción.
   
      ![Tamaño del mensaje][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Recepción de mensajes de la suscripción

1. Para recibir el mensaje, o los mensajes, que acaba de enviar, cree una nueva aplicación de consola y agregue una referencia al paquete NuGet de Service Bus, de forma similar a la aplicación de remitente anterior.
2. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Agregue el siguiente código al método `Main`. Establezca la variable `connectionString` en la cadena de conexión que obtuvo al crear el espacio de nombres y establezca `topicName` en el nombre que usó cuando creó el tema. Asegúrese también de reemplazar `<your subscription name>` por el nombre de la suscripción que creó en el paso 3. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. Ejecute el programa y vuelva a comprobar el portal. Tenga en cuenta que los valores de **Recuento de mensajes** y **Actual** deben ser ahora 0.
   
    ![Longitud del tema][topic-message-receive]

¡Enhorabuena! Ya ha creado un tema y una suscripción, ha enviado un mensaje y ha recibido dicho mensaje.

## <a name="next-steps"></a>Pasos siguientes

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
