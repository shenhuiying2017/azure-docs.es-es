<properties
	pageTitle="Introducción al Centro de IoT de Azure para C# | Microsoft Azure"
	description="Tutorial de introducción al Centro de IoT de Azure con C# Utilice Centro de IoT de Azure y C# con los SDK de IoT de Microsoft Azure para implementar una solución de Internet de las cosas."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Introducción al Centro de IoT de Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de Windows:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **SimulatedDevice**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQPS.

> [AZURE.NOTE] Para más información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para ejecutarse en dispositivos y en el back-end de la solución, consulte [SDK de Centro de IoT][lnk-hub-sdks].

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya se creó el Centro de IoT y ya tiene el nombre de host y la cadena de conexión que necesita para completar el resto del tutorial.

## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Windows que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Para más información, consulte la sección "Registro de identidad del dispositivo" de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. En Visual Studio, agregue un nuevo proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Denomine el proyecto **CreateDeviceIdentity**.

	![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CreateDeviceIdentity** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. De esta forma, se descarga, instala y agrega una referencia al paquete NuGet [del SDK de Servicio IoT de Microsoft Azure][lnk-nuget-service-sdk] y a sus dependencias.

	![Ventana del Administrador de paquetes NuGet][11]

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Agregue los campos siguientes a la clase **Program**. Sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección anterior.

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Agregue el método siguiente a la clase **Program**:

		private static async Task AddDeviceAsync()
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

	Este método crea una nueva identidad de dispositivo con el identificador. **myFirstDevice**. (Si el identificador de dispositivo ya existe en el Registro, el código simplemente recupera la información existente del dispositivo.) A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en el dispositivo simulado para conectarse al Centro de IoT.

7. Por último, agregue las líneas siguientes al método **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Ejecute la aplicación y anote la clave del dispositivo.

    ![Clave de dispositivo generada por la aplicación][12]

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Windows que lee los mensajes de dispositivo a nube del Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos de Azure][lnk-event-hubs-overview] para permitirle leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El tutorial [Procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. Para más información sobre cómo procesar los mensajes de los Centros de eventos, consulte el tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial]. (Este tutorial es aplicable a los puntos de conexión compatibles con los Centros de eventos del Centro de IoT).

> [AZURE.NOTE] El punto de conexión compatible con los Centros de eventos para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQPS.

1. En Visual Studio, agregue un nuevo proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **ReadDeviceToCloudMessages**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadDeviceToCloudMessages** y luego haga clic en **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **WindowsAzure.ServiceBus**, seleccione **Instalar** y acepte los términos de uso. De esta forma, se descarga, se instala y se agrega una referencia al [Bus de servicio de Azure][lnk-servicebus-nuget], con todas sus dependencias. Este paquete permite que la aplicación se conecte al punto de conexión compatible con los Centros de eventos en el Centro de IoT.

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Agregue los campos siguientes a la clase **Program**. Sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección "Creación de un Centro de IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Agregue el método siguiente a la clase **Program**:

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Este método usa una instancia de **EventHubReceiver** para recibir mensajes de todas las particiones de recepción de dispositivo a nube del Centro de IoT. Observe cómo pasar un parámetro `DateTime.Now` al crear el objeto **EventHubReceiver** para que solo reciba los mensajes enviados después de iniciarse. Esto es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes, pero en un entorno de producción, el código debe asegurarse de que se procesan todos los mensajes. Para más información, consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial].

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Windows que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En Visual Studio, agregue un nuevo proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **SimulatedDevice**.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **Microsoft.Azure.Devices.Client**, haga clic en **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-device-nuget] y sus dependencias.

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Agregue los campos siguientes a la clase **Program**. Sustituya los valores de marcador de posición por el nombre de host del Centro de IoT que recuperó en la sección "Creación de un Centro de IoT" y la clave de dispositivo que recuperó en la sección "Creación de una identidad de dispositivo", respectivamente:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Agregue el método siguiente a la clase **Program**:

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

	Este método envía un nuevo mensaje de dispositivo a nube cada segundo. El mensaje contiene un objeto JSON serializado con el identificador de dispositivo y un número generado aleatoriamente para simular un sensor de velocidad del viento.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  De forma predeterminada, el método **Create** crea una instancia **DeviceClient** que usa el protocolo AMQP para comunicarse con el Centro de IoT. Para usar el protocolo HTTPS, use la invalidación del método **Create** que permite especificar el protocolo. Si usa el protocolo HTTPS, debe agregar también el paquete NuGet **Microsoft.AspNet.WebApi.Client** al proyecto para incluir el espacio de nombres **System.Net.Http.Formatting**.

Este tutorial le guiará por los pasos para crear un cliente de dispositivo de Centro de IoT. También puede utilizar la extensión de Visual Studio [Connected Service for Azure IoT Hub][lnk-connected-service] (Servicio conectado para Centro de IoT de Azure) para agregar el código necesario a la aplicación de cliente del dispositivo.


> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione **Iniciar** como la acción para los proyectos **ProcessDeviceToCloudMessages** y **SimulatedDevice**.

   	![Propiedades del proyecto de inicio][41]

2.	Presione **F5** para iniciar la ejecución de ambas aplicaciones. La salida de la consola de la aplicación **SimulatedDevice** muestra los mensajes que envía el dispositivo simulado al Centro de IoT. La salida de la consola de la aplicación **ProcessDeviceToCloudMessages** muestra los mensajes que recibe el Centro de IoT.

   	![Salida de la consola de aplicaciones][42]

3. El icono **Uso** del [Portal de Azure][lnk-portal] muestra el número de mensajes enviados al centro:

    ![Icono Uso del Portal de Azure][43]


## Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo simulado para enviar al centro los mensajes de dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro. Puede seguir explorando las características del Centro de IoT y otros escenarios en los tutoriales siguientes:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d-tutorial] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- El [Tutorial: cómo cargar archivos desde dispositivos a la nube con un centro de IoT][lnk-upload-tutorial] muestra cómo cargar archivos desde los dispositivos.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!---HONumber=AcomDC_0622_2016-->