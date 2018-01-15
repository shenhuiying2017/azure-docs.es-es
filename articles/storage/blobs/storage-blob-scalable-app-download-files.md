---
title: Descargar grandes cantidades de datos aleatorios desde Azure Storage | Microsoft Docs
description: "Obtenga información acerca de cómo usar el SDK de Azure para descargar grandes cantidades de datos aleatorios de una cuenta de Azure Storage."
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 3842860acb1c0fdd9e07f6d2f678ac5d5304003b
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Descargar grandes cantidades de datos aleatorios desde Azure Storage

Este tutorial es la tercera parte de una serie. Este tutorial muestra cómo descargar grandes cantidades de datos de Azure Storage.

En la tercera parte de la serie, se aprende a:

> [!div class="checklist"]
> * Actualizar la aplicación
> * Ejecución de la aplicación
> * Validar el número de conexiones

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe completar el tutorial anterior de Storage: [Upload large amounts of random data in parallel to Azure storage][previous-tutorial] (Cargar en paralelo grandes cantidades de datos aleatorios en Azure Storage).

## <a name="remote-into-your-virtual-machine"></a>Sesión remota en la máquina virtual

 Use el siguiente comando en el equipo local para crear una sesión de escritorio remoto en la máquina virtual. Reemplace la dirección IP con el valor de publicIPAddress de la máquina virtual. Cuando se le solicite, escriba las credenciales usadas al crear la máquina virtual.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Actualizar la aplicación

En el tutorial anterior, solo cargó los archivos en la cuenta de almacenamiento. Abra `D:\git\storage-dotnet-perf-scale-app\Program.cs` en un editor de texto. Reemplace el método `Main` por la siguiente muestra. En este ejemplo, se convierte en comentario la tarea de carga y se quita la marca de comentario de la tarea de descarga y de la tarea dedicada a eliminar el contenido de la cuenta de almacenamiento cuando esta se completa.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Una vez actualizada la aplicación, debe compilarla de nuevo. Abra un `Command Prompt` y vaya a `D:\git\storage-dotnet-perf-scale-app`. Vuelva a compilar la aplicación mediante la ejecución de `dotnet build` tal como se muestra en el ejemplo siguiente:

```
dotnet build
```

## <a name="run-the-application"></a>Ejecución de la aplicación

Ahora que se ha vuelto a compilar la aplicación, es hora de ejecutarla con el código actualizado. Si aún no la ha abierto, abra un `Command Prompt` y vaya a `D:\git\storage-dotnet-perf-scale-app`.

Escriba `dotnet run` para ejecutar la aplicación.

```
dotnet run
```

La aplicación lee los contenedores que se encuentran en la cuenta de almacenamiento especificada en **storageconnectionstring**. Se iteran 10 blobs cada vez mediante el método [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) de los contenedores y los descarga en el equipo local mediante el método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_).
En la siguiente tabla se muestran la clases [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) que se definen para cada blob a medida que se descarga.

|Propiedad|Valor|DESCRIPCIÓN|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Esta propiedad deshabilita la función de comprobación del hash MD5 del contenido cargado. Al deshabilitar la validación de MD5, se agiliza la transferencia. Aún así, hay que tener en cuenta que no se puede confirmar la validez o integridad de los archivos que se transfieren. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Esta propiedad determina si se calcula y almacena un hash MD5.   |

La tarea `DownloadFilesAsync` se muestra en el ejemplo siguiente:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>Validar las conexiones

Mientras se descargan los archivos, puede comprobar el número de conexiones simultáneas de su cuenta de almacenamiento. Abra un `Command Prompt` y escriba `netstat -a | find /c "blob:https"`. Este comando muestra el número de conexiones simultáneas que se abrieron mediante `netstat`. En el ejemplo siguiente se muestra un resultado similar a lo que verá cuando ejecute el tutorial usted mismo. Como puede ver en el ejemplo, se abrieron más de 280 conexiones al descargar los archivos aleatorios de la cuenta de almacenamiento.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>pasos siguientes

En la tercera parte de la serie aprendió no solo cómo descargar grandes cantidades de datos aleatorios de una cuenta de almacenamiento, si no que también aprendió lo siguiente:

> [!div class="checklist"]
> * Ejecución de la aplicación
> * Validar el número de conexiones

Vaya a la parte cuatro de la serie para comprobar las métricas de rendimiento y latencia en el portal.

> [!div class="nextstepaction"]
> [Comprobar las métricas de rendimiento y latencia en el portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md