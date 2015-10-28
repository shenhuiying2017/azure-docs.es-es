<properties
	pageTitle="Uso de cifrado dinámico común de PlayReady o Widevine"
	description="Servicios multimedia de Microsoft Azure le permute entregar secuencias MPEG-DASH, Smooth Streaming y Http-Live-Streaming (HLS) protegidas con DRM de Microsoft PlayReady. AMS también le permite entregar DASH cifrado con DRM de Widevine. En este tema se muestra cómo cifrar dinámicamente con DRM de PlayReady y Widevine."
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="10/14/2015"
	ms.author="juliako"/>


#Uso de cifrado dinámico común de PlayReady o Widevine

> [AZURE.SELECTOR]
- [.NET](media-services-protect-with-drm.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)

Servicios multimedia de Microsoft Azure le permite entregar transmisiones MPEG-DASH cifradas, Smooth Streaming y Http-Live-Streaming (HLS) protegidas con licencias [DRM de Microsoft PlayReady](https://www.microsoft.com/playready/overview/). También permite entregar transmisiones DASH cifradas con licencias DRM de Widevine. Tanto PlayReady como Widewine se cifran según la especificación de cifrado común (ISO/IEC 23001-7 CENC). Puede usar el [.NET SDK de AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir de la versión 3.5.1) o la API de REST para configurar AssetDeliveryConfiguration para usar Widevine.

Servicios multimedia proporciona un servicio de entrega de licencias de Microsoft PlayReady. Servicios multimedia también proporciona API que permiten configurar los derechos y las restricciones que desee aplicar en tiempo de ejecución de DRM de PlayReady cuando un usuario reproduzca contenido protegido. Cuando un usuario solicita contenido protegido con PlayReady, la aplicación de reproductor solicitará una licencia del servicio de licencias de AMS. El servicio de licencias de AMS emitirá una licencia al reproductor, si está autorizado. Una licencia de PlayReady contiene la clave de descifrado que puede usar el reproductor cliente para descifrar y transmitir el contenido.

>[AZURE.NOTE]Actualmente, Servicios multimedia no proporciona un servidor de licencias de Widevine. Puede usar los siguientes partners de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).
>
> Para obtener más información, consulte: integración con [Axinom](media-services-axinom-integration.md) y [castLabs](media-services-castlabs-integration.md).

