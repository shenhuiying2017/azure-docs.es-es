<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# <a name="getting-started"></a>Introducción a los Servicios multimedia

En este tutorial se muestra cómo empezar a desarrollar con los Servicios multimedia de Azure. Presenta el flujo de trabajo básico de los Servicios multimedia y la mayoría de los objetos y tareas de programación más comunes necesarios para el desarrollo de los Servicios multimedia. Al término del tutorial, podrá reproducir un archivo multimedia de ejemplo que cargó, codificó y descargó. O bien, puede dirigirse al recurso codificado y reproducirlo en el servidor.

Un proyecto de C# Visual Studio que contiene el código de este tutorial está disponible aquí: [Descargar][].

Este tutorial le guiará a través de estos pasos básicos:

-   [Configuración de su proyecto][]
-   [Obtención del contexto de servidor de los Servicios multimedia][]
-   [Creación de un recurso y carga de los archivos asociados con el recurso en los Servicios multimedia][]
-   [Codificación de un recurso y descarga de un recurso de salida][]

## Requisitos previos

Se requieren los siguientes requisitos previos para el tutorial y el desarrollo basado en el SDK de los Servicios multimedia de Azure.

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Para obtener más información, consulte [Creación de una cuenta de Servicios multimedia][].
-   Sistemas operativos: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5 o .NET Framework 4.
-   Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).
-   Instale **SDK de Azure para .NET**, **SDK de los Servicios multimedia de Azure para .NET** y **WCF Data Services 5.0 para las bibliotecas de OData V3** y agregue referencias a su proyecto usando el paquete [windowsazure.mediaservices Nuget][]. La siguiente sección demuestra cómo instalar y agregar estas referencias.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <span id="Step1"></span></a>Configuración de su proyecto

1.  Cree una aplicación de consola de C# nueva en Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación** y el **nombre de la solución** y, a continuación, haga clic en OK.

2.  Agregue una referencia al ensamblado System.Configuration.

    Para agregar referencias usando el cuadro de diálogo **Administrar referencias**, siga estos pasos. Haga clic con el botón secundario en el nodo **Referencias** en el **Explorador de soluciones** y seleccione **Agregar referencia**. En el cuadro de diálogo **Administrar referencias**, seleccione los ensamblados adecuados (en este caso System.Configuration).

3.  Si todavía no lo ha hecho, agregue referencias al **SDK de Azure para .NET** (Microsoft.WindowsAzure.StorageClient.dll), **SDK de los Servicios multimedia de Azure para .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) y las bibliotecas de **WCF Data Services 5.0 para OData V3** (Microsoft.Data.OData.dll) usando el paquete [windowsazure.mediaservices Nuget][].

    Para agregar referencias usando Nuget, realice lo siguiente. En el menú principal de Visual Studio, seleccione TOOLS -\> Library Package Manager -\> Package Manager Console. En la ventana de la consola, escriba *Install-Package [nombre del paquete]* y presione Entrar (en este caso use el siguiente comando: *Install-Package windowsazure.mediaservices*).

4.  Agregue una sección *appSettings* al archivo **app.config** y establezca los valores para su nombre y clave de cuenta de Servicios multimedia de Azure. Obtuvo el nombre y la clave de la cuenta de Servicios multimedia durante el proceso de configuración de la cuenta. Agregue estos valores al atributo de valor para cada configuración en el archivo app.config en el proyecto de Visual Studio.

    > [WACOM.NOTE]
    > En Visual Studio 2012, el archivo App.config se agrega de manera predeterminada. En Visual Studio 2010, tiene que agregar manualmente el archivo de configuración de la aplicación.

        <configuration>
        . . . 
        <appSettings>
            <add key="accountName" value="Add-Media-Services-Account-Name" />
            <add key="accountKey" value="Add-Media-Services-Account-Key" />
        </appSettings>
        </configuration>

5.  Cree una carpeta nueva en su equipo local y asígnele el nombre supportFiles (en este ejemplo, supportFiles se encuentra en el directorio de proyecto MediaServicesGettingStarted). El [proyecto][Descargar] que acompaña a este tutorial contiene el directorio supportFiles. Puede copiar el contenido de este directorio en su carpeta supportFiles.

6.  Sobrescriba los valores existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.

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

