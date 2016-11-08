---
title: Carga de archivos desde dispositivos mediante el centro de IoT | Microsoft Docs
description: Siga este tutorial para aprender a cargar archivos desde dispositivos con el centro de IoT de Azure con C#.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2016
ms.author: elioda

---
# Tutorial: cómo cargar archivos desde dispositivos a la nube con un centro de IoT
## Introducción
El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Existen tutoriales anteriores ([Introducción al Centro de IoT de Azure para .NET] y [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT]) que muestran la funcionalidad básica de comunicación de dispositivo a nube y viceversa. Asimismo, en [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT] se describe una forma de almacenar de manera confiable los mensajes de dispositivo a nube en el Almacenamiento de blobs de Azure. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos de que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta el Centro de IoT. Algunos ejemplos son los archivos de gran tamaño que contienen imágenes, vídeos, muestras de datos de vibración a alta frecuencia, o bien archivos que contienen algún tipo de datos preprocesados. Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Data Factory de Azure] o la pila [Hadoop]. Cuando se prefiera cargar un archivo desde un dispositivo en lugar de enviar eventos, se puede utilizar la funcionalidad de fiabilidad y seguridad del Centro de IoT.

Este tutorial se basa en el código de [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT] para mostrarle cómo usar las funcionalidades de carga de archivos del Centro de IoT. También explica cómo proporcionar de forma segura un URI de blob de Azure a un dispositivo para cargar un archivo, además de cómo usar las notificaciones de carga de archivos del Centro de IoT con el objetivo de desencadenar el procesamiento del archivo en el back-end de la aplicación.

Al final de este tutorial, ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT], que carga un archivo en el almacenamiento empleando un URI de SAS que proporciona el Centro de IoT.
* **ReadFileUploadNotification**, que recibe notificaciones de carga de archivos del Centro de IoT.

> [!NOTE]
> El Centro de IoT admite muchas plataformas de dispositivos y lenguajes (incluido C, Java y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para ver instrucciones paso a paso sobre cómo conectar el dispositivo al código que se muestra en este tutorial y, en general, al Centro de IoT de Azure.
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Microsoft Visual Studio 2015
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Asociación de una cuenta de Almacenamiento de Azure al Centro de IoT
Como el dispositivo simulado carga un archivo en un blob de Almacenamiento de Azure, debe tener una cuenta de [Almacenamiento de Azure] asociada al Centro de IoT. Cuando se asocia una cuenta de almacenamiento con un centro de IoT, el centro puede generar un URI de SAS que un dispositivo puede usar para cargar un archivo de manera segura en un contenedor de blobs. El servicio Centro de IoT y los SDK de dispositivo coordinan el proceso que genera el URI de SAS y permite que esté disponible para un dispositivo para cargar un archivo.

Para asociar una cuenta de Almacenamiento de Azure a su centro de IoT, siga las instrucciones que se indican en [Administración de Centros de IoT a través del portal de Azure].

## Carga de un archivo desde un dispositivo simulado
En esta sección, modificará la aplicación de dispositivo simulado que creó en [Envío de mensajes de nube a dispositivo con el Centro de IoT] para recibir mensajes de nube a dispositivo del Centro de IoT.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **SimulatedDevice**, haga clic en **Agregar** y luego haga clic en **Elemento existente**. Vaya a un archivo de imagen e inclúyalo en su proyecto. En este tutorial se supone que la imagen se llama `image.jpg`.
2. Haga doble clic con el botón derecho en ella y luego haga clic en **Propiedades**. Asegúrese de que **Copiar en el directorio de salida** está establecido en **Copiar siempre**.
   
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

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling] \(Control de errores transitorios).
> 
> 

## Recepción de una notificación de carga de archivos
En esta sección, se escribe una aplicación de consola de Windows que recibe mensajes de notificación de carga de archivos del Centro de IoT.

1. En la solución actual de Visual Studio, cree un nuevo proyecto de aplicación de Visual C# para Windows con la plantilla de proyecto de **Aplicación de consola**. Denomine al proyecto **ReadFileUploadNotification**.
   
    ![Nuevo proyecto en Visual Studio][2]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadFileUploadNotification** y luego haga clic en **Administrar paquetes NuGet**.
   
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

## Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples**, después, elija la acción **Iniciar** para **ReadFileUploadNotification** y **SimulatedDevice**.
2. Presione **F5**. Deberían iniciarse las dos aplicaciones. Debería ver que se ha completado la carga en una aplicación de consola y que la otra aplicación de consola ha recibido el mensaje de notificación de carga. Puede usar el [Portal de Azure] o el Explorador de servidores de Visual Studio para comprobar que el archivo cargado está en la cuenta de almacenamiento.
   
   ![][50]

## Pasos siguientes
En este tutorial ha aprendido a usar la funcionalidad de carga de archivos del Centro de IoT para simplificar la carga de archivos desde los dispositivos. Puede explorar las características del Centro de IoT y distintos escenarios con los siguientes artículos:

* [Creación de un Centro de IoT con Powershell][lnk-create-hub]
* [Introducción a C SDK][lnk-c-sdk]
* [SDK de Centro de IoT][lnk-sdks]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Diseño de la solución][lnk-design]
* [Exploración de la administración de dispositivos desde Centro de IoT de Azure con la IU de ejemplo][lnk-dmui]
* [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
* [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal de Azure]: https://portal.azure.com/

[Data Factory de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Envío de mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT]: iot-hub-csharp-csharp-process-d2c.md
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-csharp-csharp-getstarted.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Administración de Centros de IoT a través del portal de Azure]: iot-hub-manage-through-portal.md#file-upload
[paquete NuGet del SDK de servicio de IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->