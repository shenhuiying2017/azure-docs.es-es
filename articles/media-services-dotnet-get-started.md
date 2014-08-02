<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

Introducción a los Servicios multimedia
=======================================

En este tutorial se muestra cómo empezar a desarrollar con los Servicios multimedia de Azure. Presenta el flujo de trabajo básico de los Servicios multimedia y la mayoría de los objetos y tareas de programación más comunes necesarios para el desarrollo de los Servicios multimedia. Al término del tutorial, podrá reproducir un archivo multimedia de ejemplo que cargó, codificó y descargó. O bien, puede dirigirse al recurso codificado y reproducirlo en el servidor.

Un proyecto de C\# Visual Studio que contiene el código de este tutorial está disponible aquí: [Descargar](http://go.microsoft.com/fwlink/?linkid=253275).

Este tutorial le guiará a través de estos pasos básicos:

-   [Configuración de su proyecto](#Step1)
-   [Obtención del contexto de servidor de los Servicios multimedia](#Step2)
-   [Creación de un recurso y carga de los archivos asociados con el recurso en los Servicios multimedia](#Step3)
-   [Codificación de un recurso y descarga de un recurso de salida](#Step4)

Requisitos previos
------------------

Se requieren los siguientes requisitos previos para el tutorial y el desarrollo basado en el SDK de los Servicios multimedia de Azure.

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Para obtener más información, consulte [Creación de una cuenta de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=256662).
-   Sistemas operativos: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5 o .NET Framework 4.
-   Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).
-   Instale **SDK de Azure para .NET**, **SDK de los Servicios multimedia de Azure para .NET** y **WCF Data Services 5.0 para las bibliotecas de OData V3** y agregue referencias a su proyecto usando el paquete [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices). La siguiente sección demuestra cómo instalar y agregar estas referencias.

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5).

Configuración de su proyecto
----------------------------

1.  Cree una aplicación de consola de C\# nueva en Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación** y el **nombre de la solución** y, a continuación, haga clic en OK.

2.  Agregue una referencia al ensamblado System.Configuration.

    Para agregar referencias usando el cuadro de diálogo **Administrar referencias**, siga estos pasos. Haga clic con el botón secundario en el nodo **Referencias** en el **Explorador de soluciones** y seleccione **Agregar referencia**. En el cuadro de diálogo **Administrar referencias**, seleccione los ensamblados adecuados (en este caso System.Configuration).

3.  Si todavía no lo ha hecho, agregue referencias al **SDK de Azure para .NET** (Microsoft.WindowsAzure.StorageClient.dll), **SDK de los Servicios multimedia de Azure para .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) y las bibliotecas de **WCF Data Services 5.0 para OData V3** (Microsoft.Data.OData.dll) usando el paquete [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices).

    Para agregar referencias usando Nuget, realice lo siguiente. En el menú principal de Visual Studio, seleccione TOOLS -\> Library Package Manager -\> Package Manager Console. En la ventana de la consola, escriba *Install-Package [nombre del paquete]* y presione Entrar (en este caso use el siguiente comando: *Install-Package windowsazure.mediaservices*).

4.  Agregue una sección *appSettings* al archivo **app.config** y establezca los valores para su nombre y clave de cuenta de Servicios multimedia de Azure. Obtuvo el nombre y la clave de la cuenta de Servicios multimedia durante el proceso de configuración de la cuenta. Agregue estos valores al atributo de valor para cada configuración en el archivo app.config en el proyecto de Visual Studio.

    > [WACOM.NOTE] 
    > En Visual Studio 2012, el archivo App.config se agrega de manera predeterminada. En Visual Studio 2010, tiene que agregar manualmente el archivo de configuración de la aplicación.

	<pre><code>
	&lt;configuration&gt;
  	. . . 
  	&lt;appSettings&gt;
    	&lt;add key="accountName" value="Add-Media-Services-Account-Name" /&gt;
    	&lt;add key="accountKey" value="Add-Media-Services-Account-Key" /&gt;
  	&lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>

5.  Cree una carpeta nueva en su equipo local y asígnele el nombre supportFiles (en este ejemplo, supportFiles se encuentra en el directorio de proyecto MediaServicesGettingStarted). El [proyecto](http://go.microsoft.com/fwlink/?linkid=253275) que acompańa a este tutorial contiene el directorio supportFiles. Puede copiar el contenido de este directorio en su carpeta supportFiles.

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

7.  Agregue las siguientes variables de ruta de nivel de clase. La ruta **\_supportFiles** debe dirigirse a la carpeta que creó en un paso anterior.

         // Ruta de archivos de compatibilidad base.  Actualizar este campo para que se dirija a la ruta base  
         // de la carpeta de archivos de compatibilidad local que crea. 
         private static readonly string _supportFiles =
                     Path.GetFullPath(@"../..\supportFiles");
            
         // Rutas a los archivos de compatibilidad (dentro de la ruta base anterior). Puede usar 
         // los archivos multimedia de ejemplo que se proporcionan de la carpeta "supportFiles" o 
         // proporcionar rutas a sus propios archivos multimedia a continuación para ejecutar estos ejemplos.
         private static readonly string _singleInputFilePath =
             Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
         private static readonly string _outputFilesFolder =
             Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Agregue las siguientes variables de nivel de clase para recuperar la configuración de autenticación y conexión. Esta configuración se obtiene del archivo App.Config y es necesaria para conectarse a los Servicios multimedia, autenticarse y obtener un token para que pueda tener acceso al contexto del servidor. El código del proyecto hace referencia a estas variables para crear una instancia de contexto de servidor.

         private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
         private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Agregue la siguiente variable de nivel de clase que se usa como referencia estática para el contexto de servidor.

         // Campo para contexto de servicio.
         private static CloudMediaContext _context = null;

Obtención del contexto de los Servicios multimedia
--------------------------------------------------

El objeto de contexto de los Servicios multimedia contiene todos los objetos y recopilaciones fundamentales para tener acceso a la programación de los Servicios multimedia. El contexto incluye referencias a recopilaciones importantes, incluidos trabajos, recursos, archivos, directivas de acceso, localizadores y otros objetos. Debe obtener el contexto de servidor para la mayoría de las tareas de programación de los Servicios multimedia.

En el archivo Program.cs, agregue el siguiente código como primer elemento de su método **Main**. Este código usa los valores del nombre y la clave de cuenta de los Servicios multimedia del archivo app.config para crear una instancia del contexto de servidor. La instancia se asigna a la variable **\_context** que creó en el nivel de clase.

    // Obtener el contexto de servicio.
    _context = new CloudMediaContext(_accountName, _accountKey);

Creación de un recurso y carga de un archivo
--------------------------------------------

El código de esta sección realiza lo siguiente:

1.  Crea un recurso vacío  Cuando se crean recursos, puede especificar tres opciones diferentes para codificarlos.
    -   **AssetCreationOptions.None**: Sin cifrado. Si desea crear un recurso sin cifrar, debe configurar esta opción.
    -   **AssetCreationOptions.CommonEncryptionProtected**: Para archivos protegidos con cifrado común (CENC). Un ejemplo es un conjunto de archivos que ya está cifrado para PlayReady.
    -   **AssetCreationOptions.StorageEncrypted**: Cifrado de almacenamiento. Cifra un archivo de entrada limpio antes de que se cargue en el almacenamiento de Azure.

        **Nota:**

        Los Servicios multimedia proporcionan cifrado de almacenamiento en el disco, no por cable como el administrador de derechos digitales (DRM).

2.  Crea una instancia de AssetFile que necesitamos asociar con el recurso.
3.  Crea una instancia de AccessPolicy que define los permisos y la duración del acceso al recurso.
4.  Crea una instancia de Locator que proporciona acceso al recurso.
5.  Carga un solo archivo multimedia en los Servicios multimedia. El proceso de crear y cargar se llama también introducción de recursos.

Agregue los siguientes métodos a la clase.

``` {}
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
```

Agregue una llamada al método después de la línea **\_context = new CloudMediaContext(\_accountName, \_accountKey);** en su método Main.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

Codificación del recurso en el servidor y descarga de un recurso de salida
--------------------------------------------------------------------------

En los Servicios multimedia, puede crear trabajos que procesan contenidos multimedia de varias formas: codificación, cifrado, realización de conversiones de formato, etc. Un trabajo de Servicios multimedia contiene una o más tareas que especifican los detalles del trabajo de procesamiento. En esta sección puede crear una tarea de codificación básica y luego ejecutar un trabajo que la realiza usando el codificador multimedia de Azure. La tarea usa una cadena predefinida para especificar el tipo de codificación que se debe realizar. Para ver los valores de codificación predefinidos disponibles, consulte [Valores predefinidos del sistema Media Services Encoder](http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx). Los Servicios multimedia admiten los mismos formatos de archivo multimedia de entrada y salida que Microsoft Expression Encoder. Para obtener una lista de formatos admitidos, consulte [Códecs y tipos de archivo compatibles con Azure Media Encoder](http://msdn.microsoft.com/en-us/library/windowsazure/hh973634.aspx).

1.  Agregue la siguiente definición del método **CreateEncodingJob** a su clase. Este método demuestra cómo llevar a cabo varias tareas necesarias para un trabajo de codificación:

    -   Declare un trabajo nuevo.
    -   Declare un procesador de multimedia para administrar el trabajo. Un procesador de multimedia es un componente que administra la codificación, el cifrado, la conversión de formatos y otros trabajos de procesamiento relacionados. Existen varios tipos de procesadores de multimedia disponibles (puede iterar a través de todos ellos usando \_context.MediaProcessors). El método GetLatestMediaProcessorByName, que se muestra posteriormente en este tutorial, devuelve el procesador del codificador multimedia de Azure.
    -   Declare una tarea nueva. Todo trabajo tiene una o más tareas. Tenga en cuenta que con la tarea, le asigna un nombre descriptivo, una instancia de procesador de multimedia, una cadena de configuración de tareas y opciones de creación de tareas. La cadena de configuración específica la configuración de codificación. Este ejemplo usa la configuración **H264 Broadband 720p**. Este valor predefinido produce un solo archivo MP4. Para obtener más información sobre este y otros valores predefinidos, consulte [Valores predefinidos del sistema Media Services Encoder](http://msdn.microsoft.com/library/windowsazure/jj129582.aspx).
    -   Agregue un recurso de entrada a la tarea. En este ejemplo, el recurso de entrada es uno que creo en la sección anterior.
    -   Agregue un recurso de salida a la tarea. Para un recurso de salida, especifique un nombre descriptivo, un valor booleano para indicar si va a guardar la salida en el servidor después de finalizar el trabajo y un valor **AssetCreationOptions.None** para indicar que la salida no está cifrada para almacenamiento y transporte.
    -   Envíe el trabajo.
         El envío de un trabajo es el último paso que se requiere para realizar un trabajo de codificación.

    El método demuestra también como realizar otras tareas útiles (pero opcionales), como hacer un seguimiento del progreso del trabajo y tener acceso al recurso que crea su trabajo de codificación.

    ``` {}
    static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
    {
        // Declarar un trabajo nuevo.
        IJob job = _context.Jobs.Create("Mi trabajo de codificación");
        // Obtener una referencia de procesador de multimedia y pasarla al nombre del 
        // procesador que va a usar para la tarea específica.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Codificador multimedia de Azure");

        // Crear una tarea con los detalles de codificación, usando un valor predefinido de cadena.
        ITask task = job.Tasks.AddNew("Mi tarea de codificación",
            processor,
            "H264 Broadband 720p",
            Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

        // Especificar el recurso de entrada que se va a codificar.
        task.InputAssets.Add(asset);
        // Agregar un recurso de salida que va a contener los resultados del trabajo. 
        // Esta salida se especifica como AssetCreationOptions.None, lo que 
        // significa que el recurso de salida no está cifrado. 
        task.OutputAssets.AddNew("Recurso de salida",
            AssetCreationOptions.None);
        // Usar el siguiente controlador de eventos para comprobar el progreso del trabajo.  
        job.StateChanged += new
                EventHandler(StateChanged);

        // Iniciar el trabajo.
        job.Submit();

        // De manera opcional, registrar los detalles del trabajo. De esta manera se muestran los detalles básicos del trabajo
        // en la consola y se guardan en un archivo JobDetails-{JobId}.txt 
        // en su carpeta de salida.
        LogJobDetails(job.Id);

        // Comprobar la ejecución del trabajo y esperar a que finalice. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // **********
        // Código opcional.  Después de este punto no se requiere código para 
        // un trabajo de codificación, pero muestra como tener acceso a los recursos que 
        // son la salida de un trabajo, ya sea mediante la creación de direcciones URL para el 
        // recurso en el servidor o mediante la descarga. 
        // **********

        // Obtener una referencia de trabajo actualizada.
        job = GetJob(job.Id);

        // Si el estado del trabajo es Error, el método de administración de 
        // eventos para el progreso del trabajo debe registrar los errores.  En este caso comprobamos 
        // el estado del error y la salida, si es necesario.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nSaliendo del método debido a un error del trabajo".);
            return job;
        }

        // Obtener una referencia al recurso de salida desde el trabajo.
        IAsset outputAsset = job.OutputMediaAssets[0];
        IAccessPolicy policy = null;
        ILocator locator = null;

        // Declarar una directiva de acceso para los permisos en el recurso. 
        // Puede llamar a un método de creación asincrónico o sincrónico. 
        policy =
            _context.AccessPolicies.Create("Mi directiva de 30 días de solo lectura",
                TimeSpan.FromDays(30),
                AccessPermissions.Read);

        // Crear un localizador de SAS para permitir el acceso directo al recurso 
        // en el almacenamiento de blobs. Puede llamar a un método de creación asincrónico o sincrónico.  
        // Puede establecer el parámetro opcional startTime en 5 minutos 
        // antes que Now para compensar las diferencias de tiempo  
        // entre los relojes del cliente y el servidor. 

        locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Crear una lista de URL de SAS para cada archivo en el recurso. 
        List sasUrlList = GetAssetSasUrlList(outputAsset, locator);

        // Escribir la lista de URL en un archivo local. Puede usar las URL de SAS 
        // guardadas para dirigirse directamente a los archivos en el recurso.
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

            // De manera opcional, descargar la salida en la máquina local.
            DownloadAssetToLocal(job.Id, outputFolder);
        }

        
        return job;
    }
    ```

2 .  Agregue una llamada al método **CreateEncodingJob** en su método **Main**, después de las líneas que agregó anteriormente.

  
      CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
   

3 .  Agregue los siguientes métodos auxiliares a la clase. Se requieren para admitir el método **CreateEncodingJob**. A continuación se encuentra un resumen de los métodos auxiliares.
   -   El método **GetLatestMediaProcessorByName** devuelve un procesador de multimedia adecuado para administrar una codificación, un cifrado u otra tarea de procesamiento relacionada. Puede crear un procesador de multimedia usando el nombre de cadena adecuado del procesador que desea crear. Las posibles cadenas que se pueden transferir al método para el parámetro mediaProcessor son: **Azure Media Encoder**, **Azure Media Packager**, **Azure Media Encryptor**, **Storage Decryption**.
     
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            // Las posibles cadenas que se pueden pasar al 
            // método para el parámetros mediaProcessor:
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
            Console.WriteLine("El estado del trabajo cambió el evento:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("El trabajo ha finalizado".);
                    Console.WriteLine("Espere mientras se realizan las tareas o descargas locales...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Espere...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Convertir remitente como trabajo.
                    IJob job = (IJob)sender;
                    // Mostrar o registrar los detalles del error según sea necesario.
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

            builder.AppendLine("\nEl trabajo se detuvo debido a una cancelación o a un error".);
            builder.AppendLine("***************************");
            builder.AppendLine("Id. de trabajo: " + job.Id);
            builder.AppendLine("Nombre de trabajo: " + job.Name);
            builder.AppendLine("Estado del trabajo: " + job.State.ToString());
            builder.AppendLine("Trabajo iniciado (hora UTC del servidor): " + job.StartTime.ToString());
            builder.AppendLine("Nombre de la cuenta de los Servicios multimedia: " + _accountName);
            // Registrar errores del trabajo, si los hay.  
            if (job.State == JobState.Error)
            {
                builder.Append("Detalles del error: \n");
                foreach (ITask task in job.Tasks)
                {
                    foreach (ErrorDetail detail in task.ErrorDetails)
                    {
                        builder.AppendLine("  Id. de tarea: " + task.Id);
                        builder.AppendLine("    Código de error: " + detail.Code);
                        builder.AppendLine("    Mensaje de error: " + detail.Message + "\n");
                    }
                }
            }
            builder.AppendLine("***************************\n");
            // Escribir la salida en un archivo local y en la consola. La plantilla 
            // de un archivo de salida de error es:  JobStop-{JobId}.txt
            string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
        }

        private static void LogJobDetails(string jobId)
        {
            StringBuilder builder = new StringBuilder();
            IJob job = GetJob(jobId);

            builder.AppendLine("\nId. de trabajo: " + job.Id);
            builder.AppendLine("Nombre de trabajo: " + job.Name);
            builder.AppendLine("Trabajo enviado (hora UTC del cliente): " + DateTime.UtcNow.ToString());
            builder.AppendLine("Nombre de la cuenta de los Servicios multimedia: " + _accountName);

            // Escribir la salida en un archivo local y en la consola. La plantilla 
            // de un archivo de salida de error es:  JobDetails-{JobId}.txt
            string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
            WriteToFile(outputFile, builder.ToString());
            Console.Write(builder.ToString());
        }
                
        private static string JobIdAsFileName(string jobID)
        {
            return jobID.Replace(":", "_");
        }
        ```

-   El método WriteToFile escribe un archivo en la carpeta de salida especificada.

  
        static void WriteToFile(string outFilePath, string fileContent)
        {
            StreamWriter sr = File.CreateText(outFilePath);
            sr.Write(fileContent);
            sr.Close();
        }


-   Después de haber codificado los recursos en los Servicios multimedia, puede tener acceso a los recursos de salida que son resultado de un trabajo de codificación. Este tutorial le enseńa dos formas de tener acceso a la salida de un trabajo de codificación:

      -   Creación de una URL de SAS a un recurso en el servidor.
      -   Descarga del recurso de salida del servidor.

      El método GetAssetSasUrlList crea una lista de URL de SAS para todos los archivos en un recurso.

        ``` {}
        static List GetAssetSasUrlList(IAsset asset, ILocator locator)
        {
            // Declarar una lista para contener todas las URL de SAS.
            List fileSasUrlList = new List();

            // Si el recurso tiene archivos, crear una lista de URL para 
            // cada archivo en el recurso y devolver. 
            foreach (IAssetFile file in asset.AssetFiles)
            {
                string sasUrl = BuildFileSasUrl(file, locator);
                fileSasUrlList.Add(sasUrl);
            }

            // Devolver la lista de URL de SAS.
            return fileSasUrlList;
        }

        // Crear y devolver una URL de SAS para un solo archivo en un recurso. 
        static string BuildFileSasUrl(IAssetFile file, ILocator locator)
        {
            // Tomar la ruta del localizador, agregar el nombre de archivo y crear 
            // una URL de SAS completa para tener acceso a este archivo. Este es el único 
            // código que se requiere para crear la URL completa.
            var uriBuilder = new UriBuilder(locator.Path);
            uriBuilder.Path += "/" + file.Name;

            // Opcional:  imprimir el locator.Path en el recurso y 
            // la URL de SAS completa en el archivo
            Console.WriteLine("Ruta del localizador: ");
            Console.WriteLine(locator.Path);
            Console.WriteLine();
            Console.WriteLine("URL completa al archivo: ");
            Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
            Console.WriteLine();


            //Devolver la URL de SAS.
            return uriBuilder.Uri.AbsoluteUri;
        }
        ```

-   El método **DownloadAssetToLocal** descarga cada archivo del recurso en una carpeta local. En este ejemplo, debido a que el recurso se creó con un archivo multimedia de entrada, la recopilación de archivos del recurso de salida contiene dos archivos: el archivo multimedia codificado (un archivo .mp4) y un archivo .xml con metadatos sobre el recurso. El método descarga ambos archivos.

        ``` {}
        static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
        {
            // Este método ilustra cómo descargar un solo recurso. 
            // No obstante, puede iterar a través de la recopilación OutputAssets,
            // y descargar todos los recursos si hay muchos. 

            // Obtener una referencia al trabajo. 
            IJob job = GetJob(jobId);
            // Obtener una referencia al primer recurso de salida. Si hubiera varios 
            // recursos multimedia de salida, podría iterar y controlar cada uno.
            IAsset outputAsset = job.OutputMediaAssets[0];

            IAccessPolicy accessPolicy = _context.AccessPolicies.Create("Directiva de descarga de archivo", TimeSpan.FromDays(30), AccessPermissions.Read);
            ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
            BlobTransferClient blobTransfer = new BlobTransferClient
            {
                NumberOfConcurrentTransfers = 10,
                ParallelTransferThreadCount = 10
            };

            var downloadTasks = new List();
            foreach (IAssetFile outputFile in outputAsset.AssetFiles)
            {
                // Usar el siguiente controlador de eventos para comprobar el progreso de la descarga.
                outputFile.DownloadProgressChanged += DownloadProgress;

                string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                Console.WriteLine("Ruta de descarga de archivo:  " + localDownloadPath);

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
        ```

  -   Los métodos auxiliares GetJob y GetAsset consultan y devuelven una referencia a un objeto de trabajo y un objeto de recurso con identificadores dados. Puede usar un tipo similar de consulta LINQ para devolver referencias a otros objetos de los Servicios multimedia en el servidor.

        ``` {}
        static IJob GetJob(string jobId)
        {
            // Usar una consulta de selección Linq para obtener una referencia 
            // actualizada mediante el identificador. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Devolver la referencia de trabajo como un Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
        static IAsset GetAsset(string assetId)
        {
            // Usar una consulta de selección LINQ para obtener un recurso.
            var assetInstance =
                from a in _context.Assets
                where a.Id == assetId
                select a;
            // Hacer una referencia al recurso como un IAsset.
            IAsset asset = assetInstance.FirstOrDefault();

            return asset;
        }
        ```

Prueba del código
-----------------

Ejecute el programa (presione F5). La consola muestra una salida similar a la siguiente:

``` {}
Nombre de recurso: UploadSingleFile_11/14/2012 10:09:11 PM
Hora de creación: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Id. de trabajo: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Nombre de trabajo: Mi trabajo de codificación
Trabajo enviado (hora UTC del cliente): 11/14/2012 10:09:39 PM
Nombre de la cuenta de los Servicios multimedia: Add-Media-Services-Account-Name
Ubicación de la cuenta de los Servicios multimedia: Add-Media-Services-account-location-name

Estado del trabajo(Mi trabajo de codificación): En cola.
Espere...

Estado del trabajo(Mi trabajo de codificación): Procesando.
Espere...

********************
El trabajo(Mi trabajo de codificación) ha finalizado.
Espere mientras se realizan las tareas o descargas locales...
********************

Ruta del localizador:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

URL completa al archivo:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Ruta del localizador:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

URL completa al archivo:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Las descargas están en curso, espere.

Ruta de descarga de archivo:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
Ruta de descarga de archivo:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.
```

1.  Como resultado de la ejecución de esta aplicación, ocurre lo siguiente:

2.  Se carga un archivo .wmv en los Servicios multimedia.

3.  Posteriormente, el archivo se codifica usando el valor predefinido **H264 Broadband 720p** de **Azure Media Encoder**.

4.  Se crea el archivo FileSasUrlList.txt en la carpeta \\supportFiles\\outputFiles. El archivo contiene la URL al recurso codificado.

    Para reproducir el archivo multimedia, copie la dirección URL en el recurso desde el archivo de texto y péguela en un explorador.

5.  El archivo multimedia .mp4 y el archivo \_metadata.xml se descargan a la carpeta outputFiles.

**Nota:**

En el modelo de objetos de los Servicios multimedia, un recurso es un objeto de recopilación de contenido de los Servicios multimedia que representa de uno a muchos archivos. La ruta del localizador proporciona una URL de blobs de Azure, que es la ruta base a este recurso en el almacenamiento de Azure. Para tener acceso a archivos específicos dentro del recurso, agregue un nombre de archivo a la ruta del localizador base.

Pasos siguientes
----------------

Este tutorial ha demostrado una secuencia de tareas de programación para crear una aplicación sencilla de Servicios multimedia. Aprendió las tareas fundamentales de la programación de Servicios multimedia, incluida la obtención de contenido del servidor, la creación de recursos, la codificación de recursos y la descarga u obtención de acceso a los recursos en el servidor. Para conocer los pasos siguientes y tareas de desarrollo más avanzadas, consulte lo siguiente:

-   [Uso de los Servicios multimedia](http://www.windowsazure.com/en-us/develop/net/how-to-guides/media-services/)
-   [Entrega de contenido](http://msdn.microsoft.com/en-us/library/windowsazure/hh973618.aspx)

