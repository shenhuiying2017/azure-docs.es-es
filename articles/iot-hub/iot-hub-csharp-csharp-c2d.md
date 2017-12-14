---
title: Mensajes de nube a dispositivo con IoT Hub de Azure (.NET) | Microsoft Docs
description: "Cómo enviar mensajes de nube a un dispositivo de una instancia de IoT Hub de Azure mediante los SDK de IoT de Azure para .NET. Modifique una aplicación de dispositivo para recibir mensajes de nube a dispositivo y cambie una aplicación de back-end para enviar los mensajes de nube a dispositivo."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: a31c05ed-6ec0-40f3-99ab-8fdd28b1a89a
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: df0a319cdeabef8c854d7c60c7b90ef775be3ce1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Envío de mensajes desde la nube al dispositivo con IoT Hub (.NET)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introducción
IoT Hub de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos y una solución de back-end. El tutorial [Introducción a Iot Hub] muestra cómo crear un centro de IoT, aprovisionar la identidad de un dispositivo en él y programar una aplicación de dispositivo que envía mensajes de dispositivo a nube.

Este tutorial se basa en la [Introducción a Iot Hub]. En él se muestra cómo realizar las siguientes acciones:

* Desde el back-end de la nube de la aplicación, envíe mensajes de nube a dispositivo en un único dispositivo a través de IoT Hub.
* Reciba mensajes de nube a dispositivo en un dispositivo.
* Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde IoT Hub.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores de IoT Hub][IoT Hub developer guide - C2D].

Al final de este tutorial, ejecutará dos aplicaciones de consola de .NET:

* **SimulatedDevice**, versión modificada de la aplicación creada en [Introducción a Iot Hub], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **SendCloudToDevice**, que envía un mensaje de nube a dispositivo a la aplicación de dispositivo mediante IoT Hub y, luego, recibe su confirmación de entrega.

> [!NOTE]
> IoT Hub ofrece compatibilidad con SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante [SDK de dispositivos IoT de Azure]. Para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general a Azure IoT Hub consulte la [Guía del desarrollador de IoTHub de Azure].
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

## <a name="receive-messages-in-the-device-app"></a>Recepción de mensajes en la aplicación de dispositivo
En esta sección, modificará la aplicación de dispositivo que creó en el tutorial [Introducción a Iot Hub] para recibir mensajes de nube a dispositivo desde IoT Hub.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    El método `ReceiveAsync` devuelve de forma asincrónica el mensaje recibido en el momento en que lo recibe el dispositivo. Se devuelve *null* tras un período de espera que se puede especificar (en este caso, se usa el valor predeterminado de un minuto). Cuando la aplicación recibe un valor *null*, debe continuar esperando nuevos mensajes. Este requisito es el motivo de la línea `if (receivedMessage == null) continue`.
   
    La llamada a `CompleteAsync()` notifica al Centro de IoT que el mensaje se ha procesado correctamente. Los mensajes pueden quitarse con seguridad de la cola del dispositivo. Si ha sucedido algo que ha impedido que la aplicación del dispositivo termine de procesar el mensaje, el Centro de IoT lo entrega de nuevo. Es importante que la lógica de procesamiento de mensajes de la aplicación de dispositivo sea *idempotente*, de modo que, si se recibe el mismo mensaje varias veces, se genera el mismo resultado. Una aplicación también puede abandonar temporalmente un mensaje, lo que da lugar a que el Centro de IoT retenga el mensaje en la cola para su consumo futuro. O bien, la aplicación puede rechazar un mensaje, de forma que este se quita permanentemente de la cola. Para obtener más información sobre el ciclo de vida de los mensajes de nube a dispositivo, consulte la [Guía del desarrollador de IoT Hub][IoT Hub developer guide - C2D].
   
   > [!NOTE]
   > Cuando se usa HTTP en lugar de MQTT o AMQP como transporte, el método `ReceiveAsync` se devuelve inmediatamente. El patrón admitido para los mensajes de nube a dispositivo con HTTP es dispositivos conectados de forma intermitente que buscan mensajes con poca frecuencia (menos de cada 25 minutos). Emitir más recepciones HTTP tendrá como resultado la limitación de solicitudes de IoT Hub. Para más información sobre las diferencias entre la compatibilidad con MQTT, AMQP y HTTP, y la limitación de IoT Hub, consulte la [Guía del desarrollador de IoT Hub][IoT Hub developer guide - C2D].
   > 
   > 
