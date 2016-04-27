<properties
	pageTitle="Detección de caras y emociones con Análisis multimedia de Azure"
	description="Este tema muestra cómo detectar caras y emociones con Análisis multimedia de Azure."
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

#Detección de caras y emociones con Análisis multimedia de Azure

##Información general

El procesador de multimedia (MP) **Azure Media Face Detector** le permite contar, realizar un seguimiento de los movimientos e incluso evaluar la participación y reacción de la audiencia a través de expresiones faciales. Este servicio contiene dos características:

- **Detección de caras**

	La detección de caras busca y realiza el seguimiento de caras humanas dentro de un vídeo. Se pueden detectar varias caras y posteriormente realizar un seguimiento de las mismas a medida que se mueven, con los metadatos de tiempo y ubicación devueltos en un archivo JSON. Durante el seguimiento, intentará dar un identificador constante a la misma cara mientras la persona se mueve alrededor de la pantalla, aunque se tape o salga brevemente del fotograma.

	>[AZURE.NOTE]Este servicio no lleva a cabo el reconocimiento facial. Si una persona abandona el fotograma o se tapa durante demasiado tiempo se le asigna un nuevo identificador cuando regresa.

- **Detección de emociones**
	
	Detección de emociones es un componente opcional del procesador de multimedia de detección de caras que devuelve análisis sobre varios atributos emocionales a partir de las caras detectadas, como felicidad, tristeza, temor, enojo, etc. Estos datos se pueden devolver por cara o como valor de grupo y se pueden agregar a una ventana e intervalo personalizables.

El procesador de multimedia **Azure Media Face Detector** está actualmente en versión preliminar.

En este tema se proporcionan detalles sobre el **Azure Media Face Detector** y muestra cómo se usa con el SDK de Servicios multimedia para .NET

##Archivos de entrada de Face Detector

Archivos de vídeo. Actualmente, se admiten los siguientes formatos: MP4, MOV y WMV.

##Archivos de salida de Face Detector

La API de detección y seguimiento de caras proporciona detección y seguimiento de caras de alta precisión que puede detectar hasta 64 caras humanas en un vídeo. Las caras de frente ofrecen los mejores resultados, mientras que las caras de lado y las caras pequeñas (inferiores o iguales a 24x24 píxeles) podrían no ser tan precisas.

Las caras detectadas y seguidas se devuelven con coordenadas (izquierda, superior, ancho y alto) que indican la ubicación de caras en la imagen en píxeles, así como un número de identificación de cara que indica el seguimiento de esa persona. Los números de identificación de cara son propensos a restablecerse en circunstancias en las que la cara de frente se pierde o se superpone en el fotograma, lo que provoca que a algunas personas se les asigne varios identificadores.

###<a id="output_elements"></a>Elementos del archivo JSON de salida

Para la operación de detección y seguimiento de caras, el resultado de salida contiene los metadatos de las caras dentro del archivo especificado en formato JSON.

El archivo JSON de detección y seguimiento de caras incluye los siguientes atributos:

Elemento|Descripción
---|---
Versión|Esto se refiere a la versión de la API de vídeo.
Escala de tiempo|"Tics" por segundo del vídeo.
Offset|Se trata de la diferencia de tiempo para las marcas de tiempo. En la versión 1.0 de las API de vídeo, será siempre 0. En los escenarios futuros que se admitan, este valor puede cambiar.
Framerate|Fotogramas por segundo del vídeo.
Fragments|Los metadatos se separan en diferentes segmentos denominados fragmentos. Cada fragmento contiene un inicio, una duración, un número de intervalo y eventos.
Iniciar|La hora de inicio del primer evento, en 'tics'.
Duración|La longitud del fragmento, en "tics".
Intervalo|El intervalo de cada entrada de evento dentro del fragmento, en "tics".
Eventos|Cada evento contiene las caras detectadas y seguidas dentro de esa duración de tiempo. Es una matriz de matriz de eventos. La matriz externa representa un intervalo de tiempo. La matriz interna consta de 0 o más eventos que ocurrieron en ese momento en el tiempo. Un corchete vacío significa que no se detectaron caras.
ID| El identificador de la cara que se sigue. Este número puede cambiar inadvertidamente si una cara deja de detectarse. Una persona determinada debe tener el mismo identificador durante todo el vídeo, pero esto no se puede garantizar debido a las limitaciones en el algoritmo de detección (oclusión, etc.).
X e Y|Las coordenadas X e Y de la parte superior izquierda del cuadro de límite en una escala normalizada de 0,0 a 1,0. <br/>-Las coordenadas X e Y son relativas siempre a la posición horizontal, por lo que si tiene un vídeo en vertical (o boca a abajo, en el caso de iOS), tendrá que transponer las coordenadas en consecuencia.
Ancho, alto|El ancho y alto del cuadro de límite de la cara en una escala normalizada de 0,0 a 1,0.
facesDetected|Este atributo se encuentra al final de los resultados JSON y resume el número de caras que el algoritmo detectó durante el vídeo. Dado que los identificadores pueden restablecerse inadvertidamente si una cara deja de detectarse (por ejemplo, la cara sale de la pantalla o aparta la vista), este número puede no ser siempre el número real de caras en el vídeo.

