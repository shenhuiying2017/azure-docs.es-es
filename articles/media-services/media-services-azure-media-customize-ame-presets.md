<properties 
	pageTitle="Manipulación de tareas de codificación mediante la personalización de valores preestablecidos de tareas" 
	description="El Codificador de Servicios multimedia de Azure permite pasar archivos preestablecidos personalizados a Azure Media Encoder. En este tema se muestra cómo personalizar archivos preestablecidos para lograr las tareas siguientes: superponer una imagen en un vídeo existente, controlar los nombres de archivo de salida que genera el codificador y unir vídeos." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Manipulación de tareas de codificación mediante la personalización de valores preestablecidos de tareas 

El Codificador de Servicios multimedia de Azure permite pasar archivos preestablecidos personalizados a Azure Media Encoder. En este tema se muestra cómo personalizar archivos preestablecidos para lograr las tareas siguientes:

- superposición de una imagen en un vídeo existente, 
- control de los nombres de archivos de salida que genera el codificador, 
- unión de vídeos,
- codificación de presentaciones con voz principalmente.

##Control de los nombres de archivos de salida de Azure Media Encoder 

De forma predeterminada, Azure Media Encoder crea nombres de archivo de salida combinando varios atributos del recurso de entrada y el proceso de codificación. Cada atributo se identifica con una macro, tal como se describe a continuación.

A continuación se muestra una lista completa de las macros disponibles para la nomenclatura de los archivos de salida: la velocidad de bits utilizada al codificar el audio, especificada en kbps

- Códec de audio: el códec usado para codificar audio; los valores válidos son: AAC, WMA y DDP
- Número de canales: el número de canales de audio codificados; los valores válidos son: 1, 2 o 6
- Extensión predeterminada: la extensión de archivo predeterminada 
- Idioma: el código de idioma BCP-47 que representa el idioma utilizado en el audio. Actualmente el valor predeterminado es "und". 
- Nombre de archivo original: el nombre del archivo cargado en Almacenamiento de Azure
- StreamId: el identificar del streaming, tal y como define el atributo streamID del elemento <StreamInfo> del archivo predefinido 
- Códec de vídeo: el códec usado para codificar vídeo; los valores válidos son: H264 y VC1
- Velocidad de bits de vídeo: la velocidad de bits utilizada al codificar el vídeo, especificada en kbps

Estas macros pueden combinarse en cualquier permutación para controlar el nombre de los archivos que genera Media Services Encoder. Por ejemplo, la convención de nomenclatura predeterminada es:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

