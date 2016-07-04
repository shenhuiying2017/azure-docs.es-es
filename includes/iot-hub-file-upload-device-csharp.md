## Asociación de una cuenta de Almacenamiento de Azure al Centro de IoT

Como el dispositivo simulado cargará un archivo en un blob de Almacenamiento de Azure, debe tener una cuenta de Almacenamiento de Azure asociada al Centro de IoT. Puede utilizar una cuenta de almacenamiento que ya exista o seguir las instrucciones que se indican en [Acerca de las cuentas de almacenamiento de Azure] para crear una nueva. Puede asociar una cuenta de Almacenamiento de Azure al Centro de IoT siguiendo las instrucciones que se describen en [Administración de Centros de IoT a través del portal de Azure].

## Carga de un archivo desde un dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Envío de mensajes de nube a dispositivo con el Centro de IoT] para recibir mensajes de nube a dispositivo del Centro de IoT.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **SimulatedDevice**, haga clic en **Agregar** y luego haga clic en **Elemento existente**. Vaya a un archivo de imagen e inclúyalo en su proyecto. En este tutorial se supone que la imagen se llama `image.jpg`.

2. Haga doble clic en ella y luego haga clic en **Propiedades**. Asegúrese de que **Copiar en el directorio de salida** está establecido en **Copiar siempre**.

    ![][1]

3. En el archivo **Program.cs**, agregue las siguientes instrucciones al principio del archivo:

        using System.IO;

4. Agregue el método siguiente a la clase **Program**:
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    El método `UploadToBlobAsync` toma el nombre de archivo y el origen de streaming del archivo que se va a cargar y administra la carga en el almacenamiento. La aplicación de consola muestra el tiempo que se tarda en cargar el archivo.

5. Agregue el siguiente método en el método **Main**, inmediatamente delante de la línea `Console.ReadLine()`:

        SendToBlobAsync();

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] \(Control de errores transitorios).

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Acerca de las cuentas de almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Administración de Centros de IoT a través del portal de Azure]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->