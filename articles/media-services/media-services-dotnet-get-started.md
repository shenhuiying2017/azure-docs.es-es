---
title: "Introducción a la entrega de contenido a petición mediante .NET | Microsoft Docs"
description: "Este tutorial le guiará por los pasos necesarios para implementar una aplicación de entrega de contenido a petición con Servicios multimedia de Azure mediante la API de REST."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/11/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 24d324a724792051eb6d86026da7b41ee9ff87b1
ms.openlocfilehash: 26720340d72c31016e51cc33589388780a2f4a8a


---

# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Introducción a la entrega de contenido a petición mediante .NET SDK
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Información general
Este tutorial le guiará por los pasos necesarios para implementar una aplicación de entrega de contenido de vídeo a petición (VoD) con el SDK de Servicios multimedia (AMS) de Azure para .NET.

Presenta el flujo de trabajo básico de Servicios multimedia y la mayoría de los objetos y tareas de programación más comunes necesarios para el desarrollo de Servicios multimedia. Al término del tutorial, podrá transmitir o cargar progresivamente un archivo multimedia de ejemplo que cargó, codificó y descargó.

## <a name="what-youll-learn"></a>Temas que se abordarán
En el tutorial se muestra cómo realizar las siguientes tareas:

1. Creación de una cuenta de Servicios multimedia (con el Portal de Azure).
2. Configuración de puntos de conexión de streaming con Azure Portal.
3. Creación y configuración de un proyecto de Visual Studio.
4. Conexión a la cuenta de Servicios multimedia
5. Creación de un nuevo recurso y carga de un archivo de vídeo
6. Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa
7. Publicación del recurso y obtención de direcciones URL para streaming y descarga progresiva.
8. Prueba mediante la reproducción de contenido.

## <a name="prerequisites"></a>Requisitos previos
Los siguientes requisitos son necesarios para completar el tutorial.

* Para completar este tutorial, deberá tener una cuenta de Azure. 
  
    En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Incluso después de que se agoten los créditos, puede mantener la cuenta y usar los servicios y características gratuitos de Azure, como la característica de Aplicaciones web del Servicio de aplicaciones de Azure.
* Sistemas operativos: Windows 8 o posterior, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 o superior
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express) o versiones posteriores.

