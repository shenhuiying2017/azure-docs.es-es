---
title: "Introducción a Azure Blob Storage (almacenamiento de objetos) con .NET | Microsoft Docs"
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: tamram
ms.openlocfilehash: 87594d2688e3cd01f5e7db8f5be8ca513969e774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-using-net"></a>Introducción al Almacenamiento de blobs de Azure mediante .NET

[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

### <a name="about-this-tutorial"></a>Acerca de este tutorial
Este tutorial muestra cómo escribir código .NET para algunos escenarios comunes con el Almacenamiento de blobs de Azure. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar blobs.

**Requisitos previos:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [Biblioteca de cliente de Almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Administrador de configuración Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Una [cuenta de almacenamiento de Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Más ejemplos
Para ver ejemplos adicionales con el almacenamiento de blobs, consulte [Introducción a almacenamiento de blobs de Azure en .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/). Puede descargar la aplicación de ejemplo y ejecutarla, así como ver el código en GitHub.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Adición de directivas using
Agregue las siguientes directivas **using** al principio del archivo `Program.cs`:

```csharp
using Microsoft.WindowsAzure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Análisis de la cadena de conexión
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>Creación del cliente del servicio BLOB
La clase **CloudBlobClient** permite recuperar contenedores y blobs almacenados en Almacenamiento de blobs. Esta es una forma de crear el cliente de servicio:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
Ahora ya puede escribir código que lee y escribe datos en el Almacenamiento de blobs.

## <a name="create-a-container"></a>Crear un contenedor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

De manera predeterminada, el nuevo contenedor es privado, lo que significa que debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos del contenedor a disposición de todo el mundo, puede convertir el contenedor en público utilizando el código siguiente:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

Cualquier usuario de Internet puede ver blobs de en contenedores públicos. Sin embargo, solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso a la cuenta apropiada o una firma de acceso compartido.

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
El almacenamiento de blobs de Azure admite blobs en bloques y en páginas.  En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStream** . De este modo, se crea el blob si no existía anteriormente, o bien se sobrescribe si ya existía.

En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede utilizar el método **ListBlobs** del contenedor para recuperar los blobs y los directorios que contiene. Para acceder al gran número de propiedades y métodos de una lista **IListBlobItem** devuelta, debe convertirla en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir. En el código siguiente, se demuestra cómo recuperar el URI de cada elemento del contenedor _photos_ y proporcionarlo como salida:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Mediante la inclusión de la información de la ruta de acceso en los nombres de blobs se puede crear una estructura de directorios virtuales que puede organizar y recorrer, tal como lo haría en un sistema de archivos tradicional. La estructura de directorios es solo virtual (los únicos recursos disponibles en Blob Storage son contenedores y blobs). Sin embargo, la biblioteca de clientes de almacenamiento ofrece un objeto **CloudBlobDirectory** para hacer referencia a un directorio virtual y simplificar el proceso de trabajo con blobs organizados de este modo.

Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor denominado *photos*:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Al llamar a **ListBlobs** en el contenedor *photos* (como en el fragmento de código anterior), se devuelve una lista jerárquica. Contiene objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan, respectivamente, los directorios y los blobs del contenedor. El resultado tiene el siguiente aspecto:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. En este caso, los blobs del contenedor se devuelven como objetos **CloudBlockBlob** . La llamada a **ListBlobs** para devolver una lista plana tiene el siguiente aspecto:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

y el resultado tiene este aspecto:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Descargar blobs
Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **DownloadToStream** . En el siguiente ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

También puede usar el método **DownloadToStream** para descargar el contenido de un blob en forma de cadena de texto.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Eliminar blobs
Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **Delete** en ella.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Enumerar blobs en páginas de forma asincrónica
Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En este ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico si establece el parámetro _useFlatBlobListing_ del método **ListBlobsSegmentedAsync** en _false_.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave _async_ y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que la tarea de enumeración se completa.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>Escritura en un blob en anexos
Un blob en anexos se optimiza para las operaciones de anexado, como el registro. Como un blob en bloques, un blob en anexos se compone también de bloques, pero en el caso del blob en anexos cuando se agrega un nuevo bloque, siempre se anexa al final del blob. No se puede actualizar o eliminar un bloque existente en un blob en anexos. Los identificadores de bloque para un blob en anexos no está expuestos como lo están en el caso de los blobs en bloques.

Cada bloque en un blob en anexos puede tener un tamaño diferente, hasta un máximo de 4 MB y el blob puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob en anexos es, por tanto, ligeramente superior a 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob en anexos y le anexa algunos datos, para simular una operación de registro simple.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

Consulte [Descripción Blobs en bloques, en anexos y en páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) para obtener más información acerca de las diferencias entre los tres tipos de blobs.

## <a name="managing-security-for-blobs"></a>Administración de la seguridad para blobs
De forma predeterminada, Almacenamiento de Azure protege sus datos al limitar el acceso al propietario de la cuenta, quien está en posesión de las claves de acceso a ella. Cuando necesite compartir datos Blob en su cuenta de almacenamiento, es importante hacerlo sin poner en peligro la seguridad de las claves de acceso de la cuenta. Además, puede cifrar los datos Blob para cerciorarse de que es seguro pasar por la red y el Almacenamiento de Azure.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>Control del acceso a datos Blob
De forma predeterminada, los datos Blob de su cuenta de almacenamiento solo son accesibles para el propietario de la cuenta de almacenamiento. Para autenticar las solicitudes en el Almacenamiento de blobs, se necesita la clave de acceso de la cuenta de forma predeterminada. Sin embargo, puede que quiera que determinados datos Blob estén disponibles para otros usuarios. Tiene dos opciones:

* **Acceso anónimo:** puede hacer que un contenedor o sus blobs estén públicamente disponibles para el acceso anónimo. Vea [Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md) para más información.
* **Firmas de acceso compartido:** puede proporcionar a los clientes una firma de acceso compartido (SAS), que ofrece acceso delegado a un recurso de la cuenta de almacenamiento, con los permisos que especifique y durante el intervalo que indique. Consulte [Uso de firmas de acceso compartido (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para más información.

### <a name="encrypting-blob-data"></a>Cifrado de datos Blob
Almacenamiento de Azure admite el cifrado de datos Blob en el cliente y en el servidor:

* **Cifrado en el cliente:** la Biblioteca del cliente de almacenamiento para .NET admite el cifrado de datos dentro de las aplicaciones cliente antes de cargarlos en Almacenamiento de Azure y el descifrado de los datos mientras se descargan en el cliente. La biblioteca también admite la integración con el Almacén de claves de Azure para la administración de las claves de la cuenta de almacenamiento. Consulte [Cifrado del lado de cliente y Almacén de claves de Azure para el Almacenamiento de Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para más información. Consulte también [Tutorial: Cifrado y descifrado de blobs en Almacenamiento de Microsoft Azure con Almacén de claves de Azure](storage-encrypt-decrypt-blobs-key-vault.md).
* **Cifrado en el servidor**: Almacenamiento de Azure ahora admite el cifrado en el servidor. Consulte [Cifrado del servicio Almacenamiento de Azure para datos en reposo (versión preliminar)](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del Almacenamiento de blobs, siga estos vínculos para obtener más información.

### <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure
* El [Explorador de Microsoft Azure Storage (MASE)](../../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.

### <a name="blob-storage-samples"></a>Ejemplos de Almacenamiento de blobs
* [Introducción a almacenamiento de blobs de Azure en .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Referencia de Almacenamiento de blobs
* [Referencia de la biblioteca de clientes de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [Referencia de API de REST](/rest/api/storageservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Guías conceptuales
* [Introducción a la utilidad de línea de comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Introducción al Almacenamiento de archivos para .NET](../files/storage-dotnet-how-to-use-files.md)
* [Uso del almacenamiento de blobs de Azure con el SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki)