Detector de caras usa técnicas de fragmentación (donde los metadatos se pueden dividir en fragmentos según el tiempo y puede descargar solo lo que necesita) y segmentación (donde los eventos se dividen en caso de que aumenten demasiado). Algunos cálculos sencillos pueden ayudarle a transformar los datos. Por ejemplo, si un evento se inició en 6300 (tics), con una escala de tiempo de 2997 (tics/segundo) y una velocidad de fotogramas de 29,97 (fotogramas/segundo), entonces:

- Inicio/Escala de tiempo = 2,1 segundos
- Segundos x (Velocidad de fotogramas/Escala de tiempo) = 63 fotogramas

A continuación se muestra un ejemplo sencillo de extracción de JSON en un formato por fotograma para detección y seguimiento de caras:
	
	var faceDetectionResultJsonString = operationResult.ProcessingResult;
	var faceDetecionTracking = 
	     JsonConvert.DeserializeObject<FaceDetectionResult>(faceDetectionResultJsonString, settings);


##Ejemplo de entrada y salida de detección de caras

###Vídeo de entrada

[Vídeo de entrada](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###Configuración de tareas (valor preestablecido)

Al crear una tarea con **Azure Media Face Detector**, debe especificar un valor predeterminado de configuración. El siguiente valor predeterminado de configuración es solo para detección de caras.

	{"version":"1.0"}

###Salida de JSON

El siguiente ejemplo de salida de JSON se truncó.

	{
	"version": 1,
	"timescale": 30000,
	"offset": 0,
	"framerate": 29.97,
	"width": 1280,
	"height": 720,
	"fragments": [
	    {
	    "start": 0,
	    "duration": 60060
	    },
	    {
	    "start": 60060,
	    "duration": 60060,
	    "interval": 1001,
	    "events": [
	        [
	        {
	            "id": 0,
	            "x": 0.519531,
	            "y": 0.180556,
	            "width": 0.0867188,
	            "height": 0.154167
	        }
	        ],
	        [
	        {
	            "id": 0,
	            "x": 0.517969,
	            "y": 0.181944,
	            "width": 0.0867188,
	            "height": 0.154167
	        }
	        ],
	        [
	        {
	            "id": 0,
	            "x": 0.517187,
	            "y": 0.183333,
	            "width": 0.0851562,
	            "height": 0.151389
	        }
	        ],

		. . . 

##Ejemplo de entrada y salida de detección de emociones


###Vídeo de entrada

[Vídeo de entrada](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

###Configuración de tareas (valor preestablecido)

Al crear una tarea con **Azure Media Face Detector**, debe especificar un valor predeterminado de configuración. El siguiente valor predeterminado de configuración especifica la creación de JSON basándose en la detección de emociones.
 	
	{
	  "version": "1.0",
	  "options": {
	    "aggregateEmotionWindowMs": "987",
	    "mode": "aggregateEmotion",
	    "aggregateEmotionIntervalMs": "342"
	  }
	}


Nombre del atributo|Descripción
---|---
Mode|Faces: Solo detección de caras <br/>AggregateEmotion: Devolver valores de emociones medios para todas las caras del fotograma.
AggregateEmotionWindowMs|Utilizar si se selecciona el modo AggregateEmotion. Especifica la longitud del vídeo que se utiliza para generar cada resultado agregado, en milisegundos.
AggregateEmotionIntervalMs|Utilizar si se selecciona el modo AggregateEmotion. Especifica con qué frecuencia se van a producir resultados agregados.

Valores predeterminados A continuación figuran valores recomendados para la configuración de ventana e intervalo agregados. La ventana debe ser mayor que el intervalo.

 |Valores predeterminados|Máximos|Mínimos
---|---|---|---
Longitud de la ventana|2|3|1
Intervalo|0,5|1|0,25

###Salida de JSON

Salida de JSON para la emoción agregada (truncada):
 
	
	{
	 "version": 1,
	 "timescale": 30000,
	 "offset": 0,
	 "framerate": 29.97,
	 "width": 1280,
	 "height": 720,
	 "fragments": [
	   {
	     "start": 0,
	     "duration": 60060,
	     "interval": 15015,
	     "events": [
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           },
	           "windowMeanScores": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           }
	         }
	       ],
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           },
	           "windowMeanScores": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           }
	         }
	       ],
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           },
	           "windowMeanScores": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           }
	         }
	       ],
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           },
	           "windowMeanScores": {
	             "neutral": 0,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           }
	         }
	       ]
	     ]
	   },
	   {
	     "start": 60060,
	     "duration": 60060,
	     "interval": 15015,
	     "events": [
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 1,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,
	             "contempt": 0
	           },
	           "windowMeanScores": {
	             "neutral": 0.688541,
	             "happiness": 0.0586323,
	             "surprise": 0.227184,
	             "sadness": 0.00945675,
	             "anger": 0.00592107,
	             "disgust": 0.00154993,
	             "fear": 0.00450447,
	             "contempt": 0.0042109
	           }
	         }
	       ],
	       [
	         {
	           "windowFaceDistribution": {
	             "neutral": 1,
	             "happiness": 0,
	             "surprise": 0,
	             "sadness": 0,
	             "anger": 0,
	             "disgust": 0,
	             "fear": 0,


##Limitaciones

- Los formatos de vídeo de entrada admitidos incluyen MP4, MOV y WMV.
- El intervalo de tamaños de cara detectable es 24x24 a 2048x2048 píxeles. Las caras que se encuentren fuera de este intervalo no se detectarán.
- Para cada vídeo, el número máximo de caras devuelto es 64.
- Algunas caras podrían no detectarse debido a los desafíos técnicos; por ejemplo, ángulos de cara muy grandes (poses) y oclusión grande. Las caras de frente y de frente cercanas tienen los mejores resultados.


## Código de ejemplo

El programa siguiente muestra cómo:

1. Crear un recurso y cargar un archivo multimedia en dicho recurso.
1. Crear un trabajo con una tarea de detección de caras basándose en un archivo de configuración que contiene el siguiente valor predeterminado de JSON. 
					
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
		
		namespace FaceDetection
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
		
		            // Run the FaceDetection job.
		            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
		                                        @"C:\supportFiles\FaceDetection\config.json");
		
		            // Download the job output asset.
		            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
		        }
		
		        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
		        {
		            // Create an asset and upload the input media file to storage.
		            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
		                "My Face Detection Input Asset",
		                AssetCreationOptions.None);
		
		            // Declare a new job.
		            IJob job = _context.Jobs.Create("My Face Detection Job");
		
		            // Get a reference to Azure Media Face Detector.
		            string MediaProcessorName = "Azure Media Face Detector";
		
		            var processor = GetLatestMediaProcessorByName(MediaProcessorName);
		
		            // Read configuration from the specified file.
		            string configuration = File.ReadAllText(configurationFile);
		
		            // Create a task with the encoding details, using a string preset.
		            ITask task = job.Tasks.AddNew("My Face Detection Task",
		                processor,
		                configuration,
		                TaskOptions.None);
		
		            // Specify the input asset.
		            task.InputAssets.Add(asset);
		
		            // Add an output asset to contain the results of the job.
		            task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);
		
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