## Creación de una aplicación de dispositivo simulado

En esta sección, escribirá una aplicación de consola de Windows que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En la solución actual de Visual Studio, haga clic en **Archivo ->Agregar -> Proyecto** para crear un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **SimulatedDevice**.

   	![][30]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**.

	De este modo aparece la ventana Administrar paquetes de NuGet.

3. Busque `Microsoft Azure Devices Client`, haga clic en **Instalar** y acepte las condiciones de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Dispositivos IoT de Azure].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por el URI del Centro de IoT y la clave de dispositivo recuperada en las secciones **Creación de un Centro de IoT** y **Creación de una identidad de dispositivo** secciones, respectivamente:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

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

                Thread.Sleep(1000);
            }
        }

	Este método envía un nuevo mensaje de dispositivo a nube cada segundo, que contiene un objeto JSON serializado con deviceId y un número generado aleatoriamente, que representa un sensor de velocidad del viento simulado.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, se recomienda implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal como se sugiere en el artículo de MSDN [Control de errores transitorios].

<!-- Links -->

[paquete NuGet del SDK de Dispositivos IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Control de errores transitorios]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO1-->