## <a name="download-sample"></a>Descarga de un ejemplo
Obtenga y ejecute un ejemplo desde [aquí](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creación de una cuenta de Azure Media Services mediante Azure Portal
Los pasos de esta sección muestran cómo crear una cuenta de AMS.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en **+Nuevo** > **Medios + CDN** > **Servicios multimedia**.
   
    ![Creación de Servicios multimedia](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. En **CREAR CUENTA DE SERVICIOS MULTIMEDIA** especifique los valores obligatorios.
   
    ![Creación de Servicios multimedia](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. En **Nombre de la cuenta**, especifique el nombre de la cuenta nueva de AMS. El nombre de la cuenta de Servicios multimedia debe estar compuesto de números o letras en minúscula, sin espacios y con una longitud de entre 3 y 24 caracteres.
   2. En Suscripción, seleccione entre las diferentes suscripciones de Azure a las que tiene acceso.
   3. En **Grupo de recursos**seleccione el recurso nuevo o uno ya existente.  Un grupo de recursos es una colección de recursos que comparten ciclos de vida, permisos y directivas. Obtenga más información [aquí](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. En **Ubicación**, seleccione la región geográfica que se usará para almacenar los registros de medios y de metadatos para la cuenta de Media Services. Esta región se utiliza para procesar y transmitir contenido multimedia. Solo las regiones de Servicios multimedia disponibles aparecen en la lista desplegable. 
   5. En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento para proporcionar almacenamiento de blobs del contenido multimedia desde la cuenta de Servicios multimedia. Puede seleccionar una cuenta de almacenamiento existente de la misma región geográfica que la cuenta de Servicios multimedia o crearla. Se crea una nueva cuenta de almacenamiento en la misma región. Las reglas para los nombres de cuenta de almacenamiento son las mismas que para las cuentas de Servicios multimedia.
      
       Puede obtener más información acerca del almacenamiento [aquí](../storage/storage-introduction.md).
   6. Seleccione **Anclar al panel** para ver el progreso de la implementación de la cuenta.
4. Haga clic en **Crear** en la parte inferior del formulario.
   
    Una vez que la cuenta se crea correctamente, el estado cambia a **En ejecución**. 
   
    ![Configuración de Servicios multimedia](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Para administrar la cuenta de AMS (por ejemplo, para cargar vídeos, codificar recursos, supervisar el progreso del trabajo) use la ventana **Configuración** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configuración de puntos de conexión de streaming con Azure Portal
Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es entregar contenido de vídeo a los clientes mediante streaming con velocidad de bits adaptable. Media Services admite las siguientes tecnologías de streaming con velocidad de bits adaptable: HTTP Live Streaming (HLS), Smooth Streaming y MPEG DASH.

Media Services proporciona empaquetado dinámico que permite entregar contenido codificado MP4 de velocidad de bits adaptable en formatos de streaming admitidos por Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) justo a tiempo sin tener que almacenar versiones previamente empaquetadas de cada uno de estos formatos.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

* Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).  
* Cree al menos una unidad de streaming para el *punto de conexión de streaming* para el que planea entregar el contenido. Para cambiar el número de unidades de streaming, realice los siguientes pasos.

Con el empaquetado dinámico, solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente.

Para crear y cambiar el número de unidades reservadas de streaming, haga lo siguiente:

1. En la ventana **Configuración**, haga clic en **Puntos de conexión de streaming**. 
2. Haga clic en el punto de conexión de streaming predeterminado. 
   
    Aparecerá la ventana de **DETALLES DEL PUNTO DE CONEXIÓN DE STREAMING PREDETERMINADO** .
3. Para especificar el número de unidades de streaming, mueva el control deslizante **Unidades de streaming** .
   
    ![Unidades de streaming](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Haga clic en el botón **Guardar** para guardar los cambios.
   
   > [!NOTE]
   > La asignación de cualquier nueva unidad puede tardar hasta 20 minutos en completarse.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Creación y configuración de un proyecto de Visual Studio

1. Cree una nueva Aplicación de consola de C# en Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **Nombre**, la **Ubicación** y el **Nombre de la solución** y, a continuación, haga clic en **Aceptar**.
2. Use el paquete NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) para instalar **Extensiones del SDK de Azure Media Services para .NET**.  Las extensiones del SDK .NET de Servicios multimedia son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Servicios multimedia. Al instalar este paquete, también se instala el **SDK de Servicios multimedia para .NET** y agrega todas las demás dependencias necesarias.

    Para agregar referencias mediante NuGet, haga lo siguiente: en el Explorador de soluciones, haga clic en el botón secundario del mouse en el nombre del proyecto, seleccione **Administrar paquetes NuGet**. A continuación, busque **windowsazure.mediaservices.extensions** y haga clic en **Instalar**.

3. Agregue una referencia al ensamblado System.Configuration. Este ensamblado contiene la clase **System.Configuration.ConfigurationManager** que se usa para tener acceso a los archivos de configuración (por ejemplo, App.config).

    Para agregar una referencia, haga lo siguiente: en el Explorador de soluciones, haga clic en el botón secundario del mouse en el nombre del proyecto, seleccione **Agregar** > **Referencia...** y escriba la configuración en el cuadro de búsqueda. 

4. Abra el archivo App.config (agregue el archivo al proyecto si no se ha agregado de forma predeterminada) y agregue una sección *appSettings* al archivo. Establezca los valores de la clave de nombre y la cuenta de cuenta de Servicios multimedia de Azure, tal como se muestra en el ejemplo siguiente. Para obtener el nombre de la cuenta y la información de la clave, vaya a [Azure Portal](https://portal.azure.com/) y seleccione la cuenta de Azure Media Services. A continuación, seleccione **Configuración** > **Claves**. Aparece la ventana Administrar claves que muestra el nombre de la cuenta y la clave principal y la secundaria. Copie los valores del nombre de la cuenta y la clave principal.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Sobrescriba las instrucciones **using** existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Cree una carpeta nueva en el directorio de proyectos y copie el archivo .mp4 o .wmv que desea codificar y transmítalo o descárguelo progresivamente. En este ejemplo, se usa la ruta de acceso "C:\VideoFiles".

## <a name="connect-to-the-media-services-account"></a>Conexión a la cuenta de Servicios multimedia

Cuando se usa Servicios multimedia con .NET, debe usar la clase **CloudMediaContext** para la mayoría de las tareas de programación de Servicios multimedia: conexión a la cuenta de Servicios multimedia; creación, actualización, acceso y eliminación de los siguientes objetos: activos, archivos de activos, trabajos, directivas de acceso, localizadores, etc.

Sobrescriba la clase de programa predeterminada con el código siguiente. El código muestra cómo leer los valores de conexión del archivo App.config y cómo crear el objeto **CloudMediaContext** para conectarse a Servicios multimedia. Para obtener más información sobre cómo conectarse a Servicios multimedia, consulte [Conexión a Servicios multimedia con el SDK de Servicios multimedia para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).


La función **Main** llama a métodos que se definirán más adelante en esta sección.

> [!NOTE]
> Recibirá errores de compilación hasta que agregue las definiciones para todas las funciones.

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
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Creación de un nuevo recurso y carga de un archivo de vídeo

En Servicios multimedia, cargará (o introducirá) los archivos digitales en un recurso. La entidad **Recurso** puede contener archivos de vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos (y los metadatos acerca de estos archivos).  Una vez cargados los archivos, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming. Los archivos del recurso se denominan **archivos de recursos**.

El método **UploadFile** definido a continuación llama a **CreateFromFile** (definido en las extensiones del SDK para .NET). **CreateFromFile** crea un nuevo recurso en el que se carga el archivo de origen especificado.

El método **CreateFromFile** toma **AssetCreationOptions**, que permite especificar una de las siguientes opciones de creación de recursos:

* **Ninguno** : no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al usar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento.
  Si tiene previsto entregar un MP4 mediante una descarga progresiva, utilice esta opción.
* **StorageEncrypted** : use esta opción para cifrar el contenido no cifrado de manera local mediante el cifrado Estándar de cifrado avanzado (AES) de 256 bits y luego cargarlo en el almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.
* **CommonEncryptionProtected** : use esta opción si va a cargar contenido que ya se cifró y protegió con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).
* **EnvelopeEncryptionProtected** : use esta opción si va a cargar HLS cifrado con AES. Tenga en cuenta que los archivos deben haberse codificado y cifrado con Transform Manager.

El método **CreateFromFile** también permite especificar una devolución de llamada para notificar el progreso de carga del archivo.

En el siguiente ejemplo especificamos **Ninguno** para las opciones del activo.

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


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Codificación del archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa
Después de introducir los recursos en Servicios multimedia, los elementos multimedia se pueden codificar, transmultiplexar, agregar una marca de agua, entre otras opciones, antes de entregarse a los clientes. Estas actividades se programan y se ejecutan en varias instancias de rol en segundo plano para garantizar la disponibilidad y alto rendimiento. Estas actividades se denominan trabajos y cada trabajo está compuesto de tareas atómicas que realizan el trabajo real en el archivo del recurso.

Como se ha indicado antes, cuando se trabaja con Servicios multimedia de Azure, uno de los escenarios más comunes es ofrecer streaming con velocidad de bits adaptable a los clientes. Media Services puede empaquetar de manera dinámica un conjunto de archivos MP4 de velocidad de bits adaptable en uno de los siguientes formatos: HTTP Live Streaming (HLS), Smooth Streaming y MPEG DASH.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

* Codificar o transcodificar el archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o de Smooth Streaming de velocidad de bits adaptable.  
* Obtener al menos una unidad de streaming para el extremo de streaming para el que planea entregar el contenido.

El código siguiente muestra cómo enviar un trabajo de codificación. El trabajo contiene una tarea que especifica la transcodificación del archivo intermedio en un conjunto de MP4 de velocidades de bits adaptables con el **Estándar de Codificador multimedia**. El código envía el trabajo y espera hasta que se complete.

Una vez completado el trabajo, debería poder transmitir el recurso o descargar progresivamente archivos MP4 creados como resultado de la transcodificación.
Tenga en cuenta que no es necesario obtener más de 0 unidades de streaming para descargar progresivamente archivos MP4.

Agregue el método siguiente a la clase Program

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
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

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicación del recurso y obtención de direcciones URL para streaming y descarga progresiva

Para transmitir o descargar un recurso, necesita "publicarlo" mediante la creación de un localizador. Los localizadores proporcionan acceso a los archivos contenidos en el recurso. Media Services admite dos tipos de localizadores: OnDemandOrigin, que se usan para transmitir contenido multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) y localizadores de firma de acceso (SAS), que se usan para descargar archivos multimedia (para más información sobre los localizadores SAS, lea [este](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) blog).

Tras crear los localizadores, puede generar las direcciones URL que se utilizan para transmitir o descargar los archivos.

Una dirección URL de streaming de Smooth Streaming tiene el formato siguiente:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Una dirección URL de streaming de HLS tiene el formato siguiente:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Una dirección URL de streaming de MPEG DASH tiene el formato siguiente:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Una dirección URL de SAS utilizada para descargar archivos tiene el formato siguiente:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Las extensiones de SDK de Servicios multimedia para .NET ofrecen útiles métodos auxiliares que devuelven direcciones URL con formato para el recurso publicado.

El código siguiente usa extensiones del SDK de .NET para crear localizadores y obtener las direcciones URL de streaming y descarga progresiva. El código también muestra cómo descargar los archivos en una carpeta local.

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

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
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

## <a name="test-by-playing-your-content"></a>Prueba mediante la reproducción de contenido
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


Para transmitir vídeo, use [Reproductor de Servicios multimedia de Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para probar la descarga progresiva, pegue una dirección URL en un explorador (por ejemplo, Internet Explorer, Chrome o Safari).

## <a name="next-steps-media-services-learning-paths"></a>Siguientes pasos: Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>¿Busca alguna otra cosa?
Si este tema no contiene lo que esperaba, falta algo o no satisface de alguna forma sus necesidades, háganos llegar sus comentarios mediante el subproceso de Disqus siguiente.

<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Dec16_HO2-->


