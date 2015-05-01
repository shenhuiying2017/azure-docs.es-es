<properties 
	pageTitle="Codificación de un recurso mediante el Codificador multimedia de Azure" 
	description="Aprenda a usar el codificador multimedia de Azure para codificar contenido multimedia en Servicios multimedia. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Codificación de un recurso mediante el Codificador multimedia de Azure

Este artículo forma parte de la serie [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](media-services-video-on-demand-workflow.md). 

##Información general

Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Los archivos de vídeo digital son bastante grandes y pueden ser demasiado pesados para entregarlos a través de Internet o para que los dispositivos de sus clientes los muestren correctamente. La codificación es el proceso de compresión de vídeo y audio para que los clientes puedan ver el contenido multimedia.

Los trabajos de codificación son una de las operaciones de procesamiento más habituales en los Servicios multimedia. Los trabajos de codificación se crean para convertir archivos multimedia de una codificación a otra. Al codificar, puede usar el Codificador multimedia integrado de Servicios multimedia. También puede usar un codificador proporcionado por un socio de Servicios multimedia; los codificadores de terceros están disponibles a través de Azure Marketplace. Puede especificar los detalles de las tareas de codificación mediante cadenas preestablecidas definidas para el codificador o mediante archivos de configuración preestablecidos. Para ver los tipos de valores preestablecidos disponibles, vea Valores preestablecidos de tarea para los Servicios multimedia de Azure. Si usó un codificador de terceros, debe [validar los archivos](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Se recomienda codificar siempre los archivos intermedios en un conjunto de archivos MP4 de velocidad de bits adaptable y, a continuación, convertirlo al formato deseado con el [empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx).

Si el recurso de salida tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, vea [Configuración de la directiva de entrega de recursos](media-services-dotnet-configure-asset-delivery-policy.md).

##Creación de un trabajo con una sola tarea de codificación 

Al codificar con el Codificador multimedia de Azure, puede usar los valores preestablecidos de configuración de tarea especificados [aquí](https://msdn.microsoft.com/library/azure/dn619389.aspx).

###Uso del SDK de Servicios multimedia para .NET  

El método siguiente **EncodeToAdaptiveBitrateMP4Set** crea un trabajo de codificación y agrega una sola tarea de codificación al trabajo. La tarea usa el "Codificador multimedia de Azure" para codificar a "H264 Adaptive Bitrate MP4 Set 720p". 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
            case JobState.Error:

                // Cast sender as a job.
                IJob job = (IJob)sender;

                // Display or log error details as needed.
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

###Uso de extensiones del SDK de Servicios multimedia para .NET

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

##Creación de un trabajo con tareas encadenadas 

En muchos escenarios de aplicaciones, los desarrolladores desean crear una serie de tareas de procesamiento. En Servicios multimedia, puede crear una serie de tareas encadenadas. Cada tarea realiza distintos pasos de procesamiento diferentes y puede usar diferentes procesadores multimedia. Las tareas encadenadas pueden entregar un recurso de una tarea a otra, realizando una secuencia lineal de tareas en el recurso. Sin embargo, no es necesario que las tareas realizadas en un trabajo estén en una secuencia. Al crear una tarea encadenada, los objetos **ITask** encadenados se crean en un solo objeto **IJob**.

>[AZURE.NOTE] Actualmente hay un límite de 30 tareas por trabajo. Si necesita encadenar más de 30 tareas, cree más de un trabajo para incluir las tareas.

El método **CreateChainedTaskEncodingJob** siguiente crea un trabajo que contiene dos tareas encadenadas. En consecuencia, el método devuelve un trabajo que contiene dos recursos de salida.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


##Pasos siguientes

[Azure Marketplace]: https://datamarket.azure.com/
[Valores preestablecidos del codificador]: http://msdn.microsoft.com/library/dn619392.aspx
[Obtención de una instancia de procesador multimedia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Instrucciones acerca de cómo: de un recurso cifrado]:http://go.microsoft.com/fwlink/?LinkId=301733
[Instrucciones acerca de cómo: de un recurso mediante descarga]:http://go.microsoft.com/fwlink/?LinkId=301734
[Comprobación del progreso del trabajo]:http://go.microsoft.com/fwlink/?LinkId=301737
[Valores predefinidos de tarea para Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx


<!--HONumber=52-->