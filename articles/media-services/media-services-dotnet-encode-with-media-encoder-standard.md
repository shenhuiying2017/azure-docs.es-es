<properties 
	pageTitle="Codificación de un recurso mediante Estándar de codificador multimedia" 
	description="En este tema se muestra cómo usar .NET para codificar sus recursos con Codificador multimedia estándar." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="08/30/2016"
	ms.author="juliako;anilmur"/>


# Codificación de un recurso mediante Estándar de codificador multimedia

Los trabajos de codificación son una de las operaciones de procesamiento más habituales en los Servicios multimedia. Los trabajos de codificación se crean para convertir archivos multimedia de una codificación a otra. Al codificar, puede usar el Codificador multimedia integrado de Servicios multimedia. También puede usar un codificador proporcionado por un socio de Servicios multimedia; los codificadores de terceros están disponibles a través de Azure Marketplace.

En este tema se muestra cómo usar .NET para codificar sus recursos con el Codificador multimedia estándar (MES). Codificador multimedia estándar se configura mediante uno de los valores preestablecidos descritos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Se recomienda codificar siempre los archivos intermedios en un conjunto de archivos MP4 de velocidad de bits adaptable y, a continuación, convertirlo al formato deseado con el [empaquetado dinámico](media-services-dynamic-packaging-overview.md). Para aprovechar al máximo el empaquetado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido. Para obtener más información, consulte [Escalación de Servicios multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

Si el recurso de salida tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, consulte [Configuración de la directiva de entrega de recursos](media-services-dotnet-configure-asset-delivery-policy.md).

>[AZURE.NOTE]MES genera un archivo de salida con un nombre que contiene los 32 primeros caracteres del nombre del archivo de entrada. El nombre se basa en lo que se especifica en el archivo de valores preestablecidos. Por ejemplo, "FileName": "{Basename}\_{Index}{Extension}". {Basename} se reemplaza por los 32 primeros caracteres del nombre del archivo de entrada.

###Formatos de MES

[Códecs y formatos](media-services-media-encoder-standard-formats.md)

###Valores preestablecidos de MES

El Codificador multimedia estándar se configura mediante uno de los valores preestablecidos descritos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadatos de entrada y salida

Al codificar un recurso de entrada (o recursos) con MES, obtendrá un recurso de salida en la correcta realización de dicha tarea de codificación. El recurso de salida contiene vídeo, audio, miniaturas, manifiestos, etc., basados en el valor predeterminado de codificación que utilice.

El recurso de salida también contiene un archivo con metadatos sobre el recurso de entrada. El nombre del archivo XML de metadatos tiene el formato siguiente: <asset\_id>\_metadata.xml (por ejemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4\_metadata.xml), donde <asset\_id> es el valor AssetId del recurso de entrada. El esquema de este archivo XML de metadatos de entrada se describe [aquí](http://msdn.microsoft.com/library/azure/dn783120.aspx).

El recurso de salida también contiene un archivo con metadatos sobre el recurso de salida. El nombre del archivo XML de metadatos tiene el formato siguiente: <source_file_name>\_manifest.xml (por ejemplo, BigBuckBunny\_manifest.xml). El esquema de este archivo XML de metadatos de salida se describe [aquí](http://msdn.microsoft.com/library/azure/dn783217.aspx).

Si desea examinar cualquiera de los dos archivos de metadatos, puede crear un localizador de SAS y descargar el archivo en el equipo local. Puede encontrar un ejemplo sobre cómo crear un localizador SAS y descargar un archivo con extensiones del SDK de .NET de Servicios multimedia.

##Descarga de un ejemplo

Obtenga y ejecute un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

##Ejemplo

En el ejemplo de código siguiente se usa el último SDK para .NET de Servicios multimedia para realizar las siguientes tareas:

- Crear un trabajo de codificación.
- Obtener una referencia al codificador Codificador multimedia estándar.
- Especifique que se use el valor preestablecido "H264 Multiple Bitrate 720p". Puede ver todos los valores preestablecidos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409). También puede examinar el esquema con el que se deben cumplir estos valores preestablecidos [aquí](https://msdn.microsoft.com/library/mt269962.aspx).
- Agregar una única tarea de codificación al trabajo.
- Especificar el recurso de entrada que se va a codificar.
- Crear un recurso de salida que contendrá el recurso codificado.
- Agregar un controlador de eventos para comprobar el progreso del trabajo.
- Envíe el trabajo.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Create a task with the encoding details, using a string preset.
		    // In this case "H264 Multiple Bitrate 720p" preset is used.
		    ITask task = job.Tasks.AddNew("My encoding task",
		        processor,
		        "H264 Multiple Bitrate 720p",
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


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Otras referencias 

[Cómo generar la miniatura mediante Media Encoder Estándar con .NET](media-services-dotnet-generate-thumbnail-with-mes.md) [Información general de la codificación de Servicios multimedia](media-services-encode-asset.md)

<!---HONumber=AcomDC_0831_2016-->