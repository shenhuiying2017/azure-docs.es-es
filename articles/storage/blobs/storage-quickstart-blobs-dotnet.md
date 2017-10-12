---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante .NET | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante .NET"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.openlocfilehash: 97bacc2c1285fe4a467a54f224bb9fabbd851fee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Transferencia de objetos a y desde Azure Blob Storage mediante .NET

En este tutorial de inicio rápido, aprenderá a usar C#.NET para cargar, descargar y enumerar blobs en bloques en un contenedor en Azure Blob Storage en Windows.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido:

* Instale [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) con la carga de trabajo siguiente:
    - **Desarrollo de Azure**

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Creación de una cuenta de almacenamiento mediante Azure Portal

En primer lugar, cree una nueva cuenta de almacenamiento de uso general que se utilizará para este tutorial de inicio rápido. 

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure. 
2. En el menú de concentrador, seleccione **Nuevo** > **Almacenamiento** > **Cuenta de almacenamiento: blob, archivo, tabla, cola**. 
3. Escriba un nombre para la cuenta de almacenamiento. El nombre deben tener entre tres y 24 caracteres, y solo pueden contener números y letras minúsculas. También debe ser único.
4. Establezca `Deployment model` en **Administrador de recursos**.
5. Establezca `Account kind` en **Uso general**.
6. Establezca `Performance` en **Estándar**. 
7. Establezca `Replication` en **Almacenamiento con redundancia local (LRS)**.
8. Establezca `Storage service encryption` en **Deshabilitado**.
9. Establezca `Secure transfer required` en **Deshabilitado**.
10. Seleccione su suscripción. 
11. Para `resource group`, cree uno nuevo y asígnele un nombre único. 
12. Seleccione el valor de `Location` que desee usar para la cuenta de almacenamiento.
13. Active **Anclar al panel** y haga clic en **Crear** para crear la cuenta de almacenamiento. 

Después de crear la cuenta de almacenamiento, se ancla al panel. Haga clic en ella para abrirla. En CONFIGURACIÓN, haga clic en **Claves de acceso**. Seleccione una clave, copie la CADENA DE CONEXIÓN en el Portapapeles y péguela en el Bloc de notas para utilizarla posteriormente.

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo

La aplicación de ejemplo utilizada en este tutorial de inicio rápido es una aplicación de consola básica. 

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona el repositorio en la carpeta git local. Para abrir la solución de Visual Studio, busque la carpeta storage-blobs-dotnet-quickstart, ábrala y haga doble clic en storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.

En la aplicación, debe proporcionar la cadena de conexión para la cuenta de almacenamiento. Abra el archivo `app.config` en el Explorador de soluciones en Visual Studio. Busque la entrada StorageConnectionString. Para **value**, reemplace todo el valor de la cadena de conexión por la que guardó en Azure Portal en el Bloc de notas. Cuando termine, debería ver algo parecido a lo siguiente.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=youraccountname;
    AccountKey=7NGE5jasdfdRzASDFNOMEx1u20W/thisisjustC/anexampleZK/Rt5pz2xNRrDckyv8EjB9P1WGF==" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

En este ejemplo se crea un archivo de prueba en MyDocuments, se carga a Blob Storage, se enumeran los blobs del contenedor y luego se descarga el archivo con un nombre nuevo para poder comparar los archivos antiguos y nuevos. 

Ejecute el ejemplo presionando F5. La salida se muestra en una ventana de consola similar a la siguiente: 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

Al presionar cualquier tecla para continuar, se elimina el contenedor de almacenamiento y los archivos. Antes de continuar, compruebe MyDocuments para los dos archivos para ver si puede abrirlos y si son idénticos. Copie la dirección URL para el blob fuera de la ventana de consola y péguela en un explorador para ver el contenido del archivo de Blob Storage.