2. Agregue el siguiente método en el método **Main**, inmediatamente delante de la línea `Console.ReadLine()`:
   
        ReceiveC2dAsync();

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].
> 
> 

## <a name="send-a-cloud-to-device-message"></a>Envío de mensajes de nube a dispositivo
En esta sección, escribirá una aplicación de consola de .NET que envía mensajes de nube a dispositivo a la aplicación de dispositivo.

1. En la solución actual de Visual Studio, cree un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **SendCloudToDevice**.
   
    ![Nuevo proyecto en Visual Studio][20]
2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**. 
   
    Esta acción abre la ventana **Administrar paquetes NuGet**.
3. Busque **Microsoft.Azure.Devices**, haga clic en **Instalar** y acepte las condiciones de uso. 
   
    De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Servicios IoT de Azure].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub de [Introducción a Iot Hub]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Agregue el método siguiente a la clase **Program** :
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Este método envía un nuevo mensaje de la nube al dispositivo con el identificador `myFirstDevice`. Cambie este parámetro solo si lo modificó con respecto del utilizado en [Introducción a Iot Hub].
7. Por último, agregue las líneas siguientes al método **Main** :
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y elija la acción **Iniciar** para **ReadDeviceToCloudMessages**, **SimulatedDevice** y **SendCloudToDevice**.
9. Presione **F5**. Deberían iniciarse las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Debería ver el mensaje que recibe la aplicación de dispositivo.
   
   ![Aplicación que recibe el mensaje][21]

## <a name="receive-delivery-feedback"></a>Recepción de comentarios de entrega
Es posible solicitar confirmaciones de entrega (o expiración) del Centro de IoT para cada mensaje de nube a dispositivo. Esta opción permite que el back-end de solución notifique fácilmente la lógica de reintento o compensación. Para obtener más información sobre los comentarios de nube a dispositivo, consulte la [Guía del desarrollador de IoT Hub][IoT Hub developer guide - C2D].

En esta sección, modificará la aplicación **SendCloudToDevice** para solicitar comentarios y recibirlos de IoT Hub.

1. En Visual Studio, en el proyecto **SendCloudToDevice**, agregue el método siguiente a la clase **Program**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    Tenga en cuenta que este patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.
2. Agregue el siguiente método en **Main** inmediatamente después de la línea `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:
   
        ReceiveFeedbackAsync();
3. Para solicitar comentarios de la entrega del mensaje de la nube a un dispositivo, debe especificar una propiedad en el método **SendCloudToDeviceMessageAsync** . Agregue la línea siguiente, inmediatamente después de la línea `var commandMessage = new Message(...);` :
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Ejecute las aplicaciones presionando **F5**. Debería ver que se inician las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Verá los mensajes que recibe la aplicación de dispositivo y, al cabo de unos segundos, el mensaje de comentarios que recibe la aplicación **SendCloudToDevice**.
   
   ![Aplicación que recibe el mensaje][22]

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].
> 
> 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo. 

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT].

Para obtener más información sobre cómo desarrollar soluciones con IoT Hub, consulte la [Guía del desarrollador de IoTHub de Azure].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[paquete NuGet del SDK de Servicios IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Control de errores transitorios]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md

[Guía del desarrollador de IoTHub de Azure]: iot-hub-devguide.md
[Introducción a Iot Hub]: iot-hub-csharp-csharp-getstarted.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Documentación del Conjunto de aplicaciones de IoT]: https://docs.microsoft.com/azure/iot-suite/
[SDK de dispositivos IoT de Azure]: iot-hub-devguide-sdks.md