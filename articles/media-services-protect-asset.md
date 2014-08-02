<properties linkid="develop-media-services-how-to-guides-encrypt-assets" urlDisplayName="Encrypt Assets in Media Services" pageTitle="How to Encrypt Assets in Media Services - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="migree" solutions="" manager="" editor="" />

Protección de un recurso con PlayReady
======================================

Este artículo forma parte de una serie en la que se presenta la programación de los Servicios multimedia de Azure. El tema anterior trataba de [Comprobación del progreso del trabajo](http://go.microsoft.com/fwlink/?LinkID=301737&clcid=0x409).

En Servicios multimedia de Azure puede enviar un trabajo que integre la protección Microsoft PlayReady para cifrar un recurso. El código de esta sección utiliza varios archivos de transmisión de una carpeta de entrada, crea una tarea y los cifra con la protección PlayReady.

En el siguiente ejemplo se muestra cómo crear un trabajo sencillo para proporcionar la protección PlayReady.

1.  Recupere los datos de configuración. Puede obtener un archivo de configuración de ejemplo del tema [Valores preestablecidos de tarea para Azure Media Encryptor](http://msdn.microsoft.com/en-us/library/hh973610.aspx).
2.  Cargue un archivo de entrada MP4.
3.  Convierta el archivo MP4 en un recurso de Smooth Streaming.
4.  Cifre el recurso con PlayReady.
5.  Envíe el trabajo.

En el siguiente ejemplo de código se muestra cómo implementar los pasos:

``` {}
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Crear un recurso con cifrado de almacenamiento y cargar el MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declarar un trabajo nuevo para que contenga las tareas.
    IJob job = _context.Jobs.Create("Mi trabajo de protección PlayReady");

    // Configurar la primera tarea. 

    // Leer los datos de configuración de la tarea en una cadena. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Obtener una instancia de procesador multimedia.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");

    // Crear una tarea con los detalles de conversión, usando los datos de configuración. 
    ITask task = job.Tasks.AddNew("Mi Mp4 a tarea suave",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Especificar el recurso de entrada que se va a convertir.
    task.InputAssets.Add(asset);

    // Agregar un recurso de salida que va a contener los resultados del trabajo. No tenemos que 
    // conservar el recurso de salida en el almacenamiento. Por lo tanto, establezca el parámetro shouldPersistOutputOnCompletion
    // en false. 
    task.OutputAssets.AddNew("Streaming del recurso de salida", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Configurar la tarea de cifrado. 

    // Leer los datos de configuración en una cadena. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Obtener una instancia de procesador multimedia.
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Azure Media Encryptor");

    // Crear una segunda tarea especificando un nombre de tarea, el procesador multimedia y la configuración.
    ITask playreadyTask = job.Tasks.AddNew("Mi tarea PlayReady",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Agregar el recurso de entrada, que es el recurso de salida de Smooth Streaming de la primera tarea. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Agregar un recurso de salida que va a contener los resultados del trabajo. Conservar el resultado estableciendo 
    // el parámetro shouldPersistOutputOnCompletion en true.
    playreadyTask.OutputAssets.AddNew("Recurso de salida con protección PlayReady",
        AssetCreationOptions.None);

    // Usar el siguiente controlador de eventos para comprobar el progreso del trabajo. 
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Iniciar el trabajo.
    job.Submit();

    // De manera opcional, registrar los detalles del trabajo. De esta manera se muestran los detalles básicos del trabajo
    // en la consola y se guardan en un archivo JobDetails-{JobId}.txt 
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
```

Para obtener más información acerca de la protección PlayReady, consulte:

-   [Proteger Smooth Stream y MPEG DASH con PlayReady](http://msdn.microsoft.com/en-us/library/dn189154.aspx)
-   [Microsoft PlayReady](http://www.microsoft.com/PlayReady/)

Pasos siguientes
----------------

Ahora que sabe cómo proteger recursos con Servicios multimedia, diríjase al tema [Administración de recursos](http://go.microsoft.com/fwlink/?LinkID=301943&clcid=0x409).

