## Envío de mensajes interactivos desde un dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción al Centro de IoT] para enviar mensajes de dispositivo a nube interactivos al Centro de IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.

    ```
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

        Task.Delay(10000).Wait();
      }
    }
    ```

    Este método es muy similar al método **SendDeviceToCloudMessagesAsync** del proyecto **SimulatedDevice**. Las únicas diferencias son que ahora establece la propiedad del sistema **MessageId** y una propiedad de usuario denominada **messageType**. El código asigna un identificador único global (GUID) a la propiedad **MessageId**. El Bus de servicio puede utilizarla para desduplicar los mensajes que recibe. En el ejemplo se usa la propiedad **messageType** para distinguir los mensajes interactivos de los mensajes de punto de datos. La aplicación pasa esta información en las propiedades del mensaje, no en el cuerpo, para que el procesador de eventos no tenga que deserializar el mensaje para realizar su enrutamiento.

    > [AZURE.NOTE] Es importante crear el valor de **MessageId** usado para desduplicar los mensajes interactivos en el código de dispositivo. Las comunicaciones de red intermitentes, u otros errores, podrían provocar varias retransmisiones del mismo mensaje desde ese dispositivo. También puede utilizar un identificador de mensaje semántico, como un hash de los campos de datos relevantes del mensaje, en lugar de un GUID.

2. Agregue el siguiente método en el método **Main** inmediatamente antes de la línea `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, debe implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] \(Control de errores transitorios).

<!-- Links -->
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx
[Introducción al Centro de IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0608_2016-->