---
title: "Envío de eventos a Azure Event Hubs mediante .NET Framework | Microsoft Docs"
description: "Introducción al envío de eventos a Event Hubs mediante .NET Framework"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: c52f7055897ba8e851add431e5ab9c0defdb5bfc
ms.openlocfilehash: 29523e308e038904773582c73c1688f57e3c31e3


---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Envío de eventos a Azure Event Hubs mediante .NET Framework

## <a name="introduction"></a>Introducción
Event Hubs es un servicio que procesa grandes cantidades de datos de eventos (telemetría) desde aplicaciones y dispositivos conectados. Después de recopilar datos en Centros de eventos, puede almacenarlos mediante un clúster de almacenamiento o transformarlos por medio de un proveedor de análisis en tiempo real. Esta funcionalidad de recopilación y procesamiento de eventos a gran escala es un componente clave de las modernas arquitecturas de aplicaciones, entre las que se incluye Internet de las cosas (IoT).

En este tutorial se muestra cómo usar [Azure Portal](https://portal.azure.com) para crear un centro de eventos. También muestra cómo enviar eventos a un centro de eventos mediante una aplicación de consola escrita en C# con .NET Framework. Para recibir eventos mediante .NET Framework, consulte el artículo [Get started with Event Hubs using the .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md) (Introducción al uso de .NET Framework por parte de Event Hubs) o haga clic en el idioma de recepción correspondiente en la tabla de contenido de la izquierda.

Para completar este tutorial, necesitará lo siguiente:

* [Microsoft Visual Studio](http://visualstudio.com)
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/free/).

## <a name="send-messages-to-event-hubs"></a>Envío de mensajes a Centros de eventos
En esta sección se escribirá una aplicación de consola Windows que envía eventos al Centro de eventos.

1. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **Remitente**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes NuGet para la solución**. 
3. Haga clic en la pestaña **Examinar** y luego busque `Microsoft Azure Service Bus`. Asegúrese de que el nombre del proyecto (**Remitente**) se especifica en el cuadro **Versiones**. Haga clic en **Instalar**y acepte las condiciones de uso. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio descarga, instala y agrega una referencia al [paquete NuGet de la biblioteca del Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
5. Agregue los siguientes campos a la clase **Program** ; para ello, sustituya los valores del marcador de posición por el nombre del Centro de eventos creado en la sección anterior y la cadena de conexión de nivel del espacio de nombres que ha guardado anteriormente.
   
    ```csharp
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```
6. Agregue el método siguiente a la clase **Program** :
   
    ```csharp
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }
   
            Thread.Sleep(200);
        }
    }
    ```
   
    Este método envía continuamente los eventos al Centro de eventos con un retraso de 200 ms.
7. Por último, agregue las líneas siguientes al método **Main** :
   
    ```csharp
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía datos, puede pasar a los siguientes escenarios:

* [Recepción de eventos mediante el Host de procesador de eventos](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Referencia del Host del procesador de eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md




<!--HONumber=Feb17_HO1-->


