<properties 
	pageTitle="Codificación de un recurso mediante Codificador multimedia estándar"
	description="En este tema se muestra cómo usar .NET para codificar sus recursos con Codificador multimedia estándar."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="juliako"/>


#Codificación de un recurso mediante Codificador multimedia estándar

Los trabajos de codificación son una de las operaciones de procesamiento más habituales en los Servicios multimedia. Los trabajos de codificación se crean para convertir archivos multimedia de una codificación a otra. Al codificar, puede usar el Codificador multimedia integrado de Servicios multimedia. También puede usar un codificador proporcionado por un socio de Servicios multimedia; los codificadores de terceros están disponibles a través de Azure Marketplace.

En este tema se muestra cómo usar .NET para codificar sus recursos con Codificador multimedia estándar. Codificador multimedia estándar se configura mediante uno de los valores preestablecidos descritos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

>[AZURE.NOTE]La versión actual del procesador multimedia requiere que se pase una cadena completa XML o JSON como el valor preestablecido de codificación. En breve se dispondrá de soporte técnico para pasar una cadena con nombre como "H264 Multiple Bitrate 720p" a través de una actualización de servicio.

Se recomienda codificar siempre los archivos intermedios en un conjunto de archivos MP4 de velocidad de bits adaptable y, a continuación, convertirlo al formato deseado con el [empaquetado dinámico](media-services-dynamic-packaging-overview.md). Para aprovechar al máximo el empaquetado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido. Para obtener más información, consulte [Escalación de Servicios multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

Si el recurso de salida tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, consulte [Configuración de la directiva de entrega de recursos](media-services-dotnet-configure-asset-delivery-policy.md).

##Ejemplo

En el ejemplo de código siguiente se usa el último SDK para .NET de Servicios multimedia para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtener una referencia al codificador Codificador multimedia estándar.
- Cargar el código XML preestablecido desde uno de los valores preestablecidos mostrados [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).
- Agregar una única tarea de codificación al trabajo. 
- Especificar el recurso de entrada que se va a codificar.
- Crear un recurso de salida que contendrá el recurso codificado.
- Agregar un controlador de eventos para comprobar el progreso del trabajo.
- Envíe el trabajo.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset, string pathToLocalPresetFile)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		    // Load the XML (or JSON) from the local file
		    string configuration = File.ReadAllText(pathToLocalPresetFile);
		
		    // Create a task
		    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		        processor,
		        configuration,
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
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

##Otras referencias 

[Información general sobre la codificación de Servicios multimedia](media-services-encode-asset.md)

<!---HONumber=September15_HO1-->