7.  Agregue las siguientes variables de ruta de nivel de clase. La ruta \*\*\_supportFiles\*\* debe dirigirse a la carpeta que creó en un paso anterior.

        // Base support files path.  Update this field to point to the base path  
        // for the local support files folder that you create. 
        private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\supportFiles");

        // Paths to support files (within the above base path). You can use 
        // the provided sample media files from the "supportFiles" folder, or 
        // provide paths to your own media files below to run these samples.
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
        private static readonly string _outputFilesFolder =
            Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Agregue las siguientes variables de nivel de clase para recuperar la configuración de autenticación y conexión. Esta configuración se obtiene del archivo App.Config y es necesaria para conectarse a los Servicios multimedia, autenticarse y obtener un token para que pueda tener acceso al contexto del servidor. El código del proyecto hace referencia a estas variables para crear una instancia de contexto de servidor.

        private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
        private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Agregue la siguiente variable de nivel de clase que se usa como referencia estática para el contexto de servidor.

        // Field for service context.
        private static CloudMediaContext _context = null;

## <span id="Step2"></span></a>Obtención del contexto de los Servicios multimedia

El objeto de contexto de los Servicios multimedia contiene todos los objetos y recopilaciones fundamentales para tener acceso a la programación de los Servicios multimedia. El contexto incluye referencias a recopilaciones importantes, incluidos trabajos, recursos, archivos, directivas de acceso, localizadores y otros objetos. Debe obtener el contexto de servidor para la mayoría de las tareas de programación de los Servicios multimedia.

En el archivo Program.cs, agregue el siguiente código como primer elemento de su método **Main**. Este código usa los valores del nombre y la clave de cuenta de los Servicios multimedia del archivo app.config para crear una instancia del contexto de servidor. La instancia se asigna a la variable \*\*\_context\*\* que creó en el nivel de clase.

    // Get the service context.
    _context = new CloudMediaContext(_accountName, _accountKey);

## <span id="Step3"></span></a>Creación de un recurso y carga de un archivo

El código de esta sección realiza lo siguiente:

1.  Crea un recurso vacío
     Cuando se crean recursos, puede especificar tres opciones diferentes para codificarlos.

    -   **AssetCreationOptions.None**: Sin cifrado. Si desea crear un recurso sin cifrar, debe configurar esta opción.
    -   **AssetCreationOptions.CommonEncryptionProtected**: Para archivos protegidos con cifrado común (CENC). Un ejemplo es un conjunto de archivos que ya está cifrado para PlayReady.
    -   **AssetCreationOptions.StorageEncrypted**: Cifrado de almacenamiento. Cifra un archivo de entrada limpio antes de que se cargue en el almacenamiento de Azure.

        <div class="dev-callout"> 
<strong>Nota:</strong> 
<p>Los Servicios multimedia proporcionan cifrado de almacenamiento en el disco, no por cable como el administrador de derechos digitales (DRM).</p> 
</div>

2.  Crea una instancia de AssetFile que necesitamos asociar con el recurso.
3.  Crea una instancia de AccessPolicy que define los permisos y la duración del acceso al recurso.
4.  Crea una instancia de Locator que proporciona acceso al recurso.
5.  Carga un solo archivo multimedia en los Servicios multimedia. El proceso de crear y cargar se llama también introducción de recursos.

Agregue los siguientes métodos a la clase.

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

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                            AccessPermissions.Write | AccessPermissions.List);

        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

Agregue una llamada al método después de la línea \*\*\_context = new CloudMediaContext(\_accountName, \_accountKey);\*\* en su método Main.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

## <span id="Step4"></span></a>Codificación del recurso en el servidor y descarga de un recurso de salida

En los Servicios multimedia, puede crear trabajos que procesan contenidos multimedia de varias formas: codificación, cifrado, realización de conversiones de formato, etc. Un trabajo de Servicios multimedia contiene una o más tareas que especifican los detalles del trabajo de procesamiento. En esta sección puede crear una tarea de codificación básica y luego ejecutar un trabajo que la realiza usando el codificador multimedia de Azure. La tarea usa una cadena predefinida para especificar el tipo de codificación que se debe realizar. Para ver los valores de codificación predefinidos disponibles, consulte [Valores predefinidos del sistema Media Services Encoder][]. Los Servicios multimedia admiten los mismos formatos de archivo multimedia de entrada y salida que Microsoft Expression Encoder. Para obtener una lista de formatos admitidos, consulte [Códecs y tipos de archivo compatibles con Azure Media Encoder][].

