## Envío de mensajes interactivos desde dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado para enviar mensajes de dispositivo a nube interactivos al Centro de IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Este método es muy similar al método `SendDeviceToCloudMessagesAsync()` que se creó en [Introducción al Centro de IoT], las únicas diferencias son que la propiedad del sistema `MessageId` y una propiedad de usuario denominada `messageType` ahora están establecidas. La propiedad `MessageId` se establece en un identificador único global (guid), que se usará para desduplicar la recepción de mensajes. La propiedad `messageType` se usa para distinguir los mensajes interactivos de los mensajes de punto de datos. Esta información se pasa en las propiedades del mensaje, y no en el cuerpo, para que el procesador de eventos del back-end no tenga que deserializar el mensaje completo para realizar el enrutamiento.

> [AZURE.NOTE]Es importante que la propiedad `MessageId`, que se utiliza para desduplicar los mensajes interactivos, se cree en el dispositivo, ya que las comunicaciones de red intermitentes (u otros errores) podrían provocar varias retransmisiones del mismo mensaje desde el dispositivo. También puede usar un identificador de mensaje semántico (por ejemplo, un hash de los campos de datos de mensaje relevantes), en lugar de un guid.

2. Agregue el siguiente método en el método **Main** inmediatamente antes de la línea `Console.ReadLine()`:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, se recomienda implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal como se sugiere en el artículo MSDN [control de errores transitorios].

<!-- Links -->
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Oct15_HO1-->