La convención de nomenclatura de los archivos se especifica mediante el atributo DefaultMediaOutputFileName del elemento [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Por ejemplo:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

El codificador insertará guiones bajos entre cada macro; por ejemplo, la configuración anterior generará un nombre de archivo como el siguiente: MyVideo\_H264\_4500kpbs\_AAC\_und\_ch2\_128kbps.mp4.


##Creación de superposiciones

El Codificador de Servicios multimedia de Azure permite superponer una imagen (jpg, bmp, gif, tif), un vídeo o una pista de audio (*.wma, *.mp3, *.wav) en un vídeo existente. Esta funcionalidad es similar a la de Expression Encoder 4 (Service Pack 2).

###Superposiciones con el Codificador de Servicios multimedia

Puede especificar cuándo se presenta la superposición, su duración y, para superposiciones de imagen o vídeo, dónde se mostrará la superposición en la pantalla. También puede hacer que las superposiciones se intensifiquen o se atenúen. Los archivos de audio y vídeo que se van a superponer pueden incluirse en varios recursos o en uno solo. Las superposiciones se controlan con el XML preestablecido que se pasa al codificador. Para obtener una descripción completa del esquema preestablecido, consulte Esquema de Azure Media Encoder. Las superposiciones se especifican en el elemento <MediaFile>, tal como se muestra en el siguiente fragmento de código preestablecido:

	<MediaFile
	    ...
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="WholeSequence"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">
	    ...
	</MediaFile>

###Valores preestablecidos para superposiciones de imagen o vídeo

Las superposiciones pueden proceder de un único recurso o de varios. Cuando se crean superposiciones de vídeo con varios recursos, el nombre de archivo de superposición se especifica en el atributo OverlayFileName con la sintaxis %n% donde n es el índice basado en cero de los recursos de entrada para la tarea de codificación. Cuando se crean superposiciones de vídeo con un solo recurso, el nombre de archivo de superposición se especifica directamente en el atributo OverlayFileName, tal como se muestra en los siguientes fragmentos de código preestablecidos:

Ejemplo 1:

	<!-- Multiple Assets -->
	<MediaFile
		...
		OverlayFileName="%1%"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

Ejemplo 2:

	<!-- Single Asset -->
	<MediaFile
		...
		OverlayFileName="videoOverlay.mp4"
		OverlayRect="257, 144, 255, 144"
		OverlayOpacity="0.9"
		OverlayFadeInDuration="00:00:02"
		OverlayFadeOutDuration="00:00:02"
		OverlayLayoutMode="Custom"
		OverlayStartTime="00:00:05"
		OverlayEndTime="00:00:10.2120000">

La ubicación y el tamaño de la superposición de vídeo se controla mediante el atributo OverlayRect. Se ajustará el tamaño del contenido que se va a superponer para adaptarlo a este rectángulo. La opacidad se controla con el atributo OverlayOpacity. Los valores válidos son 0.0 – 1.0, donde 1.0 es 100% opaco. La superposición se muestra en el tiempo especificado por el atributo OverlayStartTime y termina en el tiempo especificado por el atributo OverlayEndTime. Tanto OverlayStartTime como OverlayEndTime deben estar dentro de la escala de tiempo del vídeo de origen. Para obtener más información acerca de cada atributo específico de superposición, consulte Esquema de Azure Media Encoder.

###Valores preestablecidos para superposiciones de audio

Las superposiciones de audio pueden tener cualquier formato de archivo de audio admitido, por ejemplo. Para obtener una lista completa de formatos de archivo de audio compatibles, consulte Formatos compatibles con el Codificador de Servicios multimedia. Las superposiciones de audio también se especifican en el elemento <MediaFile>, tal como se muestra en el siguiente fragmento de código preestablecido:

	<MediaFile
		...
		AudioOverlayFileName="%1%" <!-- or AudioOverlayFileName=”audioOverlay.mp3” for overlays from a single asset -->
		AudioOverlayLoop="True"
		AudioOverlayLoopingGap="00:00:00"
		AudioOverlayLayoutMode="Custom"
		AudioOverlayStartTime="00:05:00"
		AudioOverlayEndTime="00:10:00"
		AudioOverlayGainLevel="2.2"
		AudioOverlayFadeInDuration="00:00:00"
		AudioOverlayFadeOutDuration="00:00:00">

Para superposiciones de audio almacenadas en varios recursos, el nombre de archivo de superposición de audio se especifica en el atributo AudioOverlayFileName con la sintaxis %n% donde n es el índice basado en cero de la colección de recursos de entrada para la tarea de codificación. Para superposiciones de audio almacenadas en un solo recurso, el nombre de archivo de superposición se especifica directamente en el atributo AudioOverlayFileName. AudioOverlayLayoutMode determina cuándo se presentará la superposición de audio. Cuando está establecida en "WholeSequence", la pista de audio se presentará durante todo el vídeo. Cuando están establecidos en "Custom", los atributos AudioOverlayStartTime y AudioOverlayEndTime determinan cuándo comienza y termina la superposición de audio. Tanto OverlayStartTime como OverlayEndTime deben estar dentro de la escala de tiempo del vídeo de origen. Para obtener más información sobre todos los atributos de superposición de audio, consulte Esquema de Azure Media Encoder. Las superposiciones de audio se pueden combinar con superposiciones de vídeo, como se muestra en el siguiente fragmento de código preestablecido:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch"
	    OverlayFileName="%1%"
	    OverlayRect="257, 144, 255, 144"
	    OverlayOpacity="0.9"
	    OverlayFadeInDuration="00:00:02"
	    OverlayFadeOutDuration="00:00:02"
	    OverlayLayoutMode="Custom"
	    OverlayStartTime="00:00:05"
	    OverlayEndTime="00:00:10.2120000"
	    AudioOverlayFileName="%2%"
	    AudioOverlayLoop="True"
	    AudioOverlayLoopingGap="00:00:00"
	    AudioOverlayLayoutMode="Custom"
	    AudioOverlayStartTime="00:05:00"
	    AudioOverlayEndTime="00:10:00"
	    AudioOverlayGainLevel="2.2"
	    AudioOverlayFadeInDuration="00:00:00"
	    AudioOverlayFadeOutDuration="00:00:00">


###Envío de tareas con superposiciones

Una vez haya creado un archivo de valores preestablecidos, debe hacer lo siguiente:

- Cargar sus recursos
- Cargar la configuración preestablecida (Nota: el código siguiente supone que se tienen los valores preestablecidos anteriores.
- Creación de un trabajo
- Obtener una referencia al Codificador de Servicios multimedia
- Crear una tarea que especifique la colección de recursos de entrada, la configuración preestablecida, el codificador multimedia y el recurso de salida
- Enviar el archivo

El fragmento de código siguiente muestra cómo ejecutar estos pasos:

	static public void CreateOverlayJob()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);


		// Upload assets to overlay
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4); // this is the input mezzanine
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);// this will be used as a video overlay
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv); // this will be used as an audio overlay
		
		// Load the preset configuration
		string presetFileName = "OverlayPreset.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME overlay job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for overlay, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of an overlay job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
	}



