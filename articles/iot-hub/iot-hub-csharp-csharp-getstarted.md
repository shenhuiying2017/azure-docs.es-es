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
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introducción al Centro de IoT de Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introducción

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional confiable y segura entre millones de dispositivos de Internet de las cosas (IoT) y un back-end de soluciones. Uno de los mayores desafíos que plantean los proyectos de IoT es cómo conectar dispositivos al back-end de la solución de manera segura y confiable. Para abordar este desafío, el Centro de IoT:

- Ofrece una mensajería confiable de gran escala de dispositivo a nube y de nube a dispositivo.
- Habilita las comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
- Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar el Portal de Azure para crear un Centro de IoT.
- Crear una identidad de dispositivo en el Centro de IoT.
- Crear un dispositivo simulado que envía la telemetría a su back-end en la nube y recibe los comandos del back-end en la nube.

Al final de este tutorial tendrá tres aplicaciones de consola de Windows:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **SimulatedDevice**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo mediante el protocolo AMQPS.

> [AZURE.NOTE] El artículo [SDK de Centro de IoT][lnk-hub-sdks] proporciona información acerca de los SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya se creó el Centro de IoT y tiene el nombre de host y la cadena de conexión que necesita para completar el resto del tutorial.

## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Windows que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para obtener más información. Al ejecutar esta aplicación de consola, genera y una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Denomine el proyecto **CreateDeviceIdentity**.

	![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CreateDeviceIdentity** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **microsoft.azure.devices**, haga clic en **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso.

	![][11]

4. De esta forma, se descarga, instala y agrega una referencia al paquete NuGet [del SDK de Servicio IoT de Microsoft Azure][lnk-nuget-service-sdk] y a sus dependencias.

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección anterior:

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

	Este método crea una nueva identidad de dispositivo con el id. **myFirstDevice** (si ese id. de dispositivo ya existe en el registro, el código simplemente recupera la información del dispositivo existente). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en el dispositivo simulado para conectarse al Centro de IoT.

7. Por último, agregue las líneas siguientes al método **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Ejecute la aplicación y anote la clave del dispositivo.

    ![][12]

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Windows que lee los mensajes de dispositivo a nube del Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. En el tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial] se proporciona información adicional acerca de cómo procesar los mensajes desde los Centros de eventos. Dicha información se puede aplicar a los puntos de conexión compatibles con Centros de eventos de Centro de IoT.

> [AZURE.NOTE] El punto de conexión compatible con los Centros de eventos para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQPS.

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **ReadDeviceToCloudMessages**.

    ![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadDeviceToCloudMessages** y luego haga clic en **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte los términos de uso.

    De esta forma, se descarga, se instala y se agrega una referencia al [Bus de servicio de Azure][lnk-servicebus-nuget]. Este paquete permite que la aplicación para se conecte al punto de conexión compatible con el Centro de eventos en el Centro de IoT.

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección *Creación de un Centro de IoT*:

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

    Este método usa una instancia de **EventHubReceiver** para recibir mensajes de todas las particiones de recepción de dispositivo a nube del Centro de IoT. Observe cómo pasar un parámetro `DateTime.Now` al crear el objeto **EventHubReceiver** para que solo reciba los mensajes enviados después de iniciarse. Esto es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes, pero en un entorno de producción el código debe asegurarse de que se procesan todos los mensajes. Consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-process-d2c-tutorial] para más información.

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

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **SimulatedDevice**.

   	![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar** y busque **Microsoft.Azure.Devices.Client**, haga clic en **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-device-nuget] y sus dependencias.

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por el nombre de host del Centro de IoT que recuperó en la sección *Creación de un Centro de IoT* y la clave de dispositivo que recuperó en la sección *Creación de una identidad de dispositivo*, respectivamente:

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

	Este método envía un nuevo mensaje de dispositivo a nube cada segundo. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  De forma predeterminada, el método **Create** crea una instancia **DeviceClient** que usa el protocolo AMQP para comunicarse con el Centro de IoT. Para usar el protocolo HTTPS, use la invalidación del método **Create** que permite especificar el protocolo. Si decide usar el protocolo HTTPS, debe agregar también el paquete de NuGet **Microsoft.AspNet.WebApi.Client** al proyecto para incluir el espacio de nombres **System.Net.Http.Formatting**.

Este tutorial le guiará por los pasos para crear un cliente de dispositivo de Centro de IoT. Como alternativa, puede utilizar la extensión de Visual Studio [Connected Service for Azure IoT Hub][lnk-connected-service] para agregar el código necesario a la aplicación de cliente del dispositivo.


> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones en Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación cliente y, después, haga clic en **Establecer como proyecto de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione la **Acción** **Iniciar** para los proyectos **ReadDeviceToCloudMessages** y **SimulatedDevice**.

   	![][41]

2.	Presione **F5** para iniciar la ejecución de ambas aplicaciones. La salida de la consola de la aplicación **SimulatedDevice** muestra los mensajes que el dispositivo simulado envía a su Centro de IoT y la salida de la consola de la aplicación **ReadDeviceToCloudMessages** muestra los mensajes que el Centro de IoT recibe.

   	![][42]

3. El icono **Uso** del [Portal de Azure][lnk-portal] muestra el número de mensajes enviados al centro:

    ![][43]


## Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Esta identidad de dispositivo se usó para habilitar la aplicación del dispositivo simulado para enviar al centro los mensajes de dispositivo a la nube y creó otra aplicación que muestra los mensajes recibidos por el centro. Puede seguir explorando las características del Centro de IoT y otros escenarios en los tutoriales siguientes:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d-tutorial] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Cómo cargar archivos desde dispositivos a la nube ][lnk-upload-tutorial] describe un patrón que usa mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

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

<!---HONumber=AcomDC_0511_2016-->