1.  Agregue la siguiente definición del método **CreateEncodingJob** a su clase. Este método demuestra cómo llevar a cabo varias tareas necesarias para un trabajo de codificación:

    -   Declare un trabajo nuevo.
    -   Declare un procesador de multimedia para administrar el trabajo. Un procesador de multimedia es un componente que administra la codificación, el cifrado, la conversión de formatos y otros trabajos de procesamiento relacionados. Existen varios tipos de procesadores de multimedia disponibles (puede iterar a través de todos ellos usando \_context.MediaProcessors). El método GetLatestMediaProcessorByName, que se muestra posteriormente en este tutorial, devuelve el procesador del codificador multimedia de Azure.
    -   Declare una tarea nueva. Todo trabajo tiene una o más tareas. Tenga en cuenta que con la tarea, le asigna un nombre descriptivo, una instancia de procesador de multimedia, una cadena de configuración de tareas y opciones de creación de tareas. La cadena de configuración específica la configuración de codificación. Este ejemplo usa la configuración **H264 Broadband 720p**. Este valor predefinido produce un solo archivo MP4. Para obtener más información sobre este y otros valores predefinidos, consulte [Valores predefinidos del sistema Media Services Encoder][1].
    -   Agregue un recurso de entrada a la tarea. En este ejemplo, el recurso de entrada es uno que creo en la sección anterior.
    -   Agregue un recurso de salida a la tarea. Para un recurso de salida, especifique un nombre descriptivo, un valor booleano para indicar si va a guardar la salida en el servidor después de finalizar el trabajo y un valor **AssetCreationOptions.None** para indicar que la salida no está cifrada para almacenamiento y transporte.
    -   Envíe el trabajo.
         El envío de un trabajo es el último paso que se requiere para realizar un trabajo de codificación.

    El método demuestra también como realizar otras tareas útiles (pero opcionales), como hacer un seguimiento del progreso del trabajo y tener acceso al recurso que crea su trabajo de codificación.

        static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Broadband 720p",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Optionally log job details. This displays basic job details
            // to the console and saves them to a JobDetails-{JobId}.txt file 
            // in your output folder.
            LogJobDetails(job.Id);

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // **********
            // Optional code.  Code after this point is not required for 
            // an encoding job, but shows how to access the assets that 
            // are the output of a job, either by creating URLs to the 
            // asset on the server, or by downloading. 
            // **********

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return job;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];
            IAccessPolicy policy = null;
            ILocator locator = null;

            // Declare an access policy for permissions on the asset. 
            // You can call an async or sync create method. 
            policy =
                _context.AccessPolicies.Create("My 30 days readonly policy",
                    TimeSpan.FromDays(30),
                    AccessPermissions.Read);

            // Create a SAS locator to enable direct access to the asset 
            // in blob storage. You can call a sync or async create method.  
            // You can set the optional startTime param as 5 minutes 
            // earlier than Now to compensate for differences in time  
            // between the client and server clocks. 

            locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            // Build a list of SAS URLs to each file in the asset. 
            List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

            // Write the URL list to a local file. You can use the saved 
            // SAS URLs to browse directly to the files in the asset.
            if (sasUrlList != null)
            {
                string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
                StringBuilder fileList = new StringBuilder();
                foreach (string url in sasUrlList)
                {
                    fileList.AppendLine(url);
                    fileList.AppendLine();
                }
                WriteToFile(outFilePath, fileList.ToString());

                // Optionally download the output to the local machine.
                DownloadAssetToLocal(job.Id, outputFolder);
            }

            
            return job;
        }

2.  Agregue una llamada al método **CreateEncodingJob** en su método **Main**, después de las líneas que agregó anteriormente.

        CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);

