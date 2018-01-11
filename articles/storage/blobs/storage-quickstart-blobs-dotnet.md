---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante .NET | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante .NET"
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2017
ms.author: tamram
ms.openlocfilehash: 5020f070a8eb9215f175fc3ff3a905cff28ce37f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Transferencia de objetos a y desde Azure Blob Storage mediante .NET

En este tutorial de inicio rápido, aprenderá a usar la biblioteca de cliente de .NET para que Azure Storage pueda cargar, descargar y enumerar blobs en bloques en un contenedor.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido:

* Instalación de .NET Core 2.0 para [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) o [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La aplicación de ejemplo utilizada en este tutorial de inicio rápido es una aplicación de consola básica. 

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona el repositorio en la carpeta git local. Para abrir la solución de Visual Studio, busque la carpeta storage-blobs-dotnet-quickstart, ábrala y haga doble clic en storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

En la aplicación, debe proporcionar la cadena de conexión para la cuenta de almacenamiento. Se recomienda almacenar esta cadena de conexión dentro de una variable de entorno en la máquina local que ejecuta la aplicación. Siga uno de los estos ejemplos dependiendo de su sistema operativo para crear la variable de entorno.  Reemplace \<suCadenaDeConexión\> por la cadena de conexión real.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

En este ejemplo se crea un archivo de prueba en MyDocuments, se carga a Blob Storage, se enumeran los blobs del contenedor y luego se descarga el archivo con un nombre nuevo para poder comparar los archivos antiguos y nuevos. 

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

Al presionar cualquier tecla para continuar, se elimina el contenedor de almacenamiento y los archivos. Antes de continuar, compruebe los dos archivos en Mis documentos. Puede abrirlos y ver que son idénticas. Copie la dirección URL para el blob fuera de la ventana de consola y péguela en un explorador para ver el contenido del archivo de Blob Storage.

También puede usar una herramienta como [Explorador de Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver los archivos de Blob Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento. 

Después de haber comprobado los archivos, presione cualquier tecla para finalizar la demostración y eliminar los archivos de prueba. Ahora que sabe lo que hace el ejemplo, abra el archivo Program.cs para examinar el código. 

## <a name="understand-the-sample-code"></a>Descripción del código de ejemplo

A continuación, explicaremos el código de ejemplo para ayudarle a comprender saber cómo funciona.

### <a name="get-references-to-the-storage-objects"></a>Obtención de referencias a los objetos de almacenamiento

Lo primero que hay que hacer es crear las referencias a los objetos usados para acceder a Blob Storage y administrarlo. Estos objetos dependen unos de otros: cada uno es utilizado por el siguiente de la lista.

* Cree una instancia del objeto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) que apunte a la cuenta de almacenamiento.

* Cree una instancia del objeto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet), que apunte al servicio de Blob de la cuenta de almacenamiento.

* Cree una instancia del objeto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), que representa el contenedor al que está accediendo. Los contenedores se usan para organizar los blobs al igual que se usan las carpetas en el equipo para organizar los archivos.

Una vez que tenga [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), puede crear una instancia del objeto [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet) que apunte al blob específico en el que está interesado y realizar una operación de carga, descarga, copia, etc.

> [!IMPORTANT]
> Los nombres de contenedor deben estar en minúsculas. Para más información sobre la nomenclatura de contenedores y blobs, consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Asignación de nombres y referencia a contenedores, blobs y metadatos).

En esta sección, se crean instancias de los objetos, se crea un nuevo contenedor y luego se establecen permisos en el contenedor para que los blobs sean públicos y se pueda acceder a ellos con tan solo una dirección URL. El contenedor se denomina **quickstartblobs**.

En este ejemplo se utiliza [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) porque queremos crear un nuevo contenedor cada vez que se ejecuta el ejemplo. En un entorno de producción donde se usa el mismo contenedor en toda una aplicación, lo más recomendable es llamar solamente a **CreateIfNotExists** una vez. O bien crear el contenedor con antelación para que no necesite crearlo en el código.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son los que se usan con más frecuencia y serán los que utilicemos en este tutorial de inicio rápido.

Para cargar un archivo a un blob, obtenga una referencia al blob en el contenedor de destino. Cuando tenga la referencia de blob, puede cargar datos en él mediante [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Esta operación crea el blob si todavía no existe o lo sobrescribe si ya existe.

El código de ejemplo crea un archivo local que se usará para la carga y descarga, almacenando el archivo que se va a cargar como **fileAndPath** y el nombre del blob en **localFileName**. En el ejemplo siguiente se carga el archivo al contenedor denominado **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Existen varios métodos de carga que puede usar con Blob Storage. Por ejemplo, si tiene una secuencia de memoria, puede utilizar el método UploadFromStreamAsync en lugar de UploadFromFileAsync.

Los blobs en bloques pueden ser cualquier tipo de archivo de texto o binario. Los blobs en páginas se utilizan principalmente para los archivos VHD usados para respaldar VM IaaS. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los objetos almacenados en Blob Storage son blobs en bloques.

### <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Puede obtener una lista de archivos del contenedor mediante [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). El código siguiente recupera la lista de blobs, luego los recorre y se muestran los URI de los blobs encontrados. Puede copiar el URI desde la ventana de comandos y pegarlo en un explorador para ver el archivo.

Si tiene hasta 5000 blobs en el contenedor, todos los nombres de blob se recuperan en una llamada a ListBlobsSegmentedAsync. Si tiene más de 5000 blobs en el contenedor, el servicio recupera la lista en conjuntos de 5000 hasta que todos los nombres de blob se hayan recuperado. Por tanto, la primera vez que se llama a esta API, devuelve los primeros 5000 nombres de blob y un token de continuación. La segunda vez, se proporciona el token, y el servicio recupera el siguiente conjunto de nombres de blob, y así sucesivamente hasta que el token de continuación es null, lo que indica que se han recuperado todos los nombres de blob.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Descargar blobs

Descargue blobs al disco local mediante [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

El código siguiente descarga el blob cargado en una sección anterior, agregando el sufijo "_DOWNLOADED" al nombre de blob para que pueda ver ambos archivos en el disco local.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita los blobs cargados en este tutorial de inicio rápido, puede eliminar todo el contenedor mediante [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Elimine también los archivos creados si ya no son necesarios.

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

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage mediante .NET. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-dotnet-how-to-use-blobs.md)

Para obtener ejemplos de código de Azure Storage adicionales que se puedan descargar y ejecutar, consulte la lista de [Ejemplos de Azure Storage con .NET](../common/storage-samples-dotnet.md).

Para más información sobre el Explorador de Storage y los blobs, consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