>[AZURE.NOTE]Este fragmento de código carga cada recurso secuencialmente para simplificar. En entornos de producción, los recursos se cargan de forma masiva. Para obtener más información sobre la carga de varios recursos en masa, consulte [Cargar archivos con el SDK de Servicios multimedia para .NET](media-services-dotnet-upload-files.md#ingest_in_bulk).

Para ver el código de ejemplo completo, consulte [Creación de superposiciones con el Codificador de Servicios multimedia](https://code.msdn.microsoft.com/Creating-Audio-and-Video-c2942c47).

###Condiciones de error

Al editar la cadena Preset, debe asegurarse de lo siguiente:

- Para superposiciones de vídeo o imagen, el rectángulo de superposición debe ajustarse totalmente a las dimensiones del vídeo de origen
- La hora de inicio y de finalización de las superposiciones debe estar dentro de la escala de tiempo del vídeo de origen
- Si el código XML preestablecido contiene una referencia a ?OverlayFileName=”%n%”, la colección InputAssets para las tareas debe contener al menos n+1 recursos



##Unión de segmentos de vídeo

El codificador de servicios multimedia permite unir un conjunto de vídeos. Los vídeos se pueden unir juntos de un extremo a otro o se pueden especificar las partes de uno o de ambos vídeos que se van a unir. El resultado de la unión es un único recurso de salida que contiene el vídeo especificado de los recursos de entrada. Los vídeos que se van a unir pueden estar contenidos en varios recursos o en uno solo. El XML preestablecido que se pasa al codificador controla la unión. Para obtener una descripción completa del esquema preestablecido, consulte [Esquema del Codificador multimedia de Azure](https://msdn.microsoft.com/library/azure/dn584702.aspx).

###Unión con el Codificador de Servicios multimedia

La unión se controla dentro del elemento <MediaFile> tal y como se muestra en los siguientes valores preestablecidos:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	        ResizeMode="Letterbox"
	        ApplyCrop="False"
	        AudioStreamIndex="0"
	        MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Para cada vídeo que se va a unir, se agrega un elemento <Source> al elemento <Sources>. Cada elemento <Source> contiene un elemento <Clips>. Cada elemento <Clips> contiene uno o más elementos <Clip> que especifican qué parte del vídeo se unirá en el recurso de salida, al indicar una hora de inicio y de finalización. El elemento <Source> hace referencia al recurso en el que actúa. El formato de la referencia depende de si los vídeos que se van a unir están en recursos diferentes o en un solo recurso. Si desea unir un vídeo completo, omita simplemente el elemento <Clips>.

###Unión de vídeos de varios recursos

Cuando se unen vídeos de varios recursos, se usa un índice basado en cero para el atributo MediaFile del elemento <Source> a fin de identificar el recurso al que corresponde el elemento <Source>. El índice cero no se especifica, el elemento <Source> que no especifica un atributo MediaFile hace referencia al primer recurso de entrada. Todos los demás elementos <Source> deben especificar el índice basado en cero del recurso de entrada al que hace referencia mediante el uso de la sintaxis %n% donde n es el índice basado en cero del recurso de entrada. En el ejemplo anterior, el primer elemento <Source> especifica el primer recurso de entrada y el segundo elemento <Source>, el segundo recurso de entrada, y así sucesivamente. No es necesario que se haga referencia a los recursos de entrada en orden, por ejemplo:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="%1%">
	        <Clips>
	          <Clip EndTime="00:00:05" 
	                StartTime="00:00:00" />
	        </Clips>
	                  
	        </Source>
	      <Source
	       AudioStreamIndex="0">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="%2%">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Este ejemplo une partes del segundo, primer y tercer recursos de entrada. Tenga en cuenta que, puesto que cada vídeo hace referencia a un índice basado en cero, es posible unir dos vídeos que tengan el mismo nombre. Una vez haya creado un archivo de valores preestablecidos, debe hacer lo siguiente:
 
- Cargar sus recursos
- Cargar la configuración preestablecida
- Creación de un trabajo
- Obtener una referencia al Codificador de Servicios multimedia
- Crear una tarea que especifique los recursos de entrada, la configuración preestablecida, el codificador multimedia y el recurso de salida
- Enviar el archivo

El fragmento de código siguiente muestra cómo ejecutar estos pasos:
	
	static public void StitchWithMultipleAssets()
	{
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);
		
		// Upload assets to stitch
		IAsset inputAsset1 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video1.mp4);
		IAsset inputAsset2 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video2.wmv);
		IAsset inputAsset3 = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, video3.wmv);
		
		// Load the preset configuration
		string presetFileName = "StitchingWithMultipleAssets.xml";
		string configuration = File.ReadAllText(presetFileName);
		
		// Create a job
		IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
		         
		// Get a reference to the media services encoder   
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		    
		// Create a task    
		ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
		
		// Add the input assets
		task.InputAssets.Add(inputAsset1);
		task.InputAssets.Add(inputAsset2);
		task.InputAssets.Add(inputAsset3);
		
		// Add the output asset
		task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);
		
		// Submit the job
		job.Submit();
		} 


