<properties urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Creaci&oacute;n de un activo cifrado y carga en el almacenamiento de Azure" metaKeywords="" description="Aprenda a obtener contenido multimedia en Servicios multimedia creando y cargando un activo cifrado." metaCanonical="" services="media-services" documentationCenter="" title="Creaci&oacute;n de un activo cifrado y carga en el almacenamiento" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="create-asset"> </a><span class="short header">Direccionamiento del de un recurso codificado y carga en el almacenamiento</span>

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de la [Configuración de su equipo para servicios multimedia][Configuración de su equipo para servicios multimedia].

Para llevar el contenido multimedia a Servicios multimedia, primero debe crear un activo, agregar archivos y, a continuación, cargar el activo. Este proceso se llama ingesta de contenido.

Cuando cree activos, podrá especificar tres opciones diferentes para el cifrado.

-   **AssetCreationOptions.None**: Sin cifrado. Si desea crear un recurso sin cifrar, debe configurar esta opción.
-   **AssetCreationOptions.CommonEncryptionProtected**: Para archivos protegidos con cifrado común (CENC). Un ejemplo es un conjunto de archivos que ya está cifrado para PlayReady.
-   **AssetCreationOptions.StorageEncrypted**: Cifrado de almacenamiento. Cifra un archivo de entrada limpio antes de que se cargue en el almacenamiento de Azure.

> WACOM.NOTE
> Los Servicios multimedia proporcionan cifrado de almacenamiento en disco para sus activos, no por cable como el administrador de derechos digitales (DRM).

El código de ejemplo de abajo hace lo siguiente:

-   Crea un activo vacío.
-   Crea una instancia de AssetFile que necesitamos asociar con el recurso.
-   Crea una instancia de AccessPolicy que define los permisos y la duración del acceso al recurso.
-   Crea una instancia de Locator que proporciona acceso al recurso.
-   Carga un solo archivo multimedia en los Servicios multimedia.

<!-- -->

    static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
    {
        var asset = _context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);
        Console.WriteLine("Time created: " + asset.Created.Date.ToString());

        return asset;
    }

    static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);
        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        return asset;
    }

El código siguiente muestra cómo crear un activo y cargar varios archivos.

    static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
    {
        var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                            AccessPermissions.Write | AccessPermissions.List);
        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        var blobTransferClient = new BlobTransferClient();
        blobTransferClient.NumberOfConcurrentTransfers = 20;
        blobTransferClient.ParallelTransferThreadCount = 20;

        blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

        var filePaths = Directory.EnumerateFiles(folderPath);

        Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

        if (!filePaths.Any())
        {
            throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
        }

        var uploadTasks = new List<Task>();
        foreach (var filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            Console.WriteLine("Created assetFile {0}", assetFile.Name);
                    
            // It is recommended to validate AccestFiles before upload. 
            Console.WriteLine("Start uploading of {0}", assetFile.Name);
            uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
        }

        Task.WaitAll(uploadTasks.ToArray());
        Console.WriteLine("Done uploading the files");

        blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }

## Pasos siguientes

Ahora que ha cargado un activo en los Servicios multimedia, vaya al tema [Obtención de un procesador de multimedia][Obtención de un procesador de multimedia].

  [Configuración de su equipo para servicios multimedia]: http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409
  [Obtención de un procesador de multimedia]: ../media-services-get-media-processor/