También puede usar una herramienta como [Explorador de Azure Storage](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver los archivos de Blob Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento. 

Después de haber comprobado los archivos, presione cualquier tecla para finalizar la demostración y eliminar los archivos de prueba. Ahora que sabe lo que hace el ejemplo, abra el archivo Program.cs para examinar el código. 

## <a name="get-references-to-the-storage-objects"></a>Obtención de referencias a los objetos de almacenamiento

Lo primero que hay que hacer es crear las referencias a los objetos usados para acceder a Blob Storage y administrarlo. Estos objetos dependen unos de otros: cada uno es utilizado por el siguiente de la lista.

* Cree una instancia del objeto **CloudStorageAccount** que apunte a la cuenta de almacenamiento. 

* Cree una instancia del objeto **CloudBlobClient**, que apunta a Blob service en la cuenta de almacenamiento. 

* Cree una instancia del objeto **CloudBlobContainer**, que representa el contenedor al que está accediendo. Los contenedores se usan para organizar los blobs al igual que se usan las carpetas en el equipo para organizar los archivos.

Una vez que tenga **CloudBlobContainer**, puede crear una instancia del objeto **CloudBlockBlob** que apunte al blob específico en el que está interesado y realizar una operación de carga, descarga, copia, etc.

En esta sección, se crean instancias de los objetos, se crea un nuevo contenedor y luego se establecen permisos en el contenedor para que los blobs sean públicos y se pueda acceder a ellos con tan solo una dirección URL. El contenedor se denomina **quickstartblobs**. 

En este ejemplo se utiliza CreateIfNotExists porque queremos crear un nuevo contenedor cada vez que se ejecuta el ejemplo. En un entorno de producción donde se usa el mismo contenedor en toda una aplicación, lo más recomendable es llamar solamente a CreateIfNotExists una vez o crear el contenedor antes de tiempo de forma que no sea necesario crearlo en el código.

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son los que se usan con más frecuencia y serán los que utilicemos en este tutorial de inicio rápido. 

Para cargar un archivo a un blob, obtenga una referencia al blob en el contenedor de destino. Cuando tenga la referencia de blob, puede cargar datos en él mediante [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Esta operación crea el blob si todavía no existe o lo sobrescribe si ya existe.

El código de ejemplo crea un archivo local que se usará para la carga y descarga, almacenando el archivo que se va a cargar como **fileAndPath** y el nombre del blob en **localFileName**. En el ejemplo siguiente se carga el archivo al contenedor denominado **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Existen varios métodos de carga que puede usar con Blob Storage. Por ejemplo, si tiene una secuencia de memoria, puede utilizar el método UploadFromStreamAsync en lugar de UploadFromFileAsync. 

Los blobs en bloques pueden tener un tamaño de hasta 4,7 TB y pueden ser cualquier cosa, desde hojas de cálculo de Excel a archivos de vídeo grandes. Los blobs en páginas se utilizan principalmente para los archivos VHD usados para respaldar VM IaaS. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los objetos almacenados en Blob Storage son blobs en bloques.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Obtenga una lista de archivos del contenedor mediante [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). El código siguiente recupera la lista de blobs, luego los recorre y se muestran los URI de los blobs encontrados. Puede copiar el URI desde la ventana de comandos y pegarlo en un explorador para ver el archivo.

Si tiene hasta 5000 blobs en el contenedor, todos los nombres de blob se recuperan en una llamada a ListBlobsSegmentedAsync. Si tiene más de 5000 blobs en el contenedor, el servicio recupera la lista en conjuntos de 5000 hasta que todos los nombres de blob se hayan recuperado. Por tanto, la primera vez que se llama a esta API, devuelve los primeros 5000 nombres de blob y un token de continuación. La segunda vez, se proporciona el token, y el servicio recupera el siguiente conjunto de nombres de blob, y así sucesivamente hasta que el token de continuación es null, lo que indica que se han recuperado todos los nombres de blob. 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>Descargar blobs

Descargue blobs al disco local mediante [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

El código siguiente descarga el blob cargado en una sección anterior, agregando el sufijo "_DOWNLOADED" al nombre de blob para que pueda ver ambos archivos en el disco local. 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita los blobs cargados en este tutorial de inicio rápido, puede eliminar todo el contenedor mediante [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Elimine también los archivos creados si ya no son necesarios.

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage mediante .NET. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](storage-dotnet-how-to-use-blobs.md)

Para más información sobre el Explorador de Storage y los blobs, consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
