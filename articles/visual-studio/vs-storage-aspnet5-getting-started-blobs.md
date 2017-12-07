---
title: "Introducción a Blob Storage y a los servicios conectados de Visual Studio (ASP.NET Core) | Microsoft Docs"
description: "Cómo empezar a usar Azure Blob Storage en un proyecto de ASP.NET Core de Visual Studio después de haber creado una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: afd73bd0fd041a53fbe31aa3a5c23b3e27d7a9ec
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Introducción a Azure Blob Storage y los servicios conectados de Visual Studio (ASP.NET Core) (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

En este artículo se describe cómo empezar a usar Azure Blob Storage en Visual Studio después de haber creado o hecho referencia a una cuenta de Azure Storage en un proyecto de ASP.NET Core mediante la característica **Servicios conectados** de Visual Studio. La operación **Servicios conectados** instala los paquetes de NuGet adecuados para acceder a Azure Storage en el proyecto y agrega la cadena de conexión de la cuenta de almacenamiento a los archivos de configuración del proyecto. (Vea [Documentación sobre Storage](https://azure.microsoft.com/documentation/services/storage/) para información general sobre Azure Storage).

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos. En este artículo se describe cómo empezar a usar Blob Storage después de crear una cuenta de Azure Storage mediante **Servicios conectados** de Visual Studio en un proyecto de ASP.NET Core.

Al igual que los archivos residen en carpetas, los blobs de almacenamiento residen en contenedores. Después de haber creado un blob, puede crear en él uno o varios contenedores. Por ejemplo, en un blob llamado "Scrapbook", puede crear un contenedor llamado "images" para almacenar imágenes y otro llamado "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos individuales a ellos. Consulte [Introducción al Almacenamiento de blobs de Azure mediante .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para más información sobre la manipulación de blobs mediante programación.

Algunas de las API de Azure Storage son asincrónicas, y el código de este artículo asume que se usan métodos asincrónicos. Vea [Programación asincrónica](https://docs.microsoft.com/dotnet/csharp/async) para más información.

## <a name="access-blob-containers-in-code"></a>Contenedores de blobs de acceso en el código

Para acceder mediante programación a los blobs en los proyectos de ASP.NET Core, debe agregar el código siguiente, si no está presente aún:

1. Agregue las instrucciones `using` necesarias:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Obtenga un objeto `CloudStorageAccount` que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Use un objeto `CloudBlobClient` para hacer una referencia `CloudBlobContainer` a un contenedor existente en la cuenta de almacenamiento:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Crear un contenedor en código

También puede usar `CloudBlobClient` para crear un contenedor en su cuenta de almacenamiento con una llamada a `CreateIfNotExistsAsync`:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Para poner los archivos del contenedor a disposición de todo el mundo, defina el contenedor como público:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor

Para cargar un archivo de blob en un contenedor, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob. Después, cargue cualquier flujo de datos en dicha referencia con una llamada al método `UploadFromStreamAsync`. Esta operación crea el blob, en caso de que no exista, y sobrescribe un blob existente. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Para enumerar los blobs en un contenedor, primero obtenga una referencia de contenedor y llame a su método `ListBlobsSegmentedAsync` para recuperar los blobs o directorios que incluye. Para acceder al conjunto enriquecido de propiedades y métodos de un valor `IListBlobItem`, conviértalo en un objeto `CloudBlockBlob`, `CloudPageBlob` o `CloudBlobDirectory`. Si no conoce el tipo de blob, use un comprobador de tipos para determinar a cuál se debe convertir.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
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
} while (token != null);
```

Vea [Introducción a Azure Blob Storage mediante .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) para conocer otras formas de mostrar el contenido de un contenedor de blobs.

## <a name="download-a-blob"></a>Descarga de un blob

Para descargar un blob, primero obtenga una referencia al blob y luego llame al método `DownloadToStreamAsync`. En el siguiente ejemplo se usa el método `DownloadToStreamAsync` para transferir el contenido del blob a un objeto de flujo que luego puede guardar como archivo local.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Vea [Introducción a Azure Blob Storage mediante .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) para conocer otras formas de guardar blobs como archivos.

## <a name="delete-a-blob"></a>Eliminar un blob

Para eliminar un blob, primero obtenga una referencia al blob y luego llame al método `DeleteAsync`:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
