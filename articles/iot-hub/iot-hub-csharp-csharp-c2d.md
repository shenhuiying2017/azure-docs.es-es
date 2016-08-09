<properties
	pageTitle="Envío de mensajes de nube a dispositivo con el Centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensajes de nube a dispositivo mediante el Centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# Tutorial: cómo enviar mensajes de la nube a un dispositivo con el Centro de IoT y .NET

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introducción

Centro de IoT de Azure es un servicio totalmente administrado que ayuda a habilitar la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de aplicación. El tutorial [Introducción al Centro de IoT] muestra cómo crear un Centro de IoT, aprovisionar la identidad de un dispositivo en él y codificar un dispositivo simulado que envía mensajes de dispositivo a nube.

Este tutorial se basa en [Introducción al Centro de IoT de Azure para .NET]. En él se muestra cómo realizar las siguientes acciones:

- Desde el back-end de la nube de la aplicación, envíe mensajes de la nube al dispositivo en un único dispositivo a través de Centro de IoT.
- Reciba mensajes de nube a dispositivo en un dispositivo.
- Desde el back-end de la nube de la aplicación, solicite confirmación de entrega (*comentarios*) para los mensajes enviados a un dispositivo desde Centro de IoT.

Encontrará más información sobre los mensajes de nube a dispositivo en la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D].

Al final de este tutorial, ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**, versión modificada de la aplicación creada en [Introducción al Centro de IoT], que se conecta al Centro de IoT y recibe mensajes de nube a dispositivo.
* **SendCloudToDevice**, que envía un mensaje de la nube al dispositivo simulado mediante el Centro de IoT y, luego, recibe su confirmación de entrega.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK en muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK de dispositivos IoT de Azure. Visite el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Recepción de mensajes en el dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Introducción al Centro de IoT de Azure para .NET] para recibir mensajes de la nube a un dispositivo desde el Centro de IoT.

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

    El método `ReceiveAsync` devuelve de forma asincrónica el mensaje recibido en el momento en que lo recibe el dispositivo. Se devuelve *null* tras un período de espera que se puede especificar (en este caso, se usa el valor predeterminado de un minuto). Cuando esto sucede, el código debe seguir esperando nuevos mensajes. Este es el motivo para la línea `if (receivedMessage == null) continue`.

    La llamada a `CompleteAsync()` notifica al Centro de IoT que el mensaje se ha procesado correctamente. Los mensajes pueden quitarse con seguridad de la cola del dispositivo. Si ha sucedido algo que ha impedido que la aplicación del dispositivo termine de procesar el mensaje, el Centro de IoT lo entrega de nuevo. Es importante que la lógica de procesamiento de mensajes de la aplicación para dispositivo sea *idempotente*, de modo que, si se recibe el mismo mensaje varias veces, se genera el mismo resultado. Una aplicación también puede abandonar temporalmente un mensaje, lo que da lugar a que el Centro de IoT retenga el mensaje en la cola para su consumo futuro. O bien, la aplicación puede rechazar un mensaje, de forma que este se quita permanentemente de la cola. Para más información sobre el ciclo de vida de los mensajes de la nube a un dispositivo, consulte la [Guía del desarrollador del Centro de IoT de Azure][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Cuando se usa HTTP/1 en lugar de AMQP como transporte, el método `ReceiveAsync` se devuelve inmediatamente. El patrón admitido para los mensajes de nube a dispositivo con HTTP/1 es dispositivos conectados de forma intermitente que buscan mensajes con poca frecuencia (menos de 25 minutos). Emitir más recepciones HTTP/1 tendrá como resultado la limitación de solicitudes del Centro de IoT. Consulte la [Guía del desarrollador del Centro de IoT][IoT Hub Developer Guide - C2D] para más información sobre las diferencias entre la compatibilidad con AMQP y HTTP/1, y la limitación del Centro de IoT.

2. Agregue el siguiente método en el método **Main**, inmediatamente delante de la línea `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

## Envío de mensajes de nube a dispositivo

En esta sección, escribirá una aplicación de consola de Windows que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado.

1. En la solución actual de Visual Studio, cree un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **SendCloudToDevice**.

    ![Nuevo proyecto en Visual Studio][20]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**.

	Se abre la ventana **Administrar paquetes NuGet**.

3. Busque `Microsoft Azure Devices`, haga clic en **Instalar** y acepte las condiciones de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Servicios IoT de Azure].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices;

5. Agregue los campos siguientes a la clase **Program**. Sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT de [Introducción al Centro de IoT]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program**:

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Este método envía un nuevo mensaje de la nube al dispositivo con el identificador `myFirstDevice`. Si modificó el parámetro usado en [Introducción al Centro de IoT de Azure para .NET], cámbielo según corresponda.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y elija la acción **Iniciar** para las aplicaciones **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **SendCloudToDevice**.

9.  Presione **F5**. Deberían iniciarse las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Debería ver el mensaje que recibe la aplicación simulada.

    ![Aplicación que recibe el mensaje][21]

## Recepción de comentarios de entrega
Es posible solicitar confirmaciones de entrega (o expiración) del Centro de IoT para cada mensaje de nube a dispositivo. Esto permite que el back-end de nube notifique fácilmente la lógica de reintento o compensación. Consulte la [Guía del desarrollador del Centro de IoT de Azure][IoT Hub Developer Guide - C2D] para más información sobre los comentarios de la nube a un dispositivo.

En esta sección, modificará la aplicación **SendCloudToDevice** para solicitar comentarios y recibirlos desde el Centro de IoT.

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

    Tenga en cuenta que el patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.

2. Agregue el siguiente método en **Main** inmediatamente después de la línea `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:

        ReceiveFeedbackAsync();

3. Para solicitar comentarios de la entrega del mensaje de la nube a un dispositivo, debe especificar una propiedad en el método **SendCloudToDeviceMessageAsync**. Agregue la línea siguiente, inmediatamente después de la línea `var commandMessage = new Message(...);`:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Ejecute las aplicaciones presionando **F5**. Debería ver que se inician las tres aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione **Intro**. Debería ver mensajes que recibe la aplicación simulada y, al cabo de unos segundos, el mensaje de comentarios que recibe la aplicación **SendCloudToDevice**.

    ![Aplicación que recibe el mensaje][22]

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] (Control de errores transitorios).

## Pasos siguientes

En este tutorial, aprendió a enviar y recibir mensajes de nube a dispositivo.

Para ver ejemplos de soluciones completas de un extremo a otro que usen el Centro de IoT, consulte [Documentación del Conjunto de aplicaciones de IoT].

Para más información sobre cómo desarrollar soluciones con el Centro de IoT, consulte la [Guía del desarrollador del Centro de IoT de Azure].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[paquete NuGet del SDK de Servicios IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Guía del desarrollador del Centro de IoT de Azure]: iot-hub-devguide.md
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Documentación del Conjunto de aplicaciones de IoT]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0727_2016-->