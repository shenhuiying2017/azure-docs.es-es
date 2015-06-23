<properties 
	pageTitle="Codificación avanzada con el flujo de trabajo del Codificador multimedia Premium" 
	description="Aprenda a codificar con el flujo de trabajo del Codificador multimedia Premium. Los ejemplos de código están escritos en C# y utilizan el SDK de Servicios multimedia para .NET." 
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
	ms.date="03/03/2015" 
	ms.author="juliako"/>

#Codificación avanzada con el flujo de trabajo del Codificador multimedia Premium (vista previa pública)

**Nota** El procesador multimedia de flujo de trabajo del Codificador multimedia Premium que se trata en este tema no está disponible en China.

##Información general

Servicios multimedia de Microsoft Azure presenta la vista previa del procesador multimedia de **Flujo de trabajo del Codificador multimedia Premium**. Este procesador ofrece funciones de codificación avanzadas para sus flujos de trabajo de Premium a petición. 

Los siguientes temas describen los detalles relacionados con el **flujo de trabajo del Codificador multimedia Premium**: 

- [Formatos que admite el flujo de trabajo del Codificador multimedia Premium](media-services-premium-workflow-encoder-formats.md) - Trata los formatos de archivo y códecs admitidos por el **flujo de trabajo del Codificador multimedia Premium**.

- En la sección [Comparación de códecs](media-services-encode-asset.md#compare_encoders) se comparan las funciones de codificación del **flujo de trabajo del Codificador multimedia Premium** y del **Codificador multimedia de Azure**.

En este tema se muestra cómo codificar con el **flujo de trabajo del Codificador multimedia Premium** usando .NET.

##Codificación

Las tareas de codificación para el **flujo de trabajo del Codificador multimedia Premium** requieren un archivo de configuración independiente, denominado archivo de flujo de trabajo. Estos archivos tienen una extensión .workflow y se crean mediante la herramienta [Diseñador de flujo de trabajo](media-services-workflow-designer.md).

También puede obtener los archivos de flujo de trabajo predeterminados [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Los archivos de flujo de trabajo deben cargarse en su cuenta de Servicios multimedia como un recurso, y el recurso se debe transferir a la tarea de codificación.

En el ejemplo siguiente se muestra cómo codificar con el **flujo de trabajo del Codificador multimedia Premium**. 

Hay que seguir estos pasos: 
 
1. Crear un recurso y cargar un archivo de flujo de trabajo. 
2. Crear un recurso y cargar un archivo multimedia de origen.
3. Obtenga el procesador multimedia "Flujo de trabajo de Codificador multimedia Premium".
4. Crear un trabajo y una tarea.
5. Agregar dos recursos de entrada a la tarea.
	
	a. 1.º: el recurso de flujo de trabajo.

	b. 2.º: el recurso de vídeo.
	
	**Nota**: El recurso de flujo de trabajo debe agregarse a la tarea antes del recurso multimedia. 
La cadena de configuración para esta tarea debe estar vacía. 

6. Envíe el trabajo de codificación.

El siguiente ejemplo es un ejemplo completo. Para obtener información sobre cómo configurar con el desarrollo de .NET de Servicios multimedia, consulte [Desarrollo de Servicios multimedia con .NET](media-services-dotnet-how-to-use.md).


 	using System; 
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace MediaEncoderPremiumWorkflowSample
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
	
	        private static readonly string _supportFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _workflowFilePath =
	            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
	        
	        private static readonly string _singleMP4InputFilePath =
	            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
	            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
	            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                                AccessPermissions.Write | AccessPermissions.List);
	
	            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            locator.Delete();
	            accessPolicy.Delete();
	
	            return asset;
	        }
	
	        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
	            // Obtener una referencia de procesador de multimedia y pasarla al nombre del 
	            // procesador que va a usar para la tarea específica.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
	
	            // Crear una tarea con los detalles de codificación, usando un valor predefinido de cadena.
	            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
	                processor,
	                "",
	                TaskOptions.None);
	
	            // Especificar el recurso de entrada que se va a codificar.
	            task.InputAssets.Add(workflow);
	            task.InputAssets.Add(video); // agregamos un recurso
	            // Agregar un recurso de salida que va a contener los resultados del trabajo. 
	            // Esta salida se especifica como AssetCreationOptions.None, which 
	            // significa que el recurso de salida no está cifrado. 
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Usar el siguiente controlador de eventos para comprobar el progreso del trabajo.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Iniciar el trabajo.
	            job.Submit();
	
	            // Comprobar la ejecución del trabajo y esperar a que finalice. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // Si el estado del trabajo es Error, el método de administración de 
	            // eventos para el progreso del trabajo debe registrar los errores.  En este caso comprobamos 
	            // el estado del error y la salida, si es necesario.
	            if (job.State == JobState.Error)
	            {
	                throw new Exception("\nExiting method due to job error.");
	            }
	
	            return job.OutputMediaAssets[0];
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
	                    LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static private void LogJobStop(string jobId)
	        {
	            StringBuilder builder = new StringBuilder();
	            IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
	
	            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	            builder.AppendLine("***************************");
	            builder.AppendLine("Job ID: " + job.Id);
	            builder.AppendLine("Job Name: " + job.Name);
	            builder.AppendLine("Job State: " + job.State.ToString());
	            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	            builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
	            // Log job errors if they exist.  
	            if (job.State == JobState.Error)
	            {
	                builder.Append("Error Details: \n");
	                foreach (ITask task in job.Tasks)
	                {
	                    foreach (ErrorDetail detail in task.ErrorDetails)
	                    {
	                        builder.AppendLine("  Task Id: " + task.Id);
	                        builder.AppendLine("    Error Code: " + detail.Code);
	                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
	                    }
	                }
	            }
	            builder.AppendLine("***************************\n");
	
	            Console.Write(builder.ToString());
	        }
	
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}


##Problemas conocidos

Si el vídeo de entrada no contiene subtítulos, el recurso de salida seguirá conteniendo un archivo TTML vacío.


<!--HONumber=52--> 