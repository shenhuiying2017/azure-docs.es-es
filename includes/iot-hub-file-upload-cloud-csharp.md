## Recepción de una notificación de carga de archivos
En esta sección, se escribe una aplicación de consola de Windows que recibe mensajes de notificación de carga de archivos del Centro de IoT.

1. En la solución actual de Visual Studio, cree un nuevo proyecto de aplicación de Visual C# para Windows con la plantilla de proyecto **Aplicación de consola**. Denomine al proyecto **ReadFileUploadNotification**.
   
    ![Nuevo proyecto en Visual Studio][2]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadFileUploadNotification** y luego haga clic en **Administrar paquetes de NuGet**.
   
    De este modo aparece la ventana Administrar paquetes de NuGet.
3. Busque `Microsoft.Azure.Devices`, haga clic en **Instalar** y acepte las condiciones de uso.
   
    Se descarga, instala y agrega una referencia al [paquete NuGet del SDK de servicio de IoT de Azure] en el proyecto **ReadFileUploadNotification**.
4. En el archivo **Program.cs**, agregue las siguientes instrucciones al principio del archivo:
   
        using Microsoft.Azure.Devices;
5. Agregue los campos siguientes a la clase **Program**. Sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT de [Introducción al Centro de IoT]\:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Agregue el método siguiente a la clase **Program**:
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    Tenga en cuenta que el patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.
7. Por último, agregue las líneas siguientes al método **Main**:
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[paquete NuGet del SDK de servicio de IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Introducción al Centro de IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->