3.  Agregue los siguientes métodos auxiliares a la clase. Se requieren para admitir el método **CreateEncodingJob**. A continuación se encuentra un resumen de los métodos auxiliares.
    -   El método **GetLatestMediaProcessorByName** devuelve un procesador de multimedia adecuado para administrar una codificación, un cifrado u otra tarea de procesamiento relacionada. Puede crear un procesador de multimedia usando el nombre de cadena adecuado del procesador que desea crear. Las posibles cadenas que se pueden transferir al método para el parámetro mediaProcessor son: **Azure Media Encoder**, **Azure Media Packager**, **Azure Media Encryptor**, **Storage Decryption**.

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                // The possible strings that can be passed into the 
                // method for the mediaProcessor parameter:
                //   Azure Media Encoder
                //   Azure Media Packager
                //   Azure Media Encryptor
                //   Storage Decryption

                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

    -   Al ejecutar trabajos, muchas veces se requiere una forma de hacer un seguimiento al progreso del trabajo. El siguiente ejemplo de código define el controlador de eventos StateChanged. Este controlador de eventos hace un seguimiento del progreso del trabajo y proporciona un estado actualizado, según el estado. El código define también el método LogJobStop. Este método auxiliar registra los detalles del error.

            private static void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("********************");
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine("Please wait while local tasks or downloads complete...");
                        Console.WriteLine("********************");
                        Console.WriteLine();
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

            private static void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);
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
                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobStop-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }

            private static void LogJobDetails(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nJob ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);

                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobDetails-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }
                    
            private static string JobIdAsFileName(string jobID)
            {
                return jobID.Replace(":", "_");
            }

    -   El método WriteToFile escribe un archivo en la carpeta de salida especificada.

            static void WriteToFile(string outFilePath, string fileContent)
            {
                StreamWriter sr = File.CreateText(outFilePath);
                sr.Write(fileContent);
                sr.Close();
            }

    -   Después de haber codificado los recursos en los Servicios multimedia, puede tener acceso a los recursos de salida que son resultado de un trabajo de codificación. Este tutorial le enseña dos formas de tener acceso a la salida de un trabajo de codificación:

        -   Creación de una URL de SAS a un recurso en el servidor.
        -   Descarga del recurso de salida del servidor.

        El método GetAssetSasUrlList crea una lista de URL de SAS para todos los archivos en un recurso.

            static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
            {
                // Declare a list to contain all the SAS URLs.
                List<String> fileSasUrlList = new List<String>();

                // If the asset has files, build a list of URLs to 
                // each file in the asset and return. 
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    string sasUrl = BuildFileSasUrl(file, locator);
                    fileSasUrlList.Add(sasUrl);
                }

                // Return the list of SAS URLs.
                return fileSasUrlList;
            }

            // Create and return a SAS URL to a single file in an asset. 
            static string BuildFileSasUrl(IAssetFile file, ILocator locator)
            {
                // Take the locator path, add the file name, and build 
                // a full SAS URL to access this file. This is the only 
                // code required to build the full URL.
                var uriBuilder = new UriBuilder(locator.Path);
                uriBuilder.Path += "/" + file.Name;

                // Optional:  print the locator.Path to the asset, and 
                // the full SAS URL to the file
                Console.WriteLine("Locator path: ");
                Console.WriteLine(locator.Path);
                Console.WriteLine();
                Console.WriteLine("Full URL to file: ");
                Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
                Console.WriteLine();


                //Return the SAS URL.
                return uriBuilder.Uri.AbsoluteUri;
            }

    -   El método **DownloadAssetToLocal** descarga cada archivo del recurso en una carpeta local. En este ejemplo, debido a que el recurso se creó con un archivo multimedia de entrada, la recopilación de archivos del recurso de salida contiene dos archivos: el archivo multimedia codificado (un archivo .mp4) y un archivo .xml con metadatos sobre el recurso. El método descarga ambos archivos.

            static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
            {
                // This method illustrates how to download a single asset. 
                // However, you can iterate through the OutputAssets
                // collection, and download all assets if there are many. 

                // Get a reference to the job. 
                IJob job = GetJob(jobId);
                // Get a reference to the first output asset. If there were multiple 
                // output media assets you could iterate and handle each one.
                IAsset outputAsset = job.OutputMediaAssets[0];

                IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
                ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
                BlobTransferClient blobTransfer = new BlobTransferClient
                {
                    NumberOfConcurrentTransfers = 10,
                    ParallelTransferThreadCount = 10
                };

                var downloadTasks = new List<Task>();
                foreach (IAssetFile outputFile in outputAsset.AssetFiles)
                {
                    // Use the following event handler to check download progress.
                    outputFile.DownloadProgressChanged += DownloadProgress;

                    string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                    Console.WriteLine("File download path:  " + localDownloadPath);

                    downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

                    outputFile.DownloadProgressChanged -= DownloadProgress;
                }

                Task.WaitAll(downloadTasks.ToArray());

                return outputAsset;
            }

            static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
            {
                Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
            }

    -   Los métodos auxiliares GetJob y GetAsset consultan y devuelven una referencia a un objeto de trabajo y un objeto de recurso con identificadores dados. Puede usar un tipo similar de consulta LINQ para devolver referencias a otros objetos de los Servicios multimedia en el servidor.

            static IJob GetJob(string jobId)
            {
                // Use a Linq select query to get an updated 
                // reference by Id. 
                var jobInstance =
                    from j in _context.Jobs
                    where j.Id == jobId
                    select j;
                // Return the job reference as an Ijob. 
                IJob job = jobInstance.FirstOrDefault();

                return job;
            }
            static IAsset GetAsset(string assetId)
            {
                // Use a LINQ Select query to get an asset.
                var assetInstance =
                    from a in _context.Assets
                    where a.Id == assetId
                    select a;
                // Reference the asset as an IAsset.
                IAsset asset = assetInstance.FirstOrDefault();

                return asset;
            }

