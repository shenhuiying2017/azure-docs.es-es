<properties
	pageTitle="Detección de movimientos con Análisis multimedia de Azure"
	description="El procesador de multimedia (MP) Detector de movimiento multimedia de Azure permite identificar de manera eficaz las secciones de interés dentro de un vídeo que, de lo contrario, sería extenso y monótono."
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
	ms.date="04/08/2016"   
	ms.author="milanga;juliako;"/>

# Detección de movimientos con Análisis multimedia de Azure

##Información general

El procesador de multimedia (MP) **Detector de movimiento multimedia de Azure** permite identificar de manera eficaz las secciones de interés dentro de un vídeo que, de lo contrario, sería extenso y monótono. La detección de movimiento se puede usar en grabaciones con cámara estática para identificar las secciones del vídeo donde se produjo el movimiento. Genera un archivo JSON que contiene metadatos con marcas de tiempo y la región delimitadora donde se produjo el evento.

Orientada a las fuentes de vídeo de seguridad, esta tecnología puede clasificar el movimiento en eventos relevantes y falsos positivos, como sombras y cambios en la iluminación. Esto le permite generar alertas de seguridad a partir de las fuentes de las cámaras sin recibir correos no deseados con una cantidad interminable de eventos irrelevantes, a la vez que puede extraer momentos de interés a partir vídeos de vigilancia considerablemente extensos.

El MP **Detector de movimiento multimedia de Azure** está actualmente en versión preliminar.

Este tema proporciona detalles sobre el **Detector de movimiento multimedia de Azure** y muestra cómo se usa con el SDK de Servicios multimedia para .NET

##Archivos de entrada del Detector de movimiento

Archivos de vídeo. Actualmente, se admiten los siguientes formatos: MP4, MOV y WMV.

##Archivos de salida del Detector de movimiento

Un trabajo de detección de movimiento devolverá un archivo JSON en el recurso de salida, el que describe las alertas de movimiento y sus categorías dentro del vídeo. El archivo contendrá información sobre el tiempo y la duración del movimiento detectado en el vídeo.

Actualmente, la detección de movimiento solo admite la categoría de movimiento genérico, a la que se hace referencia como ***tipo 2*** en la salida.

Los tamaños y las coordenadas X e Y se mostrarán con un valor float normalizado entre 0,0 y 1,0. Multiplique este valor por la resolución de alto y ancho del vídeo para obtener el cuadro de límite de la región del movimiento detectado.

Cada salida se divide en fragmentos y se subdivide en intervalos para definir los datos dentro del vídeo. Las duraciones de los fragmentos no necesariamente son iguales y pueden abarcar duraciones extensas donde no se detecta movimiento alguno.

La API del Detector de movimiento brinda indicadores una vez que hay objetos en movimiento en un vídeo de fondo fijo (por ejemplo, un vídeo de vigilancia). El Detector de movimiento está entrenado para disminuir las alarmas falsas, como cambios de iluminación y sombras. Las limitaciones actuales de los algoritmos incluyen vídeos con visión nocturna, objetos semitransparentes y objetos pequeños.

###<a id="output_elements"></a>Elementos del archivo JSON de salida

En la tabla siguiente, se describen elementos del archivo JSON de salida.

Elemento|Descripción
---|---
Versión|Esto se refiere a la versión de la API de vídeo.
Escala de tiempo|"Tics" por segundo del vídeo.
Offset|La diferencia de tiempo para las marcas de tiempo en "tics". En la versión 1.0 de las API de vídeo, será siempre 0. En los escenarios futuros que se admitan, este valor puede cambiar.
Framerate|Fotogramas por segundo del vídeo.
Ancho, alto|Se refiere al ancho y alto del vídeo en píxeles.
Iniciar|La marca de tiempo de inicio en "tics".
Duración|La longitud del evento, en "tics".
Intervalo|El intervalo de cada entrada del evento, en "tics".
Eventos|Cada fragmento de evento contiene el movimiento detectado dentro de esa duración.
Tipo|En la versión actual, este valor siempre es "2" para el movimiento genérico. Esta etiqueta brinda a las API de vídeo la flexibilidad para clasificar el movimiento en las versiones futuras.
RegionID|Tal como se explicó anteriormente, este valor siempre será 0 en esta versión. Esta etiqueta brinda a la API de vídeo la flexibilidad para encontrar el movimiento en diversas regiones en las versiones futuras.
Regiones|Se refiere al área del vídeo donde le interesa el movimiento. En la versión actual de las API de vídeo, no puede especificar una región; en lugar de eso, el área del movimiento que se detectará será la superficie completa del vídeo.<br/>- El id. representa el área de la región. En esta versión solo existe uno, el id. 0. <br/>-El rectángulo representa la forma de la región donde le interesa el movimiento. En esta versión, siempre es un triángulo. <br/>- Las dimensiones de la región son X, Y, ancho y alto. Las coordenadas X e Y representan las coordenadas XY del lado superior izquierdo de la región en una escala normalizada de 0,0 a 1,0. El ancho y el alto representan el tamaño de la región en una escala normalizada de 0,0 a 1,0. En la versión actual, los valores de X, Y, ancho y alto siempre están fijos en 0,0 y 1,1.<br/>- Fragmentos: los metadatos se dividen en distintos segmentos, llamados fragmentos. Cada fragmento contiene un inicio, una duración, un número de intervalo y eventos. Un fragmento sin eventos significa que no se detectó movimiento durante esa hora de inicio y la duración.
Corchetes|Cada corchete representa un intervalo del evento. Si ese intervalo contiene corchetes vacíos, significa que no se detectó movimiento.
 

