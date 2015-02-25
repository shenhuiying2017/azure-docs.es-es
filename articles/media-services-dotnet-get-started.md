<properties pageTitle="Introducción al SDK de Servicios multimedia para .NET de Azure" description="Este tutorial le guiará por los pasos necesarios para implementar una aplicación de entrega de contenido de vídeo a petición (VoD) con Servicios multimedia de Azure mediante. NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/12/2015" ms.author="juliako"/>


# Entrega de vídeo a petición con el SDK de Servicios multimedia para .NET 

[AZURE.INCLUDE [media-services-selector-get-started](../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Prueba gratuita de Azure</a>.

Este tutorial le guiará por los pasos necesarios para implementar una aplicación de entrega de contenido de vídeo a petición (VoD) con el SDK de Servicios multimedia (AMS) de Azure para .NET. 

Presenta el flujo de trabajo básico de los Servicios multimedia y la mayoría de los objetos y tareas de programación más comunes necesarios para el desarrollo de los Servicios multimedia. Al término del tutorial, podrá transmitir o cargar progresivamente un archivo multimedia de ejemplo que cargó, codificó y descargó.  

Para implementar una aplicación de entrega de contenido VoD, puede usar distintas tecnologías (por ejemplo, .NET, REST o Java) o herramientas (Portal de administración de Azure o el Explorador de Servicios multimedia de Azure) o una combinación de dos. 

Este tutorial utiliza el Portal de administración de Azure y SDK de Servicios multimedia para .NET para lograr las tareas siguientes:     


1.  [Creación de una cuenta de Servicios multimedia con el Portal](#create_ams).
2.  [Configuración de unidades de streaming con el Portal](#configure_streaming_units).
3.  [Creación y configuración de un proyecto de Visual Studio](#configure_VS)
4.  [Uso de .NET para cargar, codificar y entregar contenido](#use_dotnet)
	5.  [Conexión a la cuenta de Servicios multimedia](#connect).
	1.  [Creación de un nuevo recurso y carga de un archivo de video](#upload).
	1.  [Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa](#encode).
	1.  [Configuración de la directiva de entrega para el recurso codificado](#configure_delivery_method).
	2.  [Opcionalmente, configure la protección de contenido dinámica](#configure_content_protection). 
	1.  [Publicación del recurso y obtención de direcciones URL de descarga progresiva y de streaming](#publish_get_urls). 
1.  [Reproducción del contenido](#play). 

## Requisitos previos
Los siguientes requisitos previos son necesarios para iniciar el desarrollo con el SDK de Servicios multimedia para .NET.

- Sistemas operativos: Windows 7, Windows 2008 R2, Windows 8 o posterior.
- .NET Framework 4.5 o .NET Framework 4.0
- Visual Studio 2013, Visual Studio 2012, Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).


## <a id="create_ams"></a>Creación de una cuenta de Servicios multimedia con el Portal

1. En el [Portal de administración][], haga clic en **Nuevo**, después en **Servicios multimedia** y, finalmente, en **Creación rápida**.
   
	![Creación rápida de Servicios multimedia](./media/media-services-create-account/wams-QuickCreate.png)

2. En **NOMBRE**, especifique el nombre de la cuenta nueva. El nombre de cuenta de Servicios multimedia debe estar compuesto de números o letras en minúscula, sin espacios y con una longitud de entre 3 y 24 caracteres. 

3. En **REGIÓN**, seleccione la región geográfica que se usará para almacenar los registros de metadatos para la cuenta de Servicios multimedia. Solo las regiones de Servicios multimedia disponibles aparecen en el cuadro desplegable. 

4. En **CUENTA DE ALMACENAMIENTO**, seleccione una cuenta de almacenamiento para proporcionar almacenamiento de blobs del contenido multimedia desde la cuenta de Servicios multimedia. Puede seleccionar una cuenta de almacenamiento existente en la misma región geográfica que la cuenta de Servicios multimedia o crear una nueva cuenta de almacenamiento. Se crea una nueva cuenta de almacenamiento en la misma región. 

5. Si ha creado una nueva cuenta de almacenamiento, en **NOMBRE DE NUEVA CUENTA DE ALMACENAMIENTO**, especifique un nombre para la cuenta de almacenamiento. Las reglas para los nombres de cuenta de almacenamiento son las mismas que para las cuentas de Servicios multimedia.

6. Haga clic en **Creación rápida** en la parte inferior del formulario.

	Puede supervisar el estado del proceso en el área de mensajes situada en parte inferior de la ventana.

	Una vez que la cuenta se crea correctamente, el estado cambia a Activo. 
	
	En la parte inferior de la página, se muestra el botón **ADMINISTRAR CLAVES**. Al hacer clic en este botón, se muestra un cuadro de diálogo con el nombre de cuenta de Servicios multimedia y las claves principales y secundarias. Necesitará el nombre de cuenta y la información de la clave principal para obtener acceso mediante programación a la cuenta de Servicios multimedia. 

	
	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	Cuando haga doble clic en el nombre de cuenta, se mostrará la página de inicio rápido de forma predeterminada. La página le permite realizar algunas tareas de administración que también están disponibles en otras páginas del portal. Por ejemplo, puede cargar un archivo de vídeo desde esta página o hacerlo desde la página CONTENT.

	 
## <a id="configure_streaming_units"></a>Configuración de unidades de streaming con el Portal

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es ofrecer streaming de velocidad de bits adaptable a los clientes. Con el streaming de velocidad de bits adaptable, el cliente puede cambiar a una secuencia de velocidad de bits mayor o menor que el vídeo mostrado, según el ancho de banda actual de la red, el uso de CPU y otros factores. Servicios multimedia admite el streaming de velocidad de bits adaptable siguiente: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access). 

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming. 

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- codificar o transcodificar el archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o de Smooth Streaming de velocidad de bits adaptable (más adelante en este tutorial se muestran los pasos de codificación),  
- obtener al menos una unidad de streaming a petición para el extremo de streaming para el que piensa entregar el contenido.

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente. 

Tenga en cuenta que además de poder usar las capacidades de empaquetado dinámico, las unidades reservadas de streaming a petición con capacidad de salida dedicada pueden adquirirse en incrementos de 200 Mbps. De manera predeterminada, el streaming a petición está configurado en un modelo de instancias compartidas para el que se comparten recursos de servidor (por ejemplo, proceso, capacidad de salida, entre otros) con los demás usuarios. Para mejorar el resultado del streaming a petición, se recomienda adquirir unidades reservadas de streaming a petición.


Para cambiar el número de dichas unidades, haga lo siguiente:

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página EXTREMOS DE STREAMING. Luego, haga clic en el extremo de streaming que desea modificar.

3. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Presione el botón SAVE para guardar los cambios.

	La asignación de cualquier nueva unidad de streaming a petición puede tardar unos 20 minutos en finalizarse. 

	 
	>[AZURE.NOTE] Actualmente, pasar de cualquier valor positivo de unidades de streaming a petición a ninguno puede deshabilitar el streaming a petición hasta una hora.
	>
	> Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).



## <a id="configure_VS"></a>Creación y configuración de un proyecto de Visual Studio

1. Cree una nueva Aplicación de consola de C# en Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación** y el **nombre de la solución** y, a continuación, haga clic en Aceptar. 

2. Utilice el paquete de Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **Extensiones del SDK .NET de Servicios multimedia de Azure**.  Las extensiones del SDK .NET de Servicios multimedia son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Servicios multimedia. Al instalar este paquete, también se instala el **SDK de Servicios multimedia de .NET** y agrega todas las demás dependencias necesarias.
 
3. Agregue una referencia al ensamblado System.Configuration. Este ensamblado contiene la clase System.Configuration.ConfigurationManager que se utiliza para tener acceso a los archivos de configuración (por ejemplo, App.config). 

4. Abra el archivo App.config (agregue el archivo al proyecto si no se ha agregado de forma predeterminada) y agregue una sección *appSettings* al archivo. Establezca los valores de la clave de nombre y la cuenta de cuenta de Servicios multimedia de Azure, tal como se muestra en el ejemplo siguiente. Para obtener el nombre de cuenta y la información de la clave, abra el Portal de administración de Azure, seleccione su cuenta de Servicios multimedia y haga clic en el botón **ADMINISTRAR CLAVES**.


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Sobrescriba los valores existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. Cree una carpeta nueva en el directorio de proyectos y copie el archivo .mp4 o .wmv que desea codificar y transmítalo o descárguelo progresivamente. En este ejemplo, la ruta de acceso es "C:\VideoFiles". 

## <a id="use_dotnet"></a>Uso de .NET para cargar, codificar y entregar contenido 

En el código de esta sección se muestra cómo realizar las tareas siguientes:

1. Conexión a la cuenta de Servicios multimedia
1. Creación de un nuevo recurso y carga de un archivo de video
1. Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa
1. Configuración de la directiva de entrega para el recurso codificado
2. Opcionalmente, configuración de la protección de contenido dinámica
1. Publicación del recurso y obtención de las direcciones URL 


### <a id="connect"></a>Conexión a la cuenta de Servicios multimedia

Cuando se utilizan Servicios multimedia con .NET, debe utilizar la clase **CloudMediaContext** para la mayoría de las tareas de programación de Servicios multimedia: conexión a la cuenta de Servicios multimedia; creación, actualización, obtención de acceso y eliminación de los siguientes objetos: recursos, archivos de recursos, trabajos, directivas de acceso, localizadores, entre otras. 
 
Sobrescriba la clase de programa predeterminada con el código siguiente. El código muestra cómo leer los valores de conexión del archivo App.config y cómo crear el objeto CloudMediaContext para conectarse a Servicios multimedia. Para obtener más información sobre cómo conectarse a Servicios multimedia, consulte [Conexión a Servicios multimedia con el SDK de Servicios multimedia para .NET](http://msdn.microsoft.com/es-es/library/azure/jj129571.aspx).

La función **Main** llama a métodos que se definirán más adelante en esta sección.
	
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
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new 
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

### <a id="upload"></a>Creación de un nuevo recurso y carga de un archivo de video

En Servicios multimedia, cargará (o introducirá) los archivos digitales en un recurso. La entidad **Recurso** puede contener archivos de vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos (y los metadatos acerca de estos archivos).  Una vez cargados los archivos, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming. Los archivos del recurso se denominan **archivos de recursos**.

El método **UploadFile** definido a continuación llama a **CreateFromFile** (definido en las extensiones de SDK de .NET). **CreateFromFile** crea un nuevo recurso en el que se carga el archivo de origen especificado. 

El método **CreateFromFile** toma **AssetCreationOptions**, que le permite especificar una de las siguientes opciones de creación de recursos:
 
- **Ninguno**: no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento.
Si tiene previsto entregar un MP4 mediante una descarga progresiva, utilice esta opción. 
- **StorageEncrypted**: cifra el contenido no cifrado localmente mediante el cifrado AES de 256 bits y, a continuación, lo carga en el almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.
- **CommonEncryption**: utilice esta opción si va a cargar contenido que ya se ha cifrado y protegido con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).
- **EnvelopeEncrypted**: utilice esta opción si va a cargar HLS cifrado con AES. Tenga en cuenta que los archivos deben haberse codificado y cifrado con Transform Manager.

El método **CreateFromFile** también permite especificar una devolución de llamada para notificar el progreso de carga del archivo.

En el siguiente ejemplo especificamos **Ninguno** para las opciones del recurso.

Agregue el método siguiente a la clase Program

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}


### <a id="encode"></a>Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa

Después de introducir los recursos en Servicios multimedia, los elementos multimedia se pueden codificar, transmultiplexar, agregar una marca de agua, entre otras opciones, antes de entregarse a los clientes. Estas actividades se programan y se ejecutan en varias instancias de rol en segundo plano para garantizar la disponibilidad y alto rendimiento. Estas actividades se denominan trabajos y cada trabajo está compuesto de tareas atómicas que realizan el trabajo real en el archivo del recurso. 

Como se ha indicado antes, cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es ofrecer streaming de velocidad de bits adaptable a los clientes. Servicios multimedia pueden empaquetar de manera dinámica un conjunto de archivos MP4 de velocidad de bits adaptable en uno de los siguientes formatos: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access). 

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

- codificar o transcodificar el archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o de Smooth Streaming de velocidad de bits adaptable,  
- obtener al menos una unidad de streaming a petición para el extremo de streaming para el que piensa entregar el contenido. 

El código siguiente muestra cómo enviar un trabajo de codificación. El trabajo contiene una tarea que especifica la transcodificación del archivo intermedio en un conjunto de MP4 de velocidades de bits adaptables con el **Codificador de Servicios multimedia**. El código envía el trabajo y espera hasta que se complete. 

Una vez completado el trabajo, debería poder transmitir el recurso o descargar progresivamente archivos MP4 creados como resultado de la transcodificación.
Tenga en cuenta que no es necesario obtener unidades de streaming a petición para descargar progresivamente archivos MP4. 


Agregue el método siguiente a la clase Program

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // Submit the job and wait until it is completed.
	    job.Submit();
	
	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;
	
	    Console.WriteLine("Transcoding job finished.");
	
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
	    return outputAsset;
	}


### <a id="configure_content_protection"></a>Opcionalmente, configure la protección de contenido dinámica

Para obtener información sobre cómo configurar la protección de contenido, consulte los siguientes artículos:

- [Uso de cifrado dinámico de AES-128 y el servicio de entrega de claves](http://msdn.microsoft.com/es-es/library/azure/dn783457.aspx)
- [Uso del cifrado dinámico de PlayReady y servicio de entrega de licencias](http://msdn.microsoft.com/es-es/library/azure/dn783467.aspx)
- [Entrega de contenido cifrado de almacenamiento](http://msdn.microsoft.com/es-es/library/azure/dn783451.aspx)

### <a id="configure_delivery_method"></a>Configuración de la directiva de entrega para el recurso codificado

Uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar las directivas de entrega de recursos. Algunos aspectos que incluye la configuración de la directiva de entrega de recursos son los siguientes: ¿qué protocolos pueden utilizarse para entregar el recurso (por ejemplo, MPEG DASH, HLS, HDS, Smooth Streaming o todos), si se desea cifrar dinámicamente el recurso y cómo (en sobre o cifrado común). 

El siguiente método **ConfigureClearAssetDeliveryPolicy** especifica no aplicar el cifrado dinámico y entregar la transmisión en cualquiera de los siguientes protocolos:  MPEG DASH, HLS y Smooth Streaming. 
  
Agregue el método siguiente a la clase Program

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

Como resultado de esta configuración de entrega, podrá solicitar una secuencia de transmisión suave, HLS o MPEG DASH con los formatos siguientes:

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 


### <a id="publish_get_urls"></a>Publicación del recurso y obtención de direcciones URL de descarga progresiva y de streaming

Para transmitir o descargar un recurso, necesita "publicarlo" mediante la creación de un localizador. Los localizadores proporcionan acceso a los archivos contenidos en el recurso. Servicios multimedia admite dos tipos de localizadores: Localizadores OnDemandOrigin, utilizados para transmitir contenido multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) y localizadores de firma de acceso (SAS), que se usan para descargar archivos multimedia.

Una vez que se crean los localizadores, puede generar las direcciones URL que se utilizan para transmitir o descargar los archivos. 


Una dirección URL a petición de Smooth Streaming tiene el formato siguiente:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Una dirección URL a petición de HLS tiene el formato siguiente:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Una dirección URL a petición de MPEG DASH tiene el formato siguiente:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Una dirección URL de SAS utilizada para descargar archivos tiene el formato siguiente:

	{blob container name}/{asset name}/{file name}/{SAS signature}

Las extensiones de SDK de Servicios multimedia de .NET ofrecen útiles métodos auxiliares que devuelven direcciones URL con formato para el recurso publicado.
 
El código siguiente usa extensiones del SDK de .NET para crear localizadores, obtener las direcciones URL de streaming y descarga progresiva. El código también muestra cómo descargar los archivos en una carpeta local.

Agregue el método siguiente a la clase Program

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming, 
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming, 
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get progressive download URLs for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the progressive download URLs.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a id="play"></a>Reproducción del contenido  

Una vez ejecutado el programa definido en la sección anterior, se mostrarán las direcciones URL similares a la siguiente en la ventana de consola.

Direcciones URL de streaming adaptable:

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Direcciones URL de descarga progresiva (audio y vídeo).    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Para probar la transmisión por secuencias suave, utilice [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) o [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor).

Para probar MPEG DASH, utilice [http://dashif.org](http://dashif.org/reference/players/javascript/).

Para probar HLS, utilice dispositivos iOS o Safari, o [3ivx-hls-player](http://apps.microsoft.com/windows/es-es/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614). 


Para probar la descarga progresiva, pegue una dirección URL en un explorador (por ejemplo, Internet Explorer, Chrome o Safari).


<h2>Recursos adicionales</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Servicios multimedia de Azure 101 - ¡Obtenga el vídeo en línea ahora!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Servicios multimedia de Azure 102 - Empaquetado dinámico y dispositivos móviles</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Instalador de plataforma web]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal de administración]: http://manage.windowsazure.com/




<h2>Recursos adicionales</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Servicios multimedia de Azure 101 - ¡Obtenga el vídeo en línea ahora!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Servicios multimedia de Azure 102 - Empaquetado dinámico y dispositivos móviles</a>

<!--HONumber=42-->