Este fragmento de código carga cada recurso secuencialmente para simplificar. En entornos de producción, los recursos se cargan de forma masiva. Para obtener más información sobre la carga de varios recursos en masa, consulte [Cargar archivos con el SDK de Servicios multimedia para .NET](media-services-dotnet-upload-files.md#ingest_in_bulk). Para ver el código de ejemplo completo, consulte [Unión con el Codificador de Servicios multimedia](https://code.msdn.microsoft.com/Stitching-with-Media-8fd5f203).

###Unión de vídeos de un solo recurso

Cuando se unen vídeos de un solo recurso, cada vídeo debe tener un nombre único. Los vídeos se especifican mediante el atributo MediaFile con el nombre de archivo como valor del atributo. Por ejemplo:
	
	<MediaFile
	    DeinterlaceMode="AutoPixelAdaptive"
	    ResizeQuality="Super"
	    AudioGainLevel="1"
	    VideoResizeMode="Stretch">
	    <Sources>
	      <Source
	        AudioStreamIndex="0"
	        MediaFile="video1.mp4">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	       AudioStreamIndex="0"
	       MediaFile="video2.wmv">
	
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	      <Source
	          AudioStreamIndex="0"
	         MediaFile="video3.wmv">
	        <Clips>
	          <Clip
	            StartTime="00:00:00"
	            EndTime="00:00:05" />
	        </Clips>
	      </Source>
	     </Sources>
	</MediaFile>

Estos valores preestablecidos unen las partes de video1.mp4, video2.wmv y video3.wmv en el recurso de salida. La creación del trabajo y las tareas es la misma que para unir vídeos de varios recursos, solo debe cargar un solo recurso, tal como se muestra en el código siguiente:

	// Creates a stitching job that uses a single asset 
    static public void StitchWithASingleAsset()
    {
        string presetFileName = "StitchingWithASingleAsset.xml";
        string configuration = File.ReadAllText(presetFileName);

        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                        MediaServicesAccountName,
                        MediaServicesAccountKey);
        // Used the cached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
        IJob job = _context.Jobs.Create("A AME stitching job, using " + presetFileName);
        IAsset asset = CreateAssetAndUploadMultipleFiles(AssetCreationOptions.None, _stitchingFiles);

        ITask task = job.Tasks.AddNew("Encode Task for stitching, using " + presetFileName, processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("Result of a stitching job, using " + presetFileName, AssetCreationOptions.None);

        job.Submit();
    }

##Codificación de presentaciones o secuencias de audio con voz principalmente
 
Al codificar un vídeo cuya pista de audio contiene voz principalmente, los valores preestablecidos del codificador predeterminado pueden provocar que se amplifique el ruido de fondo en el recurso codificado. Este comportamiento se debe a que el atributo NormalizeAudio está establecido en true.

###Codificación de presentaciones con voz principalmente.

Para evitar la amplificación del ruido de fondo, haga lo siguiente:

1. Copie el contenido del valor preestablecido del codificador que está utilizando en un archivo XML. Los valores preestablecidos del codificador se pueden encontrar en: Esquema de Azure Media Encoder
1. Elimine el atributo NormalizeAudio, que se encuentra casi al principio del archivo preestablecido bajo el elemento <MediaFile>:
	
	<MediaFile
	     DeinterlaceMode="AutoPixelAdaptive"
	     ResizeQuality="Super"
	     NormalizeAudio="True"
	     AudioGainLevel="1"
	     VideoResizeMode="Stretch">

1. Guarde el archivo preestablecido modificado en el disco duro local y utilice código como el siguiente para codificar con los valores preestablecidos personalizados:
		
		// Upload file and create asset
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, @"C:\TEMP\Original.mp4");
		 
		string inputPresetFile = @"C:\TEMP\H264 Broadband 720p NoAudioNorm.xml";
		string presetName = Path.GetFileNameWithoutExtension(inputPresetFile);
		 
		IJob job = _context.Jobs.Create("Encode Job for " + asset.Name + ", encoded using " +  presetName);
		
		Console.WriteLine("Encode Job for " + asset.Name + ", encoded using " + presetName);
		
		// Get a media processor reference, and pass to it the name of the processor to use for the specific task.
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
		Console.WriteLine("Got MP " + processor.Name + ", ID : " + processor.Id + ", version: " + processor.Version);
		 
		// Read the configuration data into a string. 
		string configuration = File.ReadAllText(inputPresetFile);
		 
		// Create a task with the encoding details, using a string preset.
		ITask task = job.Tasks.AddNew("Encode Task for " + asset.Name + ", encoded using " + presetName, processor, configuration,
		                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
		 
		// Specify the input asset to be encoded.
		task.InputAssets.Add(asset);
		 
		// Add an output asset to contain the results of the job.
		task.OutputAssets.AddNew("Output asset for " + asset.Name + ", encoded using " + presetName, AssetCreationOptions.None);
		 
		// Launch the job. 
		job.Submit();


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Otras referencias

[Esquema de Azure Media Encoder XML Schema](https://msdn.microsoft.com/library/azure/dn584702.aspx)

<!---HONumber=Nov15_HO3-->