Servicios multimedia admite varias formas de autorizar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: abrir o restricción de token. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato [Token de web simple](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) y en formato [Token de web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Para obtener más información, consulte Configuración de la directiva de autorización de la clave de contenido.

Para aprovechar las ventajas del cifrado dinámico, debe disponer de un recurso que contenga un conjunto de archivos MP4 o archivos de origen Smooth Streaming, de varias velocidades de bits. También deberá configurar las directivas de entrega para el recurso (se describe más adelante en este tema). Luego, según el formato especificado en la URL de streaming, el servidor de streaming a petición se asegurará de que se reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en un solo formato de almacenamiento y Servicios multimedia compilará y proporcionará la respuesta adecuada en función de cada solicitud de un cliente.

Este tema será útil para los desarrolladores que trabajan en aplicaciones que entregan archivos multimedia protegidos con diversos DRM, por ejemplo, PlayReady y Widevine. Este tema describe cómo configurar el servicio de entrega de licencias de PlayReady con directivas de autorización para que solo los clientes autorizados puedan recibir licencias de PlayReady o Widevine. También muestra cómo usar el cifrado dinámico con DRM de PlayReady o Widevine a través de DASH.

>[AZURE.NOTE]Para empezar a usar el cifrado dinámico, primero debe obtener al menos una unidad de escalado (también conocida como unidad de streaming). Para obtener más información, consulte [Escalación de un servicio multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

##Configuración del cifrado dinámico común y el servicio de entrega de licencias DRM

Éstos son los pasos generales que deberá realizar al proteger sus recursos con PlayReady, mediante el servicio de entrega de licencias de Servicios multimedia y también mediante cifrado dinámico.

1. Creación de un recurso y carga de los archivos en el recurso. 
1. Codificación del recurso que contiene el archivo con Adaptive Bitrate MP4 Set.
1. Creación de una clave de contenido y su asociación con el recurso codificado. En Servicios multimedia, la clave de contenido contiene la clave de cifrado del activo.
1. Configuración de la directiva de autorización de la clave de contenido. El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave de contenido. 
1. Configuración de la directiva de entrega para un recurso. La configuración de directivas de entrega incluye: el protocolo de entrega (por ejemplo, MPEG DASH, HLS, HDS, Smooth Streaming o todos), el tipo de cifrado dinámico (por ejemplo, cifrado común) y la URL de adquisición de licencias de PlayReady o Widevine. 
 
	Puede aplicar diferentes directivas a cada protocolo en el mismo recurso. Por ejemplo, puede aplicar cifrado PlayReady a Smooth/DASH y AES Envelope a HLS. Se bloqueará la transmisión para todos los protocolos que no estén definidos en una directiva de entrega (por ejemplo, si agrega una sola directiva que solo especifica HLS como el protocolo). La excepción a esta regla se produce en el caso de que no haya definido ninguna directiva de entrega de recursos. En tal caso, todos los protocolos estarán habilitados sin cifrar.
1. Creación de un localizador a petición para obtener una URL de streaming.

>[AZURE.NOTE]Actualmente, Servicios multimedia no proporciona un servidor de licencias de Widevine. Puede usar los siguientes partners de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).
>
> Para obtener más información, consulte: integración con [Axinom](media-services-axinom-integration.md) y [castLabs](media-services-castlabs-integration.md).

Encontrará un ejemplo completo para .NET al final del tema.

La siguiente imagen muestra el flujo de trabajo que se ha descrito anteriormente. Aquí el token se usa para la autenticación.

![Protección con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

El resto de este tema proporciona explicaciones detalladas, ejemplos de código y vínculos a temas que le muestran cómo lograr las tareas descritas arriba.

##Limitaciones actuales

Si agrega o actualiza la directiva de entrega de recursos, debe eliminar el localizador asociado (si hay) y crear uno nuevo.

##Creación de un recurso y carga de los archivos en el recurso.

Para administrar, codificar y transmitir por secuencias sus vídeos, primero debe cargar el contenido en Servicios multimedia de Microsoft Azure. Una vez cargado, el contenido se almacena de forma segura en la nube para su posterior procesamiento y transmisión.

Para obtener información detallada, consulte [Carga de archivos en una cuenta de Servicios multimedia](media-services-dotnet-upload-files.md).

##Codificación del recurso que contiene el archivo con Adaptive Bitrate MP4 Set.

Con el cifrado dinámico, todo lo que tiene que hacer es crear un recurso que contenga un conjunto de archivos MP4 o archivos Smooth Streaming, de varias velocidades de bits. Luego, según el formato especificado en la solicitud de manifiesto o de fragmento, el servidor de streaming a petición se asegurará de que reciba la transmisión en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente. Para obtener más información, consulte el tema [Información general sobre el empaquetado dinámico](media-services-dynamic-packaging-overview.md).

Para obtener instrucciones sobre cómo codificar, consulte [Codificación de un recurso mediante Codificador multimedia estándar](media-services-dotnet-encode-with-media-encoder-standard.md).
	

##<a id="create_contentkey"></a>Creación de una clave de contenido y su asociación con el recurso codificado

En Servicios multimedia, la clave de contenido contiene la clave con la que desea cifrar un recurso.

Para obtener más información, consulte [Creación de la clave de contenido](media-services-dotnet-create-contentkey.md).


##<a id="configure_key_auth_policy"></a>Configuración de la directiva de autorización de claves de contenido.

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave. La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP.

Para obtener información detallada, consulte [Configuración de la directiva de autorización de claves de contenido](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

##<a id="configure_asset_delivery_policy"></a>Configuración de directivas de entrega de recursos 

Configure la directiva de entrega de sus recursos. Algunos de los elementos que incluye la configuración de la directiva de entrega de recursos son:

- La dirección URL de adquisición de licencias de DRM. 
- El protocolo de entrega de recursos (por ejemplo, MPEG DASH, HLS, HDS, Smooth Streaming o todos). 
- El tipo de cifrado dinámico (en este caso, cifrado común). 

Para obtener más información, consulte [Configuración de directivas de entrega de recursos](media-services-rest-configure-asset-delivery-policy.md).

##<a id="create_locator"></a>Creación de un localizador de streaming a petición para obtener una URL de streaming

Deberá suministrar al usuario la URL de streaming para Smooth, DASH o HLS.

>[AZURE.NOTE]Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.

Para obtener instrucciones sobre cómo publicar un recurso y generar una dirección URL de streaming, vea [Creación de una dirección URL de streaming](media-services-deliver-streaming-content.md).

##Obtención de un token de prueba

Obtenga un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves.

	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate = 
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the data in the given TokenRestrictionTemplate.
	//The GenerateTestToken method returns the token without the word “Bearer” in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

	
Puede usar el [reproductor de AMS](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para probar la transmisión.

##<a id="example"></a>Ejemplo

1. Creación de un nuevo proyecto de consola
1. Use NuGet para instalar y agregar Extensiones del SDK de Servicios multimedia de Azure para .NET. Al instalar este paquete, también se instala el SDK de Servicios multimedia para .NET y agrega todas las demás dependencias necesarias.
2. Agregue referencias adicionales: System.Runtime.Serialization y System.Configuration.
2. Agregue el archivo de configuración que contiene el nombre de cuenta y la información de clave:

	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Sobrescriba el código del archivo Program.cs con el código mostrado en esta sección.
	
	Asegúrese de actualizar las variables para que apunten a las carpetas donde se encuentran los archivos de entrada.
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		
		namespace CommonDynamicEncryptAndKeyDeliverySvc
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
		            Console.WriteLine();
		    
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		
		                //The GenerateTestToken method returns the token without the word “Bearer” in front
		                //so you have to add it in front of the token string. 
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey, DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		

		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted MPEG-DASH URL: {0}/Manifest(format=mpd-time-csf) ", url);
		
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.StorageEncrypted);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
	
				static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
				{
				    // Declare a new job.
				    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
				    // Get a media processor reference, and pass to it the name of the 
				    // processor to use for the specific task.
				    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
				
				    // Create a task with the encoding details, using a string preset.
				    // In this case "H264 Multiple Bitrate 720p" preset is used.
				    ITask task = job.Tasks.AddNew("My encoding task",
				        processor,
				        "H264 Multiple Bitrate 720p",
				        TaskOptions.None);
				
				    // Specify the input asset to be encoded.
				    task.InputAssets.Add(asset);
				    // Add an output asset to contain the results of the job. 
				    // This output is specified as AssetCreationOptions.None, which 
				    // means the output asset is not encrypted. 
				    task.OutputAssets.AddNew("Output asset",
				        AssetCreationOptions.None);
				
				    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
				    job.Submit();
				    job.GetExecutionProgressTask(CancellationToken.None).Wait();
				
				    return job.OutputMediaAssets[0];
				}

		
		        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
		        {
		            // Create Common Encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryption);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		    {
		
		        // Create ContentKeyAuthorizationPolicy with Open restrictions 
		        // and create authorization policy          
		
		        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		        {
		            new ContentKeyAuthorizationPolicyRestriction 
		            { 
		                Name = "Open", 
		                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
		                Requirements = null
		            }
		        };
		
		        // Configure PlayReady license template.
		        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		        IContentKeyAuthorizationPolicyOption policyOption =
		            _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.PlayReadyLicense,
		                    restrictions, newLicenseTemplate);
		
		        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                    ContentKeyAuthorizationPolicies.
		                    CreateAsync("Deliver Common Content Key with no restrictions").
		                    Result;
		
		
		        contentKeyAuthorizationPolicy.Options.Add(policyOption);
		
		        // Associate the content key authorization policy with the content key.
		        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		        contentKey = contentKey.UpdateAsync().Result;
		    }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            IContentKeyAuthorizationPolicy policy = _context.
		                                    ContentKeyAuthorizationPolicies.
		                                    CreateAsync("HLS token restricted authorization policy").Result;
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		            {
		                new ContentKeyAuthorizationPolicyRestriction 
		                { 
		                    Name = "Token Authorization Policy", 
		                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                    Requirements = tokenTemplateString, 
		                }
		            };
		
		            // Configure PlayReady license template.
		            string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
		
		            IContentKeyAuthorizationPolicyOption policyOption =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                    ContentKeyDeliveryType.PlayReadyLicense,
		                        restrictions, newLicenseTemplate);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common Content Key with no restrictions").
		                        Result;
		            
		            policy.Options.Add(policyOption);
		
		            // Add ContentKeyAutorizationPolicy to ContentKey
		            contentKeyAuthorizationPolicy.Options.Add(policyOption);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        } 
		
		        static private string ConfigurePlayReadyLicenseTemplate()
		        {
		            // The following code configures PlayReady License Template using .NET classes
		            // and returns the XML string.
		             
		            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
		            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
		
		            responseTemplate.LicenseTemplates.Add(licenseTemplate);
		
		            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
		        }
		
		
				static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
				{
				    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
				
				    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
				        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
				    {
				        {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
				        {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl,"http://testurl"},
				        
				    };
				
				    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
				            "AssetDeliveryPolicy",
				        AssetDeliveryPolicyType.DynamicCommonEncryption,
				        AssetDeliveryProtocol.Dash,
				        assetDeliveryPolicyConfiguration);
				
				   
				    // Add AssetDelivery Policy to the asset
				    asset.DeliveryPolicies.Add(assetDeliveryPolicy);
				
				}
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }


				static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
				{
				    Console.WriteLine("Job state changed event:");
				    Console.WriteLine("  Previous state: " + e.PreviousState);
				    Console.WriteLine("  Current state: " + e.CurrentState);
				    switch (e.CurrentState)
				    {
				        case JobState.Finished:
				            Console.WriteLine();
				            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
				            break;
				        default:
				            break;
				    }
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


##Rutas de aprendizaje de Servicios multimedia

Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

- [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##Consulte también

[Configuración del empaquetado Widevine con AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

<!---HONumber=Oct15_HO3-->