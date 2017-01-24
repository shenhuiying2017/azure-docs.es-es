---
title: "Protección del contenido HLS con Apple FairPlay o Microsoft PlayReady | Microsoft Docs"
description: "Este tema ofrece información general y muestra cómo usar Azure Media Services para cifrar dinámicamente el contenido HTTP Live Streaming (HLS) con Apple FairPlay. También muestra cómo utilizar el servicio de entrega de licencias de Servicios multimedia para entregar licencias de FairPlay a los clientes."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b67594033bfa89fc6ff8d80dd064d766c4048a3b


---
# <a name="protect-your-hls-content-with-apple-fairplay-andor-microsoft-playready"></a>Protección del contenido HLS con Apple FairPlay o Microsoft PlayReady
Servicios multimedia de Azure permite cifrar dinámicamente el contenido HTTP Live Streaming (HLS) mediante los siguientes formatos:  

* **Clave sin cifrado de sobre AES-128**

    El fragmento completo se cifra mediante el modo **AES-128 CBC** . El reproductor de OSX e iOS admite el descifrado de la transmisión de forma nativa. Para obtener más información, consulte [este artículo](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Las muestras de audio y vídeo individuales se cifran mediante el modo **AES-128 CBC** . **FairPlay Streaming** (FPS) se integra en los sistemas operativos de dispositivos, con compatibilidad nativa en iOS y TV de Apple. Safari en OS X permite FPS mediante la compatibilidad con la interfaz Encrypted Media Extensions (EME).
* **Microsoft PlayReady**

La siguiente imagen muestra el flujo de trabajo de **HLS más cifrado dinámico de FairPlay o PlayReady** .

![Protección con FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Este tema muestra cómo usar Servicios multimedia de Azure para cifrar dinámicamente el contenido HLS con FairPlay de Apple. También muestra cómo utilizar el servicio de entrega de licencias de Servicios multimedia para entregar licencias de FairPlay a los clientes.

> [!NOTE]
> Si también quiere cifrar el contenido HLS con PlayReady, debe crear una clave de contenido común y asociarla con el recurso. También debe configurar la directiva de autorización de la clave de contenido, como se describe en el tema [Uso de cifrado dinámico común de PlayReady](media-services-protect-with-drm.md) .
>
>

## <a name="requirements-and-considerations"></a>Requisitos y consideraciones
* Se necesita lo siguiente para usar AMS para proporcionar HLS cifrado con FairPlay y entregar licencias de FairPlay.

  * Una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Una cuenta de Servicios multimedia. Para crear una cuenta de Media Services, consulte [Creación de cuenta](media-services-portal-create-account.md).
  * Suscríbase al [programa de desarrollo de Apple](https://developer.apple.com/).
  * En Apple es obligatorio que el propietario del contenido obtenga el [paquete de implementación](https://developer.apple.com/contact/fps/). Indique en la solicitud que ya ha implementado KSM (módulo principal de seguridad) con Servicios multimedia de Azure y que está solicitando el paquete FPS final. Se proporcionarán instrucciones en el paquete FPS final para generar certificados y obtener la ASK, que luego se utilizarán para configurar FairPlay.
  * Versión **3.6.0** del SDK .NET de Servicios multimedia de Azure.
* En la entrega de claves de AMS se debe establecer lo siguiente:

  * **Certificación de aplicaciones (AC)** : archivo .pfx que contiene la clave privada. Este archivo lo crea el cliente y lo cifra el mismo cliente mediante una contraseña+.

       Cuando el cliente configura la directiva de entrega de claves, debe proporcionar esa contraseña y el archivo .pfx en formato base64.

      En los pasos siguientes se describe cómo generar un certificado pfx para FairPlay.

    1. Instalación de OpenSSL desde https://slproweb.com/products/Win32OpenSSL.html

        Vaya a la carpeta donde están los certificados FairPlay y otros archivos cuyo emisor sea Apple.
    2. Línea de comandos para convertir archivos CER en PEM:

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
    3. Línea de comandos para convertir archivos PEM en PFX con la clave privada (OpenSSL solicita la contraseña del archivo pfx).

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
  * **Contraseña de certificación de aplicaciones** : contraseña del cliente para crear el archivo .pfx.
  * **Identificador de contraseña de certificación de aplicaciones**: el cliente debe cargar la contraseña de forma similar a otras claves de AMS y mediante el valor de enumeración **ContentKeyType.FairPlayPfxPassword**. Como resultado se obtendrá el identificador de AMS, que es lo que se necesita usar dentro de la opción de directiva de entrega de claves.
  * **iv**: valor aleatorio de 16 bytes que debe coincidir con el IV de la directiva de entrega de recursos. El cliente genera el IV y lo pone en ambos lugares: en la directiva de entrega de recursos y en la opción de directiva de entrega de claves.
  * **ASK** : la clave secreta de la aplicación (ASK, Application Secret Key) se recibe cuando se genera la certificación mediante el portal para desarrolladores de Apple (Apple Developer). Cada equipo de desarrollo recibirá una única ASK. Guarde una copia de la ASK y almacénela en un lugar seguro. Debe configurar ASK como FairPlayAsk en Servicios multimedia de Azure más adelante.
  * **Identificador de ASK** : se obtiene cuando el cliente carga ASk en AMS. El cliente debe cargar ASk mediante el valor de enumeración **ContentKeyType.FairPlayASk** . Como resultado, se obtiene el identificador de AMS, que debe utilizarse al establecer la opción de directiva de entrega de claves.
* En el lado cliente FPS se debe establecer lo siguiente:

  * **Certificación de aplicaciones (AC)** : archivo .cer o .der que contiene la clave pública que utiliza el SO para cifrar algunas cargas. AMS necesita tener información sobre él porque lo necesita el reproductor. El servicio de entrega de claves lo descifra utilizando la clave privada correspondiente.
* Para reproducir una transmisión cifrada FairPlay, necesita obtener primero la ASK real y luego generar un certificado real. Este proceso crea todas las tres partes:

  * .der,
  * .pfx y
  * la contraseña para el archivo .pfx.
* Clientes compatibles con HLS con cifrado **AES-128 CBC** : Safari en OS X, TV Apple e iOS.

## <a name="steps-for-configuring-fairplay-dynamic-encryption-and-license-delivery-services"></a>Pasos para configurar el cifrado dinámico y los servicios de entrega de licencias de FairPlay
Estos son los pasos generales que deberá realizar cuando proteja los recursos con FairPlay, mediante el servicio de entrega de licencias de Servicios multimedia y también mediante cifrado dinámico.

1. Creación de un recurso y carga de los archivos en el recurso.
2. Codificación del recurso que contiene el archivo con Adaptive Bitrate MP4 Set.
3. Creación de una clave de contenido y su asociación con el recurso codificado.  
4. Configuración de la directiva de autorización de la clave de contenido. Al crear la directiva de autorización de claves de contenido, necesita especificar lo siguiente:

   * Método de entrega (en este caso, FairPlay)
   * Configuración de opciones de directiva de FairPlay. Para más detalles sobre cómo configurar FairPlay, consulte el método ConfigureFairPlayPolicyOptions() en el siguiente ejemplo.

     > [!NOTE]
     > En general, lo más seguro es que desee configurar las opciones de directiva de FairPlay solo una vez, ya que solo tendrá un conjunto de certificados y ASK.
     >
     >
   * Restricciones (abiertas o token),
   * Y la información específica del tipo de entrega de claves que define cómo se entrega la clave al cliente.
5. Configure la directiva de entrega de recursos. La configuración de directiva de entrega incluye:

   * Protocolo de entrega (HLS),
   * El tipo de cifrado dinámico (cifrado CBC común),
   * Direcciones URL de adquisición de licencias.

     > [!NOTE]
     > Si desea entregar una transmisión que se cifra con FairPlay + otro DRM, tiene que configurar directivas de entrega independientes:
     >
     > * Una IAssetDeliveryPolicy para configurar DASH con CENC (PlayReady + WideVine) y Smooth con PlayReady.
     > * Otra IAssetDeliveryPolicy para configurar FairPlay para HLS
     >
     >
6. Creación de un localizador a petición para obtener una URL de streaming.

## <a name="using-fairplay-key-delivery-by-playerclient-apps"></a>Uso de la entrega de la clave FairPlay de aplicaciones cliente/reproductor
Los clientes podían desarrollar aplicaciones de reproductor con el SDK de iOS. Para poder reproducir el contenido de FairPlay, los clientes deben implementar el protocolo de intercambio de licencias. Apple no especifica el protocolo de intercambio de licencias. Depende de cada aplicación decidir cómo enviar las solicitudes de entrega de claves. Los servicios de entrega de claves de AMS FairPlay esperan que SPC funcione como mensaje de publicación codificado www-form-url con el formato siguiente:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Reproductor multimedia de Azure no admite la reproducción de FairPlay de fábrica. Los clientes tienen que obtener el reproductor de ejemplo de la cuenta de desarrollador de Apple para obtener la reproducción de FairPlay en MAC OSX.
>
>

## <a name="streaming-urls"></a>Direcciones URL de streaming
Si el recurso se cifró con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL de streaming: (formato = 'm3u8-aapl', cifrado = 'xxx').

Se aplican las siguientes consideraciones:

* Se puede especificar solo un tipo de cifrado o ninguno.
* El tipo de cifrado no tiene que especificarse en la dirección URL si solo se aplicó un cifrado al recurso.
* El tipo de cifrado distingue mayúsculas de minúsculas.
* Se pueden especificar los siguientes tipos de cifrado:  
  * **cenc**: cifrado común (Playready o Widevine)
  * **cbcs-aapl**: Fairplay
  * **cbc**: cifrado de sobre AES.

## <a name="net-example"></a>Ejemplo de .NET
El ejemplo siguiente muestra la funcionalidad que se introdujo en el SDK de Servicios multimedia de Azure para la versión 3.6.0 de .Net (la capacidad de usar Servicios multimedia de Azure para entregar el contenido cifrado con FairPlay). El siguiente comando del paquete de Nuget se usó para instalar el paquete:

    PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Creación de un proyecto de consola.
2. Use NuGet para instalar y agregar el SDK de Servicios multimedia de Azure para .NET.
3. Agregue referencias adicionales: System.Configuration.
4. Agregue el archivo de configuración que contiene el nombre de cuenta y la información de clave:

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
5. Obtener al menos una unidad de streaming para el extremo de streaming para el que planea entregar el contenido. Para obtener más información, consulte [Configuración de puntos de conexión de streaming](media-services-dotnet-get-started.md#configure-streaming-endpoints-using-the-azure-portal).
6. Sobrescriba el código del archivo Program.cs con el código mostrado en esta sección.

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
        using Newtonsoft.Json;
        using System.Security.Cryptography.X509Certificates;

        namespace DynamicEncryptionWithFairPlay
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

                    IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }

                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

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

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";

                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));

                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),     AssetCreationOptions.StorageEncrypted);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }

                static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
                {
                    // Create HLS SAMPLE AES encryption content key
                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);

                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryptionCbcs);

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


                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                        ContentKeyDeliveryType.FairPlay,
                        restrictions,
                        FairPlayConfiguration);


                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }

                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                            {
                                new ContentKeyAuthorizationPolicyRestriction
                                {
                                    Name = "Token Authorization Policy",
                                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                                    Requirements = tokenTemplateString,
                                }
                            };

                    // Configure FairPlay policy option.
                    string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


                    IContentKeyAuthorizationPolicyOption FairPlayPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                               ContentKeyDeliveryType.FairPlay,
                               restrictions,
                               FairPlayConfiguration);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;

                    return tokenTemplateString;
                }

                private static string ConfigureFairPlayPolicyOptions()
                {
                    // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
                    // However, for production you must use a real ASK from Apple bound to a real prod certificate.
                    byte[] askBytes = Guid.NewGuid().ToByteArray();
                    var askId = Guid.NewGuid();
                    // Key delivery retrieves askKey by askId and uses this key to generate the response.
                    IContentKey askKey = _context.ContentKeys.Create(
                                            askId,
                                            askBytes,
                                            "askKey",
                                            ContentKeyType.FairPlayASk);

                    //Customer password for creating the .pfx file.
                    string pfxPassword = "<customer password for creating the .pfx file>";
                    // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
                    var pfxPasswordId = Guid.NewGuid();
                    byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
                    IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                                            pfxPasswordId,
                                            pfxPasswordBytes,
                                            "pfxPasswordKey",
                                            ContentKeyType.FairPlayPfxPassword);

                    // iv - 16 bytes random value, must match the iv in the asset delivery policy.
                    byte[] iv = Guid.NewGuid().ToByteArray();

                    //Specify the .pfx file created by the customer.
                    var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

                    string FairPlayConfiguration =
                        Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                            appCert,
                            pfxPassword,
                            pfxPasswordId,
                            askId,
                            iv);

                    return FairPlayConfiguration;
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

                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

                    var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

                    FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

                    // Get the FairPlay license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

                    // The reason the below code replaces "https://" with "skd://" is because
                    // in the IOS player sample code which you obtained in Apple developer account,
                    // the player only recognizes a Key URL that starts with skd://.
                    // However, if you are using a customized player,
                    // you can choose whatever protocol you want.
                    // For example, "https".

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                            {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
                        };

                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
                        AssetDeliveryProtocol.HLS,
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

                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
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
            }
        }


## <a name="next-steps-media-services-learning-paths"></a>Siguientes pasos: Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Dec16_HO2-->


