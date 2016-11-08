## Recepción de mensajes en el dispositivo simulado
En esta sección, modificará la aplicación de dispositivo simulado que creó en [Introducción al Centro de IoT] para recibir mensajes de nube a dispositivo del Centro de IoT.

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
   
    El método `ReceiveAsync` devuelve de forma asincrónica el mensaje recibido en el momento en que lo recibe el dispositivo. Devuelve *null* tras un período de tiempo de espera que se puede especificar (en este caso, se usa el valor predeterminado de un minuto). Cuando esto sucede, el código debe seguir esperando nuevos mensajes. Este es el motivo para la línea `if (receivedMessage == null) continue`.
   
    La llamada a `CompleteAsync()` notifica al Centro de IoT que el mensaje se ha procesado correctamente. Los mensajes pueden quitarse con seguridad de la cola del dispositivo. Si ha sucedido algo que ha impedido que la aplicación del dispositivo termine de procesar el mensaje, el Centro de IoT lo entrega de nuevo. Es importante que la lógica de procesamiento de mensajes en la aplicación del dispositivo sea *idempotente*, de modo que si se recibe el mismo mensaje varias veces se produce el mismo resultado. Una aplicación también puede abandonar temporalmente un mensaje, lo que da lugar a que el Centro de IoT retenga el mensaje en la cola para su consumo futuro. O bien, la aplicación puede rechazar un mensaje, de forma que este se quita permanentemente de la cola. Para más información sobre el ciclo de vida de los mensajes de nube a dispositivo, consulte la [Guía del desarrollador del Centro de IoT de Azure][IoT Hub Developer Guide - C2D].

> [!NOTE]
> Cuando se usa HTTP/1 en lugar de AMQP como transporte, el método `ReceiveAsync` se devuelve inmediatamente. El patrón admitido para los mensajes de nube a dispositivo con HTTP/1 es dispositivos conectados de forma intermitente que buscan mensajes con poca frecuencia (menos de 25 minutos). Emitir más recepciones HTTP/1 tendrá como resultado la limitación de solicitudes del Centro de IoT. Consulte la [Guía del desarrollador del Centro de IoT][IoT Hub Developer Guide - C2D] para más información sobre las diferencias entre la compatibilidad con AMQP y HTTP/1, y la limitación del Centro de IoT.
> 
> 

1. Agregue el siguiente método en el método **Main** inmediatamente antes de la línea `Console.ReadLine()`:
   
        ReceiveC2dAsync();

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].
> 
> 

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0608_2016-->