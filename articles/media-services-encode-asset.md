<properties pageTitle="Codificación de un activo para Servicios Multimedia en Azure" description="Aprenda a usar el codificador multimedia de Azure para codificar contenido multimedia en Servicios multimedia. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>


#   Codificación de un recurso
Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [ Obtención de un procesador multimedia](../media-services-get-media-processor/).

Puede codificar el contenido multimedia del servidor con una serie de codificaciones y formatos multimedia que usa Azure Media Encoder. También puede usar un codificador proporcionado por un socio de Servicios multimedia. Podrá encontrar codificadores de terceros en [Azure Marketplace][]. Puede especificar los detalles de las tareas de codificación mediante las cadenas de [valores preestablecidos de codificador][] o los archivos de configuración. 

## Codificación en un conjunto de tasa de bits adaptativa MP4
Es recomendable codificar el archivo intermedio en conjuntos de tasa de bit adaptativa MP4 y, a continuación, usar empaquetado dinámico para entregar el contenido. Para obtener más información, consulte [Creación de un trabajo de codificación con el SDK de Servicios multimedia para .NET](http://msdn.microsoft.com/es-es/library/azure/dn282273.aspx), [empaquetado dinámico](http://msdn.microsoft.com/es-es/library/azure/jj889436.aspx) y [entrega de contenido](http://msdn.microsoft.com/es-es/library/azure/hh973618.aspx).

## Codificación en MP4
El método siguiente carga un solo recurso y crea un trabajo para codificarlo en MP4 usando el valor preestablecido "H264 Broadband 720p", que creará un solo MP4 usando la codificación H264 con una resolución de 720p:
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
		// Create a task with the encoding details, using a string preset.
    	ITask task = job.Tasks.AddNew("My encoding task",
        	processor,
	        "H264 Broadband 720p",
        	_protectedConfig);
    
		// Specify the input asset to be encoded.
    	task.InputAssets.Add(asset);
    
		// Add an output asset to contain the results of the job. 
    	// This output is specified as AssetCreationOptions.None, which 
    	// means the output asset is in the clear (unencrypted). 
    	task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
		// Use the following event handler to check job progress.  
    	job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    
		// Launch the job.
    	job.Submit();
    
		// Optionally log job details. This displays basic job details
    	// to the console and saves them to a JobDetails-JobId.txt file 
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

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
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
            	LogJobStop(job.Id);
            	break;
        	default:
	            break;
    	}
	}
</code></pre>
<h2>Codificación en Smooth Streaming</h2>
Si desea codificar un vídeo en Smooth Streaming, tiene dos opciones:
<ul>
<li> Codificar directamente en Smooth Streaming </li>
<li> Codificar en MP4 y, a continuación, convertir a Smooth Streaming</li>
</ul>

Para codificar directamente en Smooth Streaming, use el código mostrado arriba, pero utilice uno de los valores preestablecidos del codificador de Smooth Streaming. Para ver una lista completa de valores preestablecidos del codificador, consulte [Cadenas predefinidas de tarea para el Codificador multimedia de Azure](http://msdn.microsoft.com/es-es/library/jj129582.aspx). 

Para convertir MP4 a Smooth Streaming, use Azure Media Packager. Azure Media Packager no admite valores preestablecidos de cadenas, por lo que tiene que especificar las opciones de configuración en XML. Podrá encontrar el XML necesario para convertir MP4 a Smooth Streaming en [Valores preestablecidos de tarea para Azure Media Packager][]. Copie y pegue el XML en un archivo llamado MediaPackager_MP4ToSmooth.xml en su proyecto. El código siguiente ilustra cómo convertir un recurso de MP4 a Smooth Streaming. El método que aparece a continuación toma un recurso existente y lo convierte. 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

Para obtener más información acerca de los recursos que se están procesando, consulte:
<ul>
<li><a href="http://msdn.microsoft.com/es-es/library/jj129580.aspx">Procesamiento de recursos con el SDK de Media Services para .NET</a></li>
<li><a href="http://msdn.microsoft.com/es-es/library/jj129574.aspx">Procesamiento de recursos con la API de REST de Media Services</a></li>
</ul>

## Pasos siguientes
Ahora que sabe cómo crear un trabajo para codificar un recurso, vaya al tema [Comprobación del progreso del trabajo con Servicios multimedia](../media-services-check-job-progress/).

[Azure Marketplace]: https://datamarket.azure.com/
[valores preestablecidos de codificador]: http://msdn.microsoft.com/es-es/library/dn619392.aspx
[ Obtención de una instancia de procesador multimedia]:http://go.microsoft.com/fwlink/?LinkId=301732
[ Carga de un recurso cifrado]:http://go.microsoft.com/fwlink/?LinkId=301733
[ Entrega de un recurso mediante descarga]:http://go.microsoft.com/fwlink/?LinkId=301734
[Comprobación del progreso del trabajo]:http://go.microsoft.com/fwlink/?LinkId=301737
[Valores preestablecidos de tarea para Azure Media Packager]:http://msdn.microsoft.com/es-es/library/windowsazure/hh973635.aspx

<!--HONumber=42-->
