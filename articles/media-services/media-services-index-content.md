<properties 
	pageTitle="Indización de archivos multimedia con el Indizador multimedia de Azure" 
	description="El Indizador multimedia de Azure permite que el contenido de los archivos multimedia se puedan buscar y genera una transcripción de texto completo para las palabras clave y subtítulos. En este tema se muestra cómo usar el Indizador multimedia." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/21/2015"   
	ms.author="juliako"/>


# Indización de archivos multimedia con el Indizador multimedia de Azure

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)


El Indizador multimedia de Azure permite que el contenido de los archivos multimedia se puedan buscar y genera una transcripción de texto completo para las palabras clave y subtítulos. Puede procesar uno o varios archivos multimedia en un lote.

>[AZURE.IMPORTANT]Al indizar contenido, asegúrese de usar archivos multimedia que tengan una voz muy clara (sin música de fondo, ruido, efectos ni silbido de micrófono). Algunos ejemplos de contenido adecuado son: reuniones, conferencias o presentaciones grabadas. Es posible que el siguiente contenido no sea adecuado para la indización: películas, programas de TV, cualquier elemento con audio y efectos de sonido mezclados o contenido mal gragado con ruido de fondo (silbido).


Un trabajo de indización genera las siguientes salidas.

- Archivos de subtítulos en los siguientes formatos: **SAMI**, **TTML** y **WebVTT**.

	Entre los archivos de subtítulos se incluye una etiqueta denominada Recognizability que puntúa un trabajo de indización en función de lo reconocible que resulta la voz en el vídeo de origen. Puede usar el valor de Recognizability para filtrar los archivos de salida por facilidad de uso. Una puntuación baja significa unos resultados de indización pobres debido a la calidad del audio.
- Archivo de palabras clave (XML).
- Archivo blob de indización de audio (AIB) para usar con SQL Server.
	
	Para obtener más información, consulte [Uso de archivos AIB con Azure Media Indexer y SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).


Este tema muestra cómo crear trabajos de indización para **Indización de un recurso** e **Indización de varios archivos**.

