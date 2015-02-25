<properties pageTitle="Cifrado de activos en Servicios multimedia de Azure" description="Aprenda a usar la protección de Microsoft PlayReady para cifrar un activo en Servicios multimedia. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





<h1><a name="playready"></a>  Protección de un recurso con PlayReady</h1>

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [ Comprobación del progreso del trabajo](../media-services-check-job-progress/).

En Servicios multimedia de Azure puede enviar un trabajo que integre la protección Microsoft PlayReady para cifrar un recurso. El código de esta sección utiliza varios archivos de transmisión de una carpeta de entrada, crea una tarea y los cifra con la protección PlayReady. 

En el siguiente ejemplo se muestra cómo crear un trabajo sencillo para proporcionar la protección PlayReady.

   1. Recupere los datos de configuración. Puede obtener un archivo de configuración de ejemplo del tema [Valores preestablecidos de tarea para Azure Media Encryptor](http://msdn.microsoft.com/es-es/library/hh973610.aspx).
   2. Cargue un archivo de entrada MP4.
   3. Convierta el archivo MP4 en un recurso de Smooth Streaming.
   4. Cifre el recurso con PlayReady.
   5. Envíe el trabajo.

En el siguiente ejemplo de código se muestra cómo implementar los pasos:

<pre><code>
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Create a storage-encrypted asset and upload the MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declare a new job to contain the tasks.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Set up the first task. 

    // Read the task configuration data into a string. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Get a media processor instance
    IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

    // Create a task with the conversion details, using the configuration data 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Specify the input asset to be converted.
    task.InputAssets.Add(asset);

    // Add an output asset to contain the results of the job. We do not need 
    // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
    // parameter to false. 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Set up the encryption task. 

    // Read the configuration data into a string. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Get a media processor instance
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

    // Create a second task, specifying a task name, the media processor, and configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Add the input asset, which is the smooth streaming output asset from the first task. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Add an output asset to contain the results of the job. Persist the output by setting 
    // the shouldPersistOutputOnCompletion param to true.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
        AssetCreationOptions.None);

    // Use the following event handler to check job progress. 
    job.StateChanged += new
            EventHandler&lt;JobStateChangedEventArgs&gt;(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
    // in your output folder.
    LogJobDetails(job.Id);

    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }
    // Perform other tasks. For example, access the assets that are the output of a job, 
    // either by creating URLs to the asset on the server, or by downloading. 

    return job;
}

</code></pre>
Para obtener más información acerca de la protección PlayReady, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/es-es/library/dn189154.aspx">Proteger Smooth Stream y MPEG DASH con PlayReady</a></li>
<li><a href="http://www.microsoft.com/PlayReady/">Microsoft PlayReady</a></li>
</ul>

<h2>Pasos siguientes</h2>
Ahora que sabe cómo proteger recursos con Servicios multimedia, diríjase al tema [Administración de recursos](../media-services-manage-assets/).

<!--HONumber=42-->
