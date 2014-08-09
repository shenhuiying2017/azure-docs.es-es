<properties linkid="develop-media-services-how-to-guides-deliver-media-assets" urlDisplayName="Delivering Media Assets" pageTitle="How to Deliver Media Assets - Azure" metaKeywords="" description="Learn about options for delivering media assets that have been uploaded to Media Services in Azure. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="migree" solutions="" manager="" editor="" />

Entrega de un recurso mediante descarga
=======================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de la [Administración de recursos](http://go.microsoft.com/fwlink/?LinkID=301815&clcid=0x409).

En este tema se analizan las opciones para entregar recursos multimedia cargados en los Servicios multimedia. Puede entregar contenido de los Servicios multimedia en diversos escenarios de aplicaciones. Puede descargar recursos multimedia o tener acceso a ellos mediante un localizador. Puede enviar contenido multimedia a otra aplicación o a otro proveedor de contenido. Para mejorar el rendimiento y la escalabilidad, también puede entregar contenido si utiliza una Red de entrega de contenido (CDN), como el servicio CDN de Azure.

En este ejemplo se muestra cómo descargar recursos multimedia desde los Servicios multimedia. El código consulta los trabajos asociados con la cuenta de Servicios multimedia por identificador de trabajo y tiene acceso a su colección **OutputMediaAssets** (que es el conjunto de uno o más recursos multimedia de salida que resultan de la ejecución de un trabajo). Este ejemplo muestra cómo descargar recursos multimedia de salida desde un trabajo, pero puede aplicar el mismo enfoque para descargar otros recursos.

``` {}
 
// Descargar el recurso de salida del trabajo especificado en una carpeta local.
static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
{
    // Este método ilustra cómo descargar un solo recurso. 
    // No obstante, puede iterar a través de la recopilación OutputAssets,
    // y descargar todos los recursos si hay muchos. 

    // Obtener una referencia al trabajo. 
    IJob job = GetJob(jobId);

    // Obtener una referencia al primer recurso de salida. Si hubiera varios 
    // recursos multimedia de salida, podría iterar y controlar cada uno.
    IAsset outputAsset = job.OutputMediaAssets[0];

    // Crear un localizador SAS para descargar el recurso.
    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("Directiva de descarga de archivo", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 20,
        ParallelTransferThreadCount = 20
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Usar el siguiente controlador de eventos para comprobar el progreso de la descarga.
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
```

Para obtener más información acerca de la entrega de recursos, consulte:

-   [Entrega de recursos con los Servicios multimedia para .NET](http://msdn.microsoft.com/es-es/library/jj129575.aspx)
-   [Entrega de recursos con la API de REST de Media Services](http://msdn.microsoft.com/es-es/library/jj129578.aspx)

Pasos siguientes
----------------

En este tema se explicó la descarga de un recurso desde el almacenamiento de Azure. Para obtener información sobre otras formas de entregar recursos, consulte el tema [Entrega de contenido de transmisiónn por secuencias](http://go.microsoft.com/fwlink/?LinkID=301942).