Para ver las actualizaciones más recientes de Azure Media Indexer, consulte [Blogs de Servicios multimedia](http://azure.microsoft.com/blog/topics/media-services/).

##Uso de archivos de manifiesto y de manifiesto para tareas de indización

Puede especificar más detalles de las tareas de indización mediante la configuración de tarea. Por ejemplo, puede especificar los metadatos que se usarán para el archivo multimedia. Estos metadatos los usa el motor de lenguaje para ampliar su vocabulario y mejora considerablemente la precisión del reconocimiento de voz.

También puede procesar varios archivos multimedia a la vez mediante un archivo de manifiesto.

Para obtener más información, consulte [Valores preestablecidos de tarea para Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).

##Indización de un recurso

El método siguiente carga un archivo multimedia como un recurso y crea un trabajo para indizarlo.

Tenga en cuenta que si no se especifica ningún archivo de configuración, el archivo multimedia se indizará con todos los valores predeterminados.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
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
	
###<a id="output_files"></a>Archivos de salida

El trabajo de indización genera los siguientes archivos de salida. Los archivos se almacenarán en el primer recurso de salida.


<table border="1">
<tr><th>Nombre de archivo</th><th>Descripción</th></tr>
<tr><td>InputFileName.aib </td>
<td>Archivo Blob de indización de audio.<br/><br/>
El archivo Blob de indización de audio (AIB) es un archivo que se puede buscar en Microsoft SQL Server mediante la búsqueda de texto completo. El archivo AIB es más eficaz que los archivos de subtítulos simples, porque contiene alternativas para cada palabra, lo que permite una experiencia de búsqueda mucho más rica.
<br/>
<br/>
Requiere la instalación del complemento de SQL Indizador en un equipo que ejecute Microsoft SQL Server 2008 o posterior. Buscar AIB mediante la búsqueda de texto completo de Microsoft SQL Server proporciona resultados de búsqueda más precisos que buscar los archivos de subtítulos generados por WAMI. Esto se debe a que el archivo AIB contiene palabras alternativas que suenan de forma similar mientras que los archivos de subtítulos contienen la palabra de mayor confianza para cada segmento del audio. Si la búsqueda de palabras habladas es de suma importancia, se recomienda usar el archivo AIB junto con Microsoft SQL Server.
<br/><br/>
Para descargar el complemento, haga clic en <a href="http://aka.ms/indexersql">Complemento de Azure Media Indexer</a>.
<br/><br/>
También es posible usar otros motores de búsqueda, como Apache Lucene/Solr para indizar simplemente el vídeo en función de los subtítulos y los archivos XML de palabras clave, pero esto generará unos resultados de búsqueda menos precisos.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>Archivos de subtítulos (CC) en formatos SAMI, TTML y WebVTT.
<br/><br/>
Se pueden usar para crear archivos de audio y vídeo accesibles para personas con discapacidades auditivas.
<br/><br/>
Entre los archivos de subtítulos se incluye una etiqueta denominada <b>Recognizability</b> que puntúa un trabajo de indización en función de lo reconocible que resulta la voz en el vídeo de origen. Puede usar el valor de <b>Recognizability</b> para filtrar los archivos de salida por facilidad de uso. Una puntuación baja significa unos resultados de indización pobres debido a la calidad del audio.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>Archivo de palabras clave.
<br/><br/>
El archivo de palabras clave es un archivo XML que contiene las palabras clave que se extraen del contenido de voz, con información de frecuencia y desplazamiento.
<br/><br/>
El archivo se puede usar para varios propósitos, por ejemplo, para realizar análisis de voz, o exponerse a motores de búsqueda como Bing, Google o Microsoft SharePoint para hacer que los archivos multimedia sean más reconocible, o bien usarse para proporcionar anuncios más relevantes.</td></tr>
</table>

Si no se indizan correctamente todos los archivos multimedia de entrada, el trabajo de indización fallará con el código de error 4000. Para obtener más información, consulte [Códigos de error](#error_codes).

##Indización de varios archivos

El método siguiente carga varios archivos multimedia como un recurso y crea un trabajo para indizar todos esos archivos en un lote.

Un archivo de manifiesto con la extensión .lst se crea y se carga en el recurso. El archivo de manifiesto contiene la lista de todos los archivos del recurso. Para obtener más información, consulte [Valores preestablecidos de tarea para Azure Media Indexer](https://msdn.microsoft.com/library/azure/dn783454.aspx).
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // Create an asset and upload to storage.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // Create a manifest file that contains all the asset file names and upload to storage.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
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
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


###Archivos de salida

Cuando haya más de un archivo multimedia de entrada, WAMI generará un archivo de manifiesto para las salidas del trabajo denominado 'JobResult.txt'. Para cada archivo multimedia de entrada, los archivos de palabras clave y AIB, SAMI, TTML y WebVTT resultantes se numeran secuencialmente, como se muestra a continuación.

Para obtener descripciones de los archivos de salida, consulte [Archivos de salida](#output_files).


<table border="1">
<tr><th>Nombre de archivo</th><th>Descripción</th></tr>
<tr><td>JobResult.txt</td>
<td>Manifiesto de salida
<br/><br/>A continuación se muestra el formato del archivo de manifiesto de salida (JobResult.txt).
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
Cada fila representa un archivo multimedia de entrada:
<br/><br/>
InputFile: nombre de archivo del recurso o URL del archio multimedia de entrada.
<br/><br/>
Alias: nombre de archivo de salida correspondiente.
<br/><br/>
MediaLength: longitud del archivo multimedia de entrada en segundos. Puede ser 0 si se ha producido un error en esta entrada.
<br/><br/>
Error: indica si el archivo multimedia se ha indizado correctamente. 0 si se realiza correctamente; en caso contrario, producirá un error. Consulte <a href="#error_codes">Códigos de error para ver errores concretos</a>.
</td></tr>
<tr><td>Media_1.aib </td>
<td>File #0: archivo de blob de indización de audio.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>File #0: archivos de subtítulos (CC) en formatos SAMI, TTML y WebVTT.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>File #0: archivo de palabras clave.</td></tr>
<tr><td>Media_2.aib </td>
<td>File #1: archivo de blob de indización de audio.</td></tr>
</table>

Si no se indizan correctamente todos los archivos multimedia de entrada, el trabajo de indización fallará con el código de error 4000. Para obtener más información, consulte [Códigos de error](#error_codes).

###Trabajo parcialmente correcto

Si no se indizan correctamente todos los archivos multimedia de entrada, el trabajo de indización fallará con el código de error 4000. Para obtener más información, consulte [Códigos de error](#error_codes).


Se generan las mismas salidas (como trabajos realizados correctamente). Puede consultar el archivo de manifiesto de salida para ver qué archivos de entrada son erróneos, según los valores de columna Error. En el caso de los archivos de entrada erróneos, no se generarán los archivos de palabras clave y AIB, SAMI, TTML y WebVTT resultantes.


### <a id="error_codes"></a>Códigos de error


<table border="1">
<tr><th>Código</th><th>Nombre</th><th>Razones posibles</th></tr>
<tr><td>2000</td><td>Configuración no válida</td><td>Configuración no válida</td></tr>
<tr><td>2001</td><td>Recursos de entrada no válidos</td><td>Faltan recursos de entrada o están vacíos.</td></tr>
<tr><td>2002</td><td>Manifiesto no válido</td><td>El manifiesto está vacío o contiene elementos no válidos.</td></tr>
<tr><td>2003</td><td>No se pudo descargar el archivo multimedia</td><td>Dirección URL no válida en el archivo de manifiesto.</td></tr>
<tr><td>2004</td><td>Protocolo no admitido</td><td>No se admite el protocolo de dirección URL de los medios.</td></tr>
<tr><td>2005</td><td>Tipo de archivo no admitido</td><td>No se admite el tipo de archivo multimedia de entrada.</td></tr>
<tr><td>2006</td><td>Hay demasiados archivos de entrada</td><td>Hay más de 10 archivos en el manifiesto de entrada. </td></tr>
<tr><td>3000</td><td>No se pudo descodificar el archivo multimedia</td>
<td>Códec multimedia no admitido.
<br/>o<br/>
El archivo multimedia está dañado.
<br/>o<br/>
No hay ninguna secuencia de audio en el archivo multimedia de entrada.</td></tr>
<tr><td>4000</td><td>Indización de lote parcialmente correcta</td><td>Algunos de los archivos multimedia de entrada no se pudieron indizar. Para obtener más información, consulte <a href="output_files">Archivos de salida</a>.</td></tr>
<tr><td>otros</td><td>Errores internos</td><td>Póngase en contacto con el equipo de soporte técnico.</td></tr>
</table>


##<a id="supported_languages"></a>Idiomas admitidos

Actualmente, se admiten los idiomas inglés y español. Para obtener más información, consulte [Azure Media Indexer en español](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)


##Rutas de aprendizaje de Servicios multimedia

Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

- [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Vínculos relacionados

[Uso de archivos AIB con el Indizador multimedia de Azure y SQL Server](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=Oct15_HO3-->