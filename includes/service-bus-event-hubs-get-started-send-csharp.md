## Envío de mensajes a Centros de eventos
En esta sección se escribirá una aplicación de consola Windows para enviar eventos al centro de eventos.

1. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **Remitente**.

   	![][7]

2. En el Explorador de soluciones, haga clic con el botón secundario en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet para la solución...**. 

	Esto muestra el cuadro de diálogo Administrar paquetes de NuGet.

3. Busque "Bus de servicio de Microsoft Azure", haga clic en **Instalar** y acepte los términos de uso. 

	![][8]

	De esta forma, se descarga, instala y agrega una referencia al <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">paquete de NuGet de la biblioteca del Bus de servicio de Azure</a>.

4. Agregue la siguiente instrucción "using" en la parte superior del archivo **Program.cs**:

		using Microsoft.ServiceBus.Messaging;

5. Agregue los siguientes campos "estático" a la clase **Programa**; para ello, sustituya los valores con el nombre del Centro de eventos creado en la sección anterior y la cadena de conexión con derechos **enviar**:

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Agregue el método siguiente a la clase **Programa**:

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	Este método enviará continuamente los eventos al Centro de eventos con un retraso de 200 ms.

7. Por último, agregue las líneas siguientes al método **Principal**:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png
