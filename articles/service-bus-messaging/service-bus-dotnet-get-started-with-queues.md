<properties
    pageTitle="Introducción a las colas del Bus de servicio | Microsoft Azure"
    description="Cómo escribir una aplicación de consola en C# para la mensajería del Bus de servicio"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>

# Introducción a las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

## Lo que se logrará

En este tutorial, se llevará a cabo lo siguiente:

1. Creación de un espacio de nombres del Bus de servicio mediante el Portal de Azure

2. Creación de una cola de mensajería del Bus de servicio mediante el Portal de Azure

3. Escritura de una aplicación de consola para enviar un mensaje

4. Escritura de una aplicación de consola para recibir mensajes

## Requisitos previos

1. [Visual Studio 2013 o Visual Studio 2015](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2015.

2. Una suscripción de Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1\. Creación de un espacio de nombres mediante el Portal de Azure

Si ya tiene creado un espacio de nombres del Bus de servicio, vaya a la sección [Creación de una cola mediante el Portal de Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2\. Creación de una cola mediante el Portal de Azure

Si ya ha creado una cola de Bus de servicio, vaya a la sección [Envío de mensajes a la cola](#3-send-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3\. Envío de mensajes a la cola

Para enviar mensajes a la cola, escribirá una aplicación de consola en C# con Visual Studio.

### Creación de una aplicación de consola

1. Inicie Visual Studio y cree una aplicación de consola.

### Agregar el paquete NuGet del bus de servicio

1. Haga clic con el botón derecho en el proyecto recién creado y seleccione **Administrar paquetes NuGet**.

2. Haga clic en la pestaña **Examinar** y, después, busque y seleccione el elemento **Bus de servicio de Microsoft Azure**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

    ![Seleccionar un paquete NuGet][nuget-pkg]

### Escritura de código para enviar un mensaje a la cola

1. Agregue la siguiente instrucción using al principio del archivo Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Agregue el siguiente código al método `Main`, establezca la variable **connectionString** en la cadena de conexión que se obtuvo al crear el espacio de nombres y establezca **queueName** en el nombre de cola que usó cuando la creó.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    Este es el aspecto que debería tener Program.cs.

    ```
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
  
3. Ejecute el programa y compruebe el Portal de Azure. Haga clic en el nombre de la cola en la hoja **Introducción** del espacio de nombres. Tenga en cuenta que el valor de **Recuento de mensajes activos** debe ser ahora 1.
    
      ![Recuento de mensajes][queue-message]
    
## 4\. Recepción de mensajes de la cola

1. Cree una nueva aplicación de consola y agregue una referencia al paquete NuGet del Bus de servicio, al igual que con la aplicación de envío anterior.

2. Agregue la siguiente instrucción `using` al principio del archivo Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Agregue el siguiente código al método `Main`, establezca la variable **connectionString** en la cadena de conexión que se obtuvo al crear el espacio de nombres y establezca **queueName** en el nombre de cola que usó cuando la creó.

    ```
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

    ```
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

## Pasos siguientes

Consulte nuestro [repositorio de GitHub con ejemplos](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) que muestran algunas de las características más avanzadas de mensajería del Bus de servicio de Azure.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_1005_2016-->