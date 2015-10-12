## Creación de una identidad de dispositivo

En esta sección, escribirá una aplicación de consola de Windows que crea una identidad de dispositivo en el Centro de IoT. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - Identity Registry] para obtener más información. Después de ejecutar esta aplicación de consola, tendrá un identificador y una clave para utilizar como identidad del dispositivo para enviar mensajes de dispositivo a nube al Centro de IoT.

1. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **CreateDeviceIdentity**.

   	![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**.

	De este modo aparece la ventana Administrar paquetes de NuGet.

3. Busque `Microsoft Azure Devices`, haga clic en **Instalar** y acepte las condiciones de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Servicios IoT de Azure].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por el nombre del Centro de IoT que creó en la sección anterior y su cadena de conexión:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Agregue el método siguiente a la clase **Program**:

		private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	Este método creará una nueva identidad de dispositivo con el identificador **myFirstDevice** (en caso de que ya exista una identidad con el mismo identificador, simplemente la recuperará). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará el dispositivo simulado para conectarse al Centro de IoT.

7. Por último, agregue las líneas siguientes al método **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Ejecute la aplicación y anote la clave del dispositivo.

    ![][12]

> [AZURE.NOTE]Es importante tener en cuenta que el registro de identidades del Centro de IoT solo se usa para almacenar identidades de dispositivo con el fin de proteger el acceso, es decir, almacenar credenciales de seguridad y habilitar o deshabilitar el acceso del dispositivo. Los metadatos de la aplicación del dispositivo deben almacenarse en un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - Identity Registry] para obtener más información.

## Recepción de mensajes de dispositivo a nube
En esta sección, creará una aplicación de consola de Windows que lee mensajes de dispositivo a nube desde el Centro de IoT. El Centro de IoT expone un extremo compatible con [Centros de eventos ][Event Hubs Overview] para leer mensajes de dispositivo a nube. Para simplificar, en este tutorial se usa un lector simplificado que no es adecuado para la implementación de alto rendimiento. Encontrará más información sobre cómo procesar los mensajes de dispositivo a nube del Centro de IoT en el tutorial [Procesamiento de mensajes de dispositivo a nube]. Para obtener más información sobre cómo procesar los mensajes de los Centros de eventos puede hacer referencia al tutorial [Introducción a los Centros de eventos].

1. En la solución actual de Visual Studio, haga clic en **Archivo ->Agregar -> Proyecto** para crear un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **ReadDeviceToCloudMessages**.

    ![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

    Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet**.

3. Busque `WindowsAzure.ServiceBus`, haga clic en **Instalar** y acepte las condiciones de uso.

    De esta forma, se descarga, instala y agrega una referencia al [Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

        using Microsoft.Azure.Devices.Common;
        using Microsoft.ServiceBus.Messaging;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por el nombre del Centro de IoT que creó en la sección anterior y su cadena de conexión:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Agregue el método siguiente a la clase **Program**:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Este método utiliza a un cliente EventHub para recibir de todas las particiones de recepción de dispositivo a nube del Centro de IoT. Observe que, al crear un EventHubReceiver, se pasa un parámetro `DateTime.Now`. Esto crea un receptor que solo recibirá los mensajes que se envíen después de que se inicie.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[paquete NuGet del SDK de Servicios IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introducción a los Centros de eventos]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO1-->