##Configuración de tareas (valor preestablecido)

Cuando cree una tarea con **Detector de movimiento multimedia de Azure**, debe especificar una configuración preestablecida. Actualmente, no puede definir ninguna opción en la configuración preestablecida del Detector de movimiento multimedia de Azure. Debe proporcionar la configuración preestablecida mínima siguiente.

	{"version":"1.0"}

##Vídeos de muestra y salidas de Detector de movimiento

###Ejemplo con movimiento real

[Ejemplo con movimiento real](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fd54876c6-89a5-41de-b1f4-f45b6e10a94f%2FGarage.ism%2Fmanifest)

###Salida de JSON

	 {
	 "version": "1.0",
	 "timescale": 60000,
	 "offset": 0,
	 "framerate": 30,
	 "width": 1920,
	 "height": 1080,
	 "regions": [
	   {
	     "id": 0,
	     "type": "rectangle",
	     "x": 0,
	     "y": 0,
	     "width": 1,
	     "height": 1
	   }
	 ],
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 68510
	   },
	   {
	     "start": 68510,
	     "duration": 969999,
	     "interval": 969999,
	     "events": [
	       [
	         {
	           "type": 2,
	           "regionId": 0
	         }
	       ]
	     ]
	   },
	   {
	     "start": 1038509,
	     "duration": 41489
	   }
	 ]
	}

###Ejemplo con falsos positivos

[Ejemplo con falsos positivos (cambios de iluminación):](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Ffdc6656b-1c10-4f3f-aa7c-07ba073c1615%2FLivingRoomLight.ism%2Fmanifest&tech=flash)

###Salida de JSON

	{
	    "version": "1.0",
	    "timescale": 30000,
	    "offset": 0,
	    "framerate": 29.97,
	    "width": 1920,
	    "height": 1080,
	    "regions": [
	    {
	        "id": 0,
	        "type": "rectangle",
	        "x": 0,
	        "y": 0,
	        "width": 1,
	        "height": 1
	    }
	    ],
	    "fragments": [
	    {
	        "start": 0,
	        "duration": 320320
	    }
	    ]
	}


##Limitaciones

- Los formatos de vídeo de entrada admitidos incluyen MP4, MOV y WMV.
- La detección de movimiento está optimizada para los vídeos de fondo fijos. El algoritmo se centra en disminuir las alarmas falsas, como cambios en la iluminación y sombras.
- Es posible que no se detecten ciertos movimientos debido a desafíos técnicos; por ejemplo, vídeos con visión nocturna, objetos semitransparentes y objetos pequeños.


## Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un recurso y cargar un archivo multimedia en dicho recurso.
1. Crear un trabajo con una tarea de detección de movimiento de vídeo basada en un archivo de configuración que contiene el siguiente valor predeterminado JSON. 
					
		{
		    "version": "1.0"
		}

1. Descargar los archivos JSON de salida.
		 
        using System;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace VideoMotionDetection
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
		
		            // Run the VideoMotionDetection job.
		            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\VideoMotionDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
		        }
		
		        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Video Motion Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Video Motion Detection Job");
		
		            // Get a reference to Azure Media Motion Detector.
		            string MediaProcessorName = "Azure Media Motion Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);
		
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


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Vínculos relacionados

[Azure Media Services Analytics Overview (Información general sobre análisis de Servicios multimedia de Azure)](media-services-analytics-overview.md)

<!---HONumber=AcomDC_0413_2016-->