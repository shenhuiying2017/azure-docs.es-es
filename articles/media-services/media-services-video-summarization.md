<properties
	pageTitle="Usar Miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo | Microsoft Azure"
	description="El resumen de vídeo puede ayudarle a crear resúmenes de vídeos largos al seleccionar automáticamente fragmentos interesantes del vídeo original. Esto es útil si quiere proporcionar una rápida descripción de lo que se va a encontrar en un vídeo largo."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/26/2016"   
	ms.author="milanga;juliako;"/>

#Usar Miniaturas de vídeo multimedia de Azure para crear un resumen de vídeo
##Información general

El procesador de multimedia (PM) **Miniaturas de vídeo multimedia de Azure** le permite crear un resumen de un vídeo útil para aquellos clientes que solo quieren ver un resumen de un vídeo largo. Por ejemplo, es posible que los clientes quieran ver un breve "vídeo de resumen" al mantener el mouse sobre una miniatura. Al ajustar los parámetros de **Miniaturas de vídeo multimedia de Azure** mediante un valor predeterminado de configuración, puede usar la eficaz tecnología de detección de tomas y concatenación del PM para generar de forma algorítmica un subclip descriptivo.

El PM **Miniatura de vídeo multimedia de Azure** está actualmente en versión preliminar.

En este tema se proporcionan detalles acerca de **Miniatura de vídeo multimedia de Azure** y se muestra cómo se usa con el SDK de Media Services para .NET

##Ejemplo de resumen de vídeo 

Estos son algunos ejemplos de lo que puede hacer el procesador de multimedia Miniaturas de vídeo multimedia de Azure:

###Vídeo original

[Vídeo original](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

###Resultado de miniaturas de vídeo

[Resultado de miniaturas de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Configuración de tareas (valor preestablecido)

Al crear una tarea de miniatura de vídeo con **Miniaturas de vídeo multimedia de Azure**, debe especificar un valor predeterminado de configuración. El ejemplo de miniatura anterior se creó con la siguiente configuración básica de JSON:

	{"version":"1.0"}

En este momento puede modificar los siguientes parámetros:

Parámetro|Description
---|---
outputAudio|Especifica si el vídeo resultante contiene audio o no. <br/>Los valores permitidos son: True o False. El valor predeterminado es True.
fadeInFadeOut|Especifica si se usan transiciones de fundido entre las distintas miniaturas de movimiento. <br/>Los valores permitidos son: True o False. El valor predeterminado es True.
maxMotionThumbnailDurationInSecs|Entero que especifica la duración de todo el vídeo resultante. El valor predeterminado depende de la duración del vídeo original.


En la tabla siguiente se describe la duración predeterminada cuando no se usa **maxMotionThumbnailInSecs**.

||||
---|---|---|---|---
Duración del vídeo|d < 3 min|3 min < d < 15 min|15 min < d < 30 min| 30 min < d
Duración de la miniatura|15 s (2-3 escenas)| 30 s (3-5 escenas)|60 s (5-10 escenas)|90 s (10-15 escenas)


El siguiente JSON establece los parámetros disponibles.
	
	{
	    "version": "1.0",
	    "options": {
	        "outputAudio": "true",
	        "maxMotionThumbnailDurationInSecs": "10",
	        "fadeInFadeOut": "true"
	    }
	}

## Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un recurso y cargar un archivo multimedia en él.
1. Crea un trabajo con una tarea de miniatura de vídeo basada en un archivo de configuración que contiene el siguiente valor predeterminado de JSON.
		
		{				
			"version": "1.0",
		    "options": {
		        "outputAudio": "true",
	    	    "maxMotionThumbnailDurationInSecs": "30",
	    	    "fadeInFadeOut": "false"
		    }
		}

1. Descarga los archivos de salida.

###Código .NET
	 
    using System;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Threading;
	using System.Threading.Tasks;
	
	namespace VideoSummarization
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	            // Run the thumbnail job.
	            var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
	                                        @"C:\supportFiles\VideoThumbnail\config.json");
	
	            // Download the job output asset.
	            DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
	        }
	
	        static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
	        {
	            // Create an asset and upload the input media file to storage.
	            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	                "My Video Thumbnail Input Asset",
	                AssetCreationOptions.None);
	
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My Video Thumbnail Job");
	
	            // Get a reference to Azure Media Video Thumbnails.
	            string MediaProcessorName = "Azure Media Video Thumbnails";
	
	            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	            // Read configuration from the specified file.
	            string configuration = File.ReadAllText(configurationFile);
	
	            // Create a task with the encoding details, using a string preset.
	            ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
	                processor,
	                configuration,
	                TaskOptions.None);
	
	            // Specify the input asset.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);
	
	            // Use the following event handler to check job progress.  
	            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Launch the job.
	            job.Submit();
	
	            // Check job execution and wait for job to finish.
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	
	            progressJobTask.Wait();
	
	            // If job state is Error, the event handling
	            // method for job progress should log errors.  Here we check
	            // for error state and exit if needed.
	            if (job.State == JobState.Error)
	            {
	                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
	                Console.WriteLine(string.Format("Error: {0}. {1}",
	                                                error.Code,
	                                                error.Message));
	                return null;
	            }
	
	            return job.OutputMediaAssets[0];
	        }
	
	        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	        {
	            IAsset asset = _context.Assets.Create(assetName, options);
	
	            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	            assetFile.Upload(filePath);
	
	            return asset;
	        }
	
	        static void DownloadAsset(IAsset asset, string outputDirectory)
	        {
	            foreach (IAssetFile file in asset.AssetFiles)
	            {
	                file.Download(Path.Combine(outputDirectory, file.Name));
	            }
	        }
	
	        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors
	                .Where(p => p.Name == mediaProcessorName)
	                .ToList()
	                .OrderBy(p => new Version(p.Version))
	                .LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor",
	                                                           mediaProcessorName));
	
	            return processor;
	        }
	
	        static private void StateChanged(object sender, JobStateChangedEventArgs e)
	        {
	            Console.WriteLine("Job state changed event:");
	            Console.WriteLine("  Previous state: " + e.PreviousState);
	            Console.WriteLine("  Current state: " + e.CurrentState);
	
	            switch (e.CurrentState)
	            {
	                case JobState.Finished:
	                    Console.WriteLine();
	                    Console.WriteLine("Job is finished.");
	                    Console.WriteLine();
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
	                    // LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	    }
    }

###Resultado de miniaturas de vídeo

[Resultado de miniaturas de vídeo](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Vínculos relacionados

[Azure Media Services Analytics Overview (Información general sobre análisis de Servicios multimedia de Azure)](media-services-analytics-overview.md)

[Demostraciones de Análisis multimedia de Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

<!---HONumber=AcomDC_0928_2016-->