## Prueba del código

Ejecute el programa (presione F5). La consola muestra una salida similar a la siguiente:

    Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
    Time created: 11/14/2012 12:00:00 AM
    Created assetFile interview2.wmv
    Upload interview2.wmv
    Done uploading of interview2.wmv using Upload()

    Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
    Job Name: My encoding job
    Job submitted (client UTC time): 11/14/2012 10:09:39 PM
    Media Services account name: Add-Media-Services-Account-Name
    Media Services account location: Add-Media-Services-account-location-name

    Job(My encoding job) state: Queued.
    Please wait...

    Job(My encoding job) state: Processing.
    Please wait...

    ********************
    Job(My encoding job) is finished.
    Please wait while local tasks or downloads complete...
    ********************

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
    A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
    BxERnav8Jb6hL7fxylq3oESc%3D

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
    1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
    od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

    Downloads are in progress, please wait.

    File download path:  C:\supportFiles\outputfiles\interview2.mp4
    1.70952185308162 % download progress.
    3.68508804454907 % download progress.
    6.48870388360293 % download progress.
    6.83808741232649 % download progress.
    . . . 
    99.0763740574049 % download progress.
    99.1522674787341 % download progress.
    100 % download progress.
    File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
    100 % download progress.

1.  Como resultado de la ejecución de esta aplicación, ocurre lo siguiente:

2.  Se carga un archivo .wmv en los Servicios multimedia.

3.  Posteriormente, el archivo se codifica usando el valor predefinido **H264 Broadband 720p** de **Azure Media Encoder**.

4.  Se crea el archivo FileSasUrlList.txt en la carpeta \\supportFiles\\outputFiles. El archivo contiene la URL al recurso codificado.

    Para reproducir el archivo multimedia, copie la dirección URL en el recurso desde el archivo de texto y péguela en un explorador.

5.  El archivo multimedia .mp4 y el archivo \_metadata.xml se descargan a la carpeta outputFiles.

<div class="dev-callout"> 
<strong>Nota:</strong> 
<p>En el modelo de objetos de los Servicios multimedia, un recurso es un objeto de recopilaci&oacute;n de contenido de los Servicios multimedia que representa de uno a muchos archivos. La ruta del localizador proporciona una URL de blobs de Azure, que es la ruta base a este recurso en el almacenamiento de Azure. Para tener acceso a archivos espec&iacute;ficos dentro del recurso, agregue un nombre de archivo a la ruta del localizador base.</p> 
</div>

## Pasos siguientes

Este tutorial ha demostrado una secuencia de tareas de programación para crear una aplicación sencilla de Servicios multimedia. Aprendió las tareas fundamentales de la programación de Servicios multimedia, incluida la obtención de contenido del servidor, la creación de recursos, la codificación de recursos y la descarga u obtención de acceso a los recursos en el servidor. Para conocer los pasos siguientes y tareas de desarrollo más avanzadas, consulte lo siguiente:

-   [Uso de Servicios multimedia][]
-   [Entrega de contenido][]

<!-- Anchors. -->

  [Descargar]: http://go.microsoft.com/fwlink/?linkid=253275
  [Configuración de su proyecto]: #Step1
  [Obtención del contexto de servidor de los Servicios multimedia]: #Step2
  [Creación de un recurso y carga de los archivos asociados con el recurso en los Servicios multimedia]: #Step3
  [Codificación de un recurso y descarga de un recurso de salida]: #Step4
  [Creación de una cuenta de Servicios multimedia]: http://go.microsoft.com/fwlink/?LinkId=256662
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5
  [Valores predefinidos del sistema Media Services Encoder]: http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx
  [Códecs y tipos de archivo compatibles con Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973634.aspx
  [1]: http://msdn.microsoft.com/library/windowsazure/jj129582.aspx
  [Uso de Servicios multimedia]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/media-services/
  [Entrega de contenido]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973618.aspx
