<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Delivering Media Assets" pageTitle="How to Deliver Media Assets - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Entrega de un recurso mediante descarga

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Administración de recursos][].

En este tema se analizan las opciones para entregar recursos multimedia cargados en los Servicios multimedia. Puede entregar contenido de los Servicios multimedia en diversos escenarios de aplicaciones. Puede descargar recursos multimedia o tener acceso a ellos mediante un localizador. Puede enviar contenido multimedia a otra aplicación o a otro proveedor de contenido. Para mejorar el rendimiento y la escalabilidad, también puede entregar contenido si utiliza una Red de entrega de contenido (CDN), como el servicio CDN de Azure.

En este ejemplo se muestra cómo descargar recursos multimedia desde los Servicios multimedia. El código consulta los trabajos asociados con la cuenta de Servicios multimedia por identificador de trabajo y tiene acceso a su colección **OutputMediaAssets** (que es el conjunto de uno o más recursos multimedia de salida que resultan de la ejecución de un trabajo). Este ejemplo muestra cómo descargar recursos multimedia de salida desde un trabajo, pero puede aplicar el mismo enfoque para descargar otros recursos.

     
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }

Para obtener más información acerca de la entrega de recursos, consulte:

-   [Entrega de recursos con los Servicios multimedia para .NET][]
-   [Entrega de recursos con la API de REST de Media Services][]

</p>
## Pasos siguientes

En este tema se explicó la descarga de un recurso desde el almacenamiento de Azure. Para obtener información sobre otras formas de entregar recursos, consulte el tema [Entrega de contenido de transmisiónn por secuencias][].

  [Administración de recursos]: http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409
  [Entrega de recursos con los Servicios multimedia para .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Entrega de recursos con la API de REST de Media Services]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Entrega de contenido de transmisiónn por secuencias]: http://go.microsoft.com/fwlink/?LinkID=301942
