---
title: "Inicio rápido de Azure: Carga, descarga y enumeración de blobs en Azure Storage mediante .NET | Microsoft Docs"
description: "En esta guía de inicio rápido, puede crear una cuenta de almacenamiento y un contenedor. Después, puede usar la biblioteca de clientes de almacenamiento para .NET a fin de cargar un blob en Azure Storage, descargar un blob o enumerar los blobs de un contenedor."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: tamram
ms.openlocfilehash: 8d1f09a39e865500aa8e4d093473d4989f134c3d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Inicio rápido: Carga, descarga y enumeración de blobs mediante .NET

En este tutorial de inicio rápido, aprenderá a usar la biblioteca de cliente de .NET para que Azure Storage pueda cargar, descargar y enumerar blobs en bloques en un contenedor.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Para completar esta guía de inicio rápido, cree primero una cuenta de Azure Storage en [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Si necesita ayuda para crearla, consulte [Creación de una cuenta de almacenamiento](../common/storage-quickstart-create-account.md).

A continuación, descargue e instale .NET Core 2.0 para su sistema operativo. También puede elegir instalar un editor y usarlo con el sistema operativo.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Instale [.NET Core para Windows](https://www.microsoft.com/net/download/windows/build) 
- Opcionalmente, instale [Visual Studio para Windows](https://www.visualstudio.com/) 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Instale [.NET Core para Linux](https://www.microsoft.com/net/download/linux/build)
- Opcionalmente, instale [Visual Studio Code](https://www.visualstudio.com/) y la [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Instale [.NET Core para macOS](https://www.microsoft.com/net/download/macos/build).
- Opcionalmente, instale [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La aplicación de ejemplo utilizada en este tutorial de inicio rápido es una aplicación de consola básica. Puede explorar la aplicación de ejemplo en [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona el repositorio en la carpeta git local. Para abrir la solución de Visual Studio, busque la carpeta storage-blobs-dotnet-quickstart, ábrala y haga doble clic en storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

Para ejecutar la aplicación, debe proporcionar la cadena de conexión de la cuenta de almacenamiento. Esta cadena de conexión se puede almacenar en una variable de entorno de la máquina local que ejecuta la aplicación. Cree la variable de entorno con uno de los siguientes comandos, en función del sistema operativo. Reemplace `<yourconnectionstring>` por la cadena de conexión real.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Edite .bash_profile y agregue la variable de entorno:

```
export STORAGE_CONNECTION_STRING=
```

Después de agregar la variable de entorno, cierre la sesión y vuelva a abrirla para que los cambios surta efecto. Como alternativa, puede escribir "source .bash_profile" desde su terminal.

---

## <a name="run-the-sample"></a>Ejecución del ejemplo

Este ejemplo crea un archivo de prueba en la carpeta **Mis documentos** local y lo carga en Blob Storage. Después, en el ejemplo se enumeran los blobs del contenedor y descarga el archivo con un nombre nuevo para que pueda comparar los archivos antiguo y nuevo. 

Desplácese hasta el directorio de la aplicación y ejecute la aplicación con el comando `dotnet run`.

```
dotnet run
```

La salida mostrada es similar a la del ejemplo siguiente:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Al presionar la tecla **Entrar**, la aplicación elimina el contenedor de almacenamiento y los archivos. Antes de eliminarlos, compruebe si la carpeta **Mis documentos** contiene los dos archivos. Puede abrirlos y ver que son idénticos. Copie la dirección URL del blob de la ventana de la consola y péguela en un explorador para ver el contenido del blob.

Después de haber comprobado los archivos, presione cualquier tecla para finalizar la demostración y eliminar los archivos de prueba. Ahora que sabe lo que hace el ejemplo, abra el archivo Program.cs para examinar el código. 

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

Después, explore el código de ejemplo para poder saber cómo funciona.

### <a name="try-parsing-the-connection-string"></a>Pruebe a analizar la cadena de conexión

Lo primero que el ejemplo realiza es comprobar que la variable de entorno contiene una cadena de conexión que se puede analizar para crear un objeto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) que señala a la cuenta de almacenamiento. Para comprobar que la cadena de conexión es válida, use el método [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Si **TryParse** funciona correctamente, inicializa la variable *storageAccount* y devuelve el valor **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Cree el contenedor y establezca los permisos

Después, el ejemplo crea un contenedor y establece sus permisos para que los blobs del contenedor sean públicos. Si un blob es público, cualquier cliente puede acceder de forma anónima.

Para crear el contenedor, en primer lugar cree una instancia del objeto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) que apunte a Blob Storage en la cuenta de almacenamiento. Cree una instancia del objeto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) y, después, cree el contenedor. 

En este caso, el ejemplo llama al método [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) para crear el contenedor. Un valor de GUID se anexa al nombre de contenedor para asegurarse de que sea único. En un entorno de producción, a menudo es preferible utilizar el método [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) para crear un contenedor solo si no existe y para evitar conflictos de nomenclatura.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información acerca de los contenedores de nomenclatura y los blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y realización de referencias a contenedores, blobs y metadatos).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Después, el ejemplo carga un archivo local en un blob en bloques. El código de ejemplo obtiene una referencia a un objeto **CloudBlockBlob** mediante una llamada al método [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) en el contenedor creado en la sección anterior. Luego carga el archivo seleccionado en el blob mediante una llamada al método [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Esta operación crea el blob si no existe y lo sobrescribe, en caso de que ya exista. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

El ejemplo enumera los blobs del contenedor mediante el método [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). En el caso del ejemplo, solo se ha agregado un blob al contenedor, por lo que la operación de enumeración devuelve simplemente dicho blob.

Si hay demasiados blobs para devolverlos en una sola llamada (de manera predeterminada más de 5000), el método **ListBlobsSegmentedAsync** devuelve un segmento del conjunto total de resultados y un token de continuación. Para recuperar el siguiente segmento de blobs, proporciónelo en el token de continuación devuelto por la llamada anterior, etc. hasta que el token de continuación es nulo. Un token de continuación nulo indica que se han recuperado todos los blobs. El código de ejemplo muestra cómo utilizar el token de continuación para los procedimientos recomendados.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Descargar blobs

A continuación, el ejemplo descarga el blob creado anteriormente en el sistema de archivos local mediante el método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). El código de ejemplo agrega el sufijo "_DOWNLOADED" al nombre del blob para que pueda ver ambos archivos en el sistema de archivos local.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Limpieza de recursos

El ejemplo limpia los recursos que creó mediante la eliminación de todo el contenedor con [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Si lo desea, también puede eliminar los archivos locales.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>Recursos para el desarrollo de aplicaciones .NET con blobs

Consulte estos recursos adicionales para el desarrollo de .NET con Blob Storage:

### <a name="binaries-and-source-code"></a>Archivos binarios y código fuente

- Descargue el paquete NuGet para la versión más reciente de la [biblioteca de cliente .NET de Storage](https://www.nuget.org/packages/WindowsAzure.Storage/). 
- Vea el [código fuente de la biblioteca de cliente .NET de Storage](https://github.com/Azure/azure-storage-net) en GitHub.

### <a name="client-library-reference-and-samples"></a>Referencia a la biblioteca de clientes y ejemplos

- Para más información acerca de la biblioteca de cliente, consulte la [referencia de la API de Azure Storage para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) .
- Explore los [ejemplos de Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) escritos mediante la biblioteca de cliente .NET de Storage.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha aprendido a cargar, descargar y enumerar blobs mediante .NET. 

Para aprender a crear una aplicación web que cargue una imagen en el Blob Storage, diríjase a [Carga de datos de imagen en la nube con Azure Storage](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-dotnet-how-to-use-blobs.md)

- Para más información sobre .NET Core, consulte [Get started with .NET in 10 minutes](https://www.microsoft.com/net/learn/get-started/) (Introducción a .NET en 10 minutos).
- Para explorar una aplicación de ejemplo que puede implementar desde Visual Studio para Windows, consulte el artículo [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) (Ejemplo de aplicación web de galería fotográfica .NET con Azure Blob Storage).
 