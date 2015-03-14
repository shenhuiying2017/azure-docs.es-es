<properties 
	pageTitle="Uso de Servicios multimedia (Java): guía de características de Azure" 
	description="Describe cómo usar Servicios multimedia de Azure para realizar tareas comunes como codificación, cifrado y recursos de streaming." 
	services="media-services" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="robmcm"/>

# Uso de Servicios multimedia

En esta guía se muestra cómo empezar a programar con Servicios multimedia de Azure usando Java. La guía incluye una introducción técnica de Servicios multimedia, pasos para configurar la cuenta de Azure para Servicios multimedia y código que muestra cómo llevar a cabo las tareas de programación típicas. 

## Tabla de contenido

-   [¿Qué es Servicios multimedia?](#media_services)
-   [Configuración de una cuenta de Azure para Servicios multimedia](#setup-account)
-   [Configuración del desarrollo de Servicios multimedia](#setup-dev)
-   [ Uso de Servicios multimedia con Java](#connect)
-   [Recursos adicionales](#additional-resources)

 
## <a id="media_services"></a>¿Qué es Servicios multimedia?

Los Servicios multimedia de Azure forman una plataforma multimedia extensible que integra lo mejor de la plataforma multimedia de Microsoft y los componentes multimedia de terceros en Azure. Los Servicios multimedia proporcionan un proceso en la nube que permite a los socios del sector ampliar o reemplazar las tecnologías de componentes. Los vendedores independientes de software y los proveedores multimedia pueden usar Servicios multimedia para generar soluciones multimedia de un extremo a otro. Esta introducción describe la arquitectura general y los escenarios de desarrollo comunes para Servicios multimedia.

El siguiente diagrama muestra la arquitectura de Servicios multimedia.

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

### Compatibilidad de las características de Servicios multimedia
La versión actual de Servicios multimedia ofrece el siguiente conjunto de características para el desarrollo de aplicaciones multimedia en la nube. 

- **Introducción**. Las operaciones de introducción introducen los recursos en el sistema, por ejemplo mediante su carga y cifrado antes de que se coloquen en el servicio de almacenamiento de Azure. En la versión RTM, Servicios multimedia ofrecerá integración con los componentes de socios para proporcionar soluciones de carga de UDP (protocolo de datagramas de usuario).
- **Codificación**. Las operaciones de codificación incluyen la codificación, transformación y conversión de recursos multimedia. Puede ejecutar tareas de codificación en la nube usando el Codificador multimedia incluido en Servicios multimedia. Las opciones de codificación incluyen las siguientes:
   - Use el codificador multimedia de Azure y trabaje con una amplia variedad de códecs y formatos estándar, incluidos IIS Smooth Streaming líder del sector, MP4 y la conversión a Apple HTTP Live Streaming.
   - Convierta bibliotecas enteras o archivos individuales con un control total sobre la entrada y la salida.
   - Un conjunto grande de tipos de archivo, formatos y códecs compatibles (consulte [Tipos de archivo compatibles para Servicios multimedia][]).
   - Conversiones de formato compatibles. Los Servicios multimedia le permiten convertir ISO MP4 (.mp4) al formato de archivo de Smooth Streaming (PIFF 1.3) (.ismv; .isma). También puede convertir el formato de archivo de Smooth Streaming (PIFF) a Apple HTTP Live Streaming (.msu8, .ts).
- **Protección**. La protección de contenido implica el cifrado de la transmisión en directo o contenido bajo demanda de cara a la seguridad en la entrega, el almacenamiento y el transporte. Los Servicios multimedia ofrecen una solución independiente de la tecnología DRM para proteger el contenido.  Las tecnologías DRM compatibles actualmente son la protección Microsoft PlayReady y cifrado común de MPEG. También habrá compatibilidad con tecnologías DRM adicionales. 
- **Transmisión**. La transmisión de contenido implica el envío de este en directo o bajo demanda a los clientes, o puede recuperar o descargar archivos multimedia específicos desde la nube. Los Servicios multimedia ofrecen una solución independiente del formato para el contenido por secuencias.  Los Servicios multimedia proporcionan  compatibilidad con el origen de streaming en formatos MP4, Smooth Streaming y Apple HTTP Live Streaming. Se ofrecerá compatibilidad con formatos adicionales. También puede entregar directamente contenido de streaming usando un CDN de Azure o un CDN de terceros, que permita la opción  de realizar una escalación a millones de usuarios.   

### Escenarios de desarrollo de Servicios multimedia
Los Servicios multimedia son compatibles con escenarios de desarrollo multimedia comunes como se describe en la siguiente tabla. 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Escenario</th>
       <th>Descripción</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>Creación de flujos de trabajo de un extremo a otro</td>
        <td>Genere flujos de trabajo multimedia completos en toda la nube. Desde la carga de medios a la distribución de contenido, los Servicios multimedia ofrecen una serie de componentes que pueden combinarse para administrar flujos de trabajo específicos de aplicaciones. Entre las capacidades actuales se encuentran la carga, el almacenamiento, el cifrado, la conversión de formato, la protección de contenido y la entrega de transmisión bajo demanda.</td>
    </tr>
    <tr>
        <td>Creación de flujos de trabajo híbridos</td>
        <td>Puede integrar los Servicios multimedia con procesos y herramientas existentes. Por ejemplo, codifique el contenido in situ y, a continuación, cárguelo en los Servicios multimedia para realizar la transcodificación en varios formatos y entregue a través de CDN de Azure o un CDN de otro proveedor. Los Servicios multimedia pueden llamarse individualmente a través de las API de REST estándar para la integración con servicios y aplicaciones externas.</td>
    </tr>
    <tr>
        <td>Ofrecimiento de compatibilidad en la nube para reproductores multimedia</td>
        <td>Puede crear, administrar y entregar contenido multimedia en varios dispositivos multimedia (incluidos los dispositivos iOS, Android y Windows) y distintas plataformas.</td>
    </tr>
  </tbody>
</table>

### Desarrollo de clientes de Servicios multimedia
Amplíe el alcance de la solución de Servicios multimedia mediante los SDK y los marcos de reproductores para generar aplicaciones clientes multimedia. Estos clientes son para los desarrolladores que deseen generar aplicaciones de Servicios multimedia que ofrezcan experiencias de usuario convincentes en una gama de dispositivos y plataformas. Según los dispositivos para los que desea crear aplicaciones cliente, existen opciones para los SDK y marcos de reproductores disponibles en Microsoft y otros socios.  

A continuación se muestra una lista de SDK de clientes y marcos de reproductores compatibles.  Para obtener más información sobre estos y otros marcos de reproductores y SDK planificados, y la funcionalidad que pueden admitir, consulte [Desarrollo de clientes de los Servicios multimedia]. 

#### Compatibilidad con clientes de PC y Mac  
Para los PC y Mac, puede dirigir una experiencia de transferencia mediante Microsoft Silverlight o Adobe Open Source Media Framework.

-	[Cliente de Smooth Streaming para Silverlight](http://www.iis.net/download/smoothclient)
-	[Plataforma multimedia de Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)
-	[Complemento Smooth Streaming para OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022) Para obtener información sobre el uso de este complemento, consulte [Uso del complemento de transmisión suave para Adobe Open Source Media Framework](http://go.microsoft.com/fwlink/?LinkId=275034).

#### Aplicaciones de Windows 8
Para Windows 8, puede generar aplicaciones de la Tienda Windows mediante una de las construcciones y lenguajes de desarrollo compatibles como HTML, Javascript, XAML, C# y C+.

-	[SDK del cliente de Smooth Streaming para Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146). Para obtener más información sobre cómo crear una aplicación de la Tienda Windows con este SDK, consulte [Generación de una aplicación de la Tienda Windows de transmisión suave](http://go.microsoft.com/fwlink/?LinkId=271647). Para obtener información sobre cómo crear un reproductor de transmisión suave en HTML5, consulte [Tutorial: Generación del primer reproductor de transmisión suave de HTML5](http://msdn.microsoft.com/library/jj573656.aspx).

-	[Plataforma multimedia de Microsoft: Player Framework for Windows 8 Windows Store Applications](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

#### Xbox
Xbox es compatible con aplicaciones de Xbox LIVE que consumen contenido de transmisión suave. El kit de desarrollo de aplicaciones (ADK) para Xbox LIVE incluye:

-	Cliente de transmisión suave para ADK de Xbox Live
-	Plataforma multimedia de Microsoft: Player Framework para ADK de Xbox LIVE

#### Dispositivos dedicados o insertados
Dispositivos como TV, decodificadores (set-top box), reproductores Blu-Ray, soluciones de TV OTT y dispositivos móviles conectados que tengan un marco de desarrollo de aplicaciones personalizado y un proceso multimedia personalizado. Microsoft proporciona los siguientes kits de migración que pueden ser objeto de licencia, y permite a los socios migrar la reproducción de transmisión suave para la plataforma.

-	[Kit de portabilidad de cliente de Smooth Streaming](http://www.microsoft.com/es-es/mediaplatform/sspk.aspx)
-	[Kit de portabilidad de dispositivos de Microsoft PlayReady](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

#### Windows Phone
Microsoft proporciona un SDK que puede usarse para crear aplicaciones de vídeo premium para Windows Phone. 

-	[Cliente de Smooth Streaming para Silverlight](http://www.iis.net/download/smoothclient)
-	[Plataforma multimedia de Microsoft: Player Framework para Silverlight](http://smf.codeplex.com/documentation)

#### Dispositivos iOS
Para los dispositivos iOS, incluidos iPhone, iPod, e iPad, Microsoft incorpora un SDK que puede usar para generar aplicaciones para estas plataformas y ofrecer contenido de vídeo premium: SDK de transmisión suave para dispositivos iOS con PlayReady.  El SDK está disponible solo para los licenciatarios. Para obtener más información, [escriba un correo electrónico a Microsoft](mailto:askdrm@microsoft.com). Para obtener información sobre el desarrollo de iOS, consulte [iOS Developer Center](https://developer.apple.com/devcenter/ios/index.action).

#### Dispositivos Android
Varios socios de Microsoft incluyen SDK para la plataforma Android que agrega la capacidad de reproducir la transmisión suave en un dispositivo Android. [Escriba un correo electrónico a Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices) para obtener más información sobre los asociados.


## <a id="setup-account"></a>Configuración de una cuenta de Azure para Servicios multimedia

Para configurar su cuenta de Servicios multimedia, use el Portal de administración de Azure. Consulte el tema [Creación de una cuenta de Servicios multimedia][]. Después de crear la cuenta en el Portal de administración, está listo para configurar el equipo para el desarrollo de Servicios multimedia. 

## <a id="setup-dev"></a>Configuración del desarrollo de Servicios multimedia

Esta sección contiene los requisitos previos generales para el desarrollo de Servicios multimedia usando el SDK de Servicios multimedia para Java.

### Requisitos previos

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Consulte el tema [Creación de una cuenta de Servicios multimedia][].
-   Las bibliotecas de Azure para Java, que puede instalar desde el [Centro para desarrolladores de Java para Azure][].

## <a if="connect"></a>  Uso de Servicios multimedia con Java

El siguiente código muestra cómo crear un recurso, cargar un archivo en el recurso, ejecutar un trabajo con una tarea para transformar el recurso y descargar los archivos de salida del recurso transformado.

Necesitará configurar una cuenta de servicios multimedia antes de usar este código. Para obtener información sobre cómo configurar una cuenta, consulte [Creación de una cuenta de Servicios multimedia](http://azure.microsoft.com/manage/services/media-services/how-to-create-a-media-services-account/).

Sustituya los valores por las variables `clientId` y `clientSecret`. El código también se basa en un archivo almacenado localmente, `c:/media/MPEG4-H264.mp4`. Necesitará proporcionar su propio archivo para usar. El código también requiere una carpeta de salida, `c:/output`, que es dónde se descargarán los archivos de salida.

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

Los recursos que cree se guardan en el almacenamiento de Azure. Sin embargo, use solamente las API de servicios multimedia de Azure (no las API de almacenamiento de Azure) para agregar, actualizar o eliminar recursos.

### Determinación de los procesadores multimedia disponibles

El código anterior usaba un procesador multimedia accediendo a él a través de un nombre de procesador multimedia específico (y si había más de una versión, utilizaba la más reciente). Para determinar qué procesadores multimedia están disponibles, podría usar el siguiente código.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

Alternativamente, el siguiente código muestra cómo recuperar el identificador de un procesador multimedia por nombre.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### Cancelación de un trabajo
Si necesita cancelar un trabajo que no ha acabado de procesarse, el siguiente código muestra cómo hacerlo mediante su identificador.

    mediaService.action(Job.cancel(jobId));

## <a id="additional-resources"></a>Recursos adicionales

Para obtener la documentación de Javadoc de Servicios multimedia, consulte [Documentación de las bibliotecas de Azure para Java][].

<!-- URLs. -->

  [Creación de una cuenta de Servicios multimedia]: http://go.microsoft.com/fwlink/?linkid=256662
  [Centro para desarrolladores de Java para Azure]: http://azure.microsoft.com/develop/java/
  [Documentación de las bibliotecas de Azure para Java]: http://dl.windowsazure.com/javadoc/
  [Desarrollo de clientes de los Servicios multimedia]: http://msdn.microsoft.com/library/windowsazure/dn223283.aspx
  [Tipos de archivo compatibles para Servicios multimedia]: http://msdn.microsoft.com/library/dn535852.aspx



<!--HONumber=42-->
