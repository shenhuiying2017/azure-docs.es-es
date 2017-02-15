---
title: "Introducción al almacenamiento de blobs y los servicios conectados de Visual Studio (ASP.NET) | Microsoft Docs"
description: "Cómo empezar a usar el almacenamiento de blobs de Azure en un proyecto ASP.NET en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: aea87ff1646770a31361e6a7e57b920be274c287


---
# <a name="get-started-with-blob-storage-and-visual-studio-connected-services-aspnet"></a>Introducción al almacenamiento de blobs servicios conectados de Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
En este artículo se describe cómo empezar a usar Azure Blob Storage después de haber creado o hecho referencia a una cuenta de almacenamiento de Azure en una aplicación ASP.NET con el cuadro de diálogo **Agregar servicios conectados** de Visual Studio. El artículo muestra cómo crear contenedores de blob y realizar otras tareas comunes, como cargar, enumerar, descargar y eliminar blobs. Los ejemplos están escritos en C\# y usan la [biblioteca del cliente de Microsoft Azure Storage para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

* Para más información general sobre el uso de Almacenamiento de blobs de Azure, consulte [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md).
* Para más información sobre los proyectos de ASP.NET, vea [ASP.NET](http://www.asp.net).

El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados a los que puede obtenerse acceso desde cualquier lugar del mundo a través de HTTP o HTTPS. Un solo blob puede tener cualquier tamaño. Los blobs pueden tener forma de imágenes, archivos de audio y vídeo, archivos sin procesar y archivos de documentos.

Al igual que los archivos residen en carpetas, los blobs de almacenamiento residen en contenedores. Después de haber creado una cuenta de almacenamiento, puede crear uno o varios contenedores en el almacenamiento. Por ejemplo, en un almacenamiento llamado "Scrapbook" puede crear contenedores de blobs en el almacenamiento denominados "images" para almacenar imágenes y "audio" para almacenar archivos de audio. Una vez creados los contenedores, puede cargar archivos de blob individuales a ellos.

## <a name="access-blob-containers-in-code"></a>Contenedores de blobs de acceso en el código
Para obtener acceso mediante programación a los blobs en los proyectos ASP.NET, deberá agregar los elementos siguientes, si no están presentes aún.

1. Agregue las siguientes declaraciones de espacio de nombres de código en la parte superior de todo archivo C# en el que desee obtener acceso al Almacenamiento de Azure mediante programación.
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;
2. Obtenga un objeto **CloudStorageAccount** que represente la información de su cuenta de almacenamiento. Use el código siguiente para obtener la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure.
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
   
   > [!NOTE]
   > Use todo el código anterior delante del código que aparece en las secciones siguientes.
   > 
   > 
3. Obtenga un objeto **CloudBlobClient** para hacer referencia a un contenedor existente en la cuenta de almacenamiento.
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [!NOTE]
> Algunas de las API que realizan llamadas a Almacenamiento de Azure en ASP.NET 5 son asincrónicas. Consulte [Programación asincrónica con Async y Await](http://msdn.microsoft.com/library/hh191443.aspx) para obtener más información.
> 
> 

## <a name="create-a-blob-container-in-code"></a>Crear un contenedor de blobs en código
También puede usar el objeto **CloudBlobClient** para crear un contenedor en su cuenta de almacenamiento. Todo lo que necesita hacer es agregar una llamada a **CreateIfNotExistsAsync** en el código anterior, tal como se muestra en el ejemplo siguiente.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Almacenamiento de blobs de Azure admite blobs en bloques y en páginas.  En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y utilícela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier secuencia de datos en ella llamando al método **UploadFromStream** . De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
Para enumerar los blobs en un contenedor, use el método **ListBlobs** para recuperar los blobs o directorios que incluye. Para acceder a las numerosas propiedades y métodos de una lista **IListBlobItem** recuperada, debe convertir esta última en un objeto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**.  Si se desconoce el tipo, puede realizar una comprobación de tipo para determinar el formato al que se debe convertir.  El código siguiente demuestra cómo recuperar y consultar el URI de cada elemento del contenedor **photos** .

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

Como se muestra en el ejemplo anterior, en el servicio BLOB los contenedores también incluyen directorios. De este modo, es posible organizar los blobs en una estructura similar a la estructura de carpetas. Por ejemplo, observe el siguiente conjunto de blobs en bloques incluidos en un contenedor denominado **photos**.

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Al llamar a **ListBlobs** en el contenedor "fotos" (como se muestra en el ejemplo anterior), la colección que se obtenga contendrá los objetos **CloudBlobDirectory** y **CloudBlockBlob** que representan a los directorios y los blobs que existen en el nivel superior. El siguiente ejemplo muestra el resultado.

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


También existe la opción de establecer el parámetro **UseFlatBlobListing** del método **ListBlobs** en **true**. De este modo, todos los blobs aparecerían como **CloudBlockBlob**, independientemente del directorio.  En el ejemplo siguiente se muestra la llamada a **ListBlobs**.

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

Y el siguiente ejemplo muestra los resultados.

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## <a name="download-blobs"></a>Descargar blobs
Para descargar blobs, use el método **DownloadToStream** . En el siguiente ejemplo, se usa el método **DownloadToStream** para transferir el contenido del blob a un objeto de secuencia que, a continuación, puede guardar en un archivo local.

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

También puede usar el método **DownloadToStream** para descargar el contenido de un blob en forma de cadena de texto.

    // Retrieve a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"></a>Eliminar blobs
Para eliminar un blob, use el método **Delete** .

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## <a name="list-blobs-in-pages-asynchronously"></a>Enumerar blobs en páginas de forma asincrónica
Si enumera un gran número de blobs o desea controlar el número de resultados que devuelve en una operación de listado, puede enumerar blobs en páginas de resultados. En el siguiente ejemplo se muestra cómo devolver resultados en páginas asincrónicamente de forma que la ejecución no se bloquee mientras se espera a devolver un conjunto grande de resultados.

En este ejemplo se muestra un listado de blobs plano, pero también puede realizar un listado jerárquico si establece el parámetro **useFlatBlobListing** del método **ListBlobsSegmentedAsync** en **false**.

Dado que el método de ejemplo llama a un método asincrónico, debe ir precedido por la palabra clave **async** y debe devolver un objeto **Task**. La palabra clave await especificada para el método **ListBlobsSegmentedAsync** suspende la ejecución del método de ejemplo hasta que se completa la tarea de enumeración.

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

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]




<!--HONumber=Nov16_HO3-->


