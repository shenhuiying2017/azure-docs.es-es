<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

Codificación de un recurso
==========================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Entrega de un procesador de multimedia](http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409).

Puede codificar el contenido multimedia del servidor con una serie de codificaciones y formatos multimedia que usa Azure Media Encoder. También puede usar un codificador proporcionado por un socio de Servicios multimedia. Podrá encontrar codificadores de terceros en [Azure Marketplace](https://datamarket.azure.com/). Puede especificar los detalles de las tareas de codificación mediante las cadenas de [valores preestablecidos](http://msdn.microsoft.com/en-us/library/hh973610.aspx) o los archivos de configuración.

Codificación en MP4
-------------------

El método siguiente carga un solo recurso y crea un trabajo para codificarlo en MP4 usando el valor preestablecido "H264 Broadband 720p", que creará un solo MP4 usando la codificación H264 con una resolución de 720p:

``` {}
    static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
    {
        //Crear un recurso cifrado y cargarlo en el almacenamiento.
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
            inputMediaFilePath);

        // Declarar un trabajo nuevo.

        IJob job = _context.Jobs.Create("Mi trabajo de codificación");
    
        // Obtener una referencia a Azure Media Encoder.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Codificador multimedia de Azure");
    
        // Crear una tarea con los detalles de codificación, usando un valor predefinido de cadena.
        ITask task = job.Tasks.AddNew("Mi tarea de codificación",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);
    
        // Especificar el recurso de entrada que se va a codificar.
        task.InputAssets.Add(asset);
    
        // Agregar un recurso de salida que va a contener los resultados del trabajo. 
        // Esta salida se especifica como AssetCreationOptions.None, lo que 
        // significa que el recurso de salida no está cifrado. 
        task.OutputAssets.AddNew("Recurso de salida", AssetCreationOptions.None);
    
        // Usar el siguiente controlador de eventos para comprobar el progreso del trabajo.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);
    
        // Iniciar el trabajo.
        job.Submit();
    
        // De manera opcional, registrar los detalles del trabajo. De esta manera se muestran los detalles básicos del trabajo
        // en la consola y se guardan en un archivo JobDetails-JobId.txt 
        // en su carpeta de salida.
        LogJobDetails(job.Id);
    
        // Comprobar la ejecución del trabajo y esperar a que finalice. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
    
        // Si el estado del trabajo es Error, el método de administración de 
        // eventos para el progreso del trabajo debe registrar los errores.  En este caso comprobamos 
        // el estado del error y la salida, si es necesario.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nSaliendo del método debido a un error del trabajo".);
            return job;
        }
    
        // Realizar otras tareas. Por ejemplo, obtener acceso a los recursos que son la salida de un trabajo, 
        // mediante la creación de las URL al recurso del servidor o una operación de descarga. 
        return job;
    }

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("El estado del trabajo cambió el evento:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("El trabajo ha finalizado. Espere mientras se realizan las tareas o descargas locales...");
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
```

Codificación en Smooth Streaming
--------------------------------

Si desea codificar un vídeo en Smooth Streaming, tiene dos opciones:

-   Codificar directamente en Smooth Streaming
-   Codificar en MP4 y, a continuación, convertir a Smooth Streaming

Para codificar directamente en Smooth Streaming, use el código mostrado arriba, pero utilice uno de los valores preestablecidos del codificador de Smooth Streaming. Para ver una lista completa de valores preestablecidos del codificador, consulte [Valores predefinidos del sistema Media Services Encoder](http://msdn.microsoft.com/en-us/library/jj129582.aspx).

Para convertir MP4 a Smooth Streaming, use Azure Media Packager. Azure Media Packager no admite valores preestablecidos de cadenas, por lo que tiene que especificar las opciones de configuración en XML. Podrá encontrar el XML necesario para convertir MP4 a Smooth Streaming en [Valores preestablecidos de tarea para Azure Media Packager](http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx). Copie y pegue el XML en un archivo llamado MediaPackager\_MP4ToSmooth.xml en su proyecto. El código siguiente ilustra cómo convertir un recurso de MP4 a Smooth Streaming. El método que aparece a continuación toma un recurso existente y lo convierte.

``` {}
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
    // Declarar un trabajo nuevo para que contenga las tareas.
    IJob job = _context.Jobs.Create("Convertir a un trabajo de Smooth Streaming");
    // Configurar la primera tarea para convertir de MP4 a Smooth Streaming. 
    // Leer el XML de configuración de la tarea.
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Obtener una referencia a media packager.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Crear una tarea con los detalles de conversión, usando los datos de configuración.
    ITask task = job.Tasks.AddNew("Mi tarea de Mp4 a Smooth",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Especificar el recurso de entrada que se va a convertir.
    task.InputAssets.Add(assetToConvert);
    // Agregar un recurso de salida que va a contener los resultados del trabajo.
    task.OutputAssets.AddNew("Streaming del recurso de salida", AssetCreationOptions.None);
    // Usar el siguiente controlador de eventos para comprobar el progreso del trabajo. 
    // El método StateChange es el mismo que el del ejemplo anterior.
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs>(StateChanged);
    // Iniciar el trabajo.
    job.Submit();
    // Comprobar la ejecución del trabajo y esperar a que finalice. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Obtener un trabajo actualizado después de esperar en un subproceso.
    job = GetJob(job.Id);
    // Comprobar si hay errores.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nSaliendo del método debido a un error del trabajo".);
    }
    return job;
}
```

Para obtener más información acerca de los recursos que se están procesando, consulte:

-   [Procesamiento de recursos con el SDK de Media Services para .NET](http://msdn.microsoft.com/en-us/library/jj129580.aspx)
-   [Procesamiento de recursos con la API de REST de Media Services](http://msdn.microsoft.com/en-us/library/jj129574.aspx)

Pasos siguientes
----------------

Ahora que sabe cómo crear un trabajo para codificar un recurso, vaya al tema [Comprobación del progreso del trabajo con Servicios multimedia](http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409).

