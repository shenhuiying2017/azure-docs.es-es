<properties 
	pageTitle="Configuración de directivas de entrega de recursos con .NET" 
	description="Este tema muestra cómo configurar directivas de entrega de recursos distintas." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="juliako"/>

# Configuración de directivas de entrega de recursos
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

Este artículo forma parte de las series [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](../media-services-video-on-demand-workflow) y [Flujo de trabajo de streaming en vivo](../media-services-live-streaming-workflow). 

Uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar las directivas de entrega para los recursos que desea transmitir. La directiva de entrega de recursos indica a Servicios multimedia cómo desea que se entregue el recurso: en qué protocolo de streaming debe empaquetarse dinámicamente el recurso (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos), si no desea cifrar dinámicamente el recurso y cómo (sobre o cifrado común). 

En este tema se explica por qué y cómo crear y configurar directivas de entrega de recursos. 

>[AZURE.NOTE]Para poder usar el empaquetado dinámico y el cifrado dinámico, debe asegurarse de tener al menos una unidad de escala (también conocida como unidad de streaming). Para obtener más información, consulte [Escalación de un servicio multimedia](../media-services-manage-origins#scale_streaming_endpoints). 
>
>Además, el recurso debe contener un conjunto de MP4 de velocidad de bits adaptable o archivos de Smooth Streaming de velocidad de bits adaptable.      

Puede aplicar diferentes directivas al mismo recurso. Por ejemplo, podría aplicar cifrado de PlayReady a Smooth Streaming y el cifrado de sobre AES a MPEG DASH y HLS. Se impedirá el streaming en todos los protocolos que no están definidos en una directiva de entrega (por ejemplo, agregar una única directiva que solo especifica HLS como protocolo). La excepción a esto es si no tiene ninguna directiva de entrega de recursos definida. En este caso, podrá cifrar todos los protocolos.

Tenga en cuenta que si desea entregar un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido mediante el uso de la directiva de entrega especificada. Por ejemplo, para entregar el recurso cifrado con la clave de cifrado de sobres de Estándar de cifrado avanzado (AES), establezca el tipo de directiva en **DynamicEnvelopeEncryption**. Para quitar el cifrado de almacenamiento y transmitir el recurso sin cifrar, establezca el tipo de directiva en **NoDynamicEncryption**. A continuación se indican ejemplos que muestran cómo configurar estos tipos de directiva. 

Dependiendo de cómo configure la directiva de entrega de recursos, podrá empaquetar dinámicamente, cifrar dinámicamente y transmitir los siguientes protocolos de streaming: Smooth Streaming, HLS, MPEG DASH y HDS.  

En la lista siguiente se muestran los formatos que se utilizan para transmitir Smooth Streaming, HLS, DASH y HDS.  

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Para obtener instrucciones sobre cómo publicar un recurso y generar una dirección URL de streaming, consulte [Generación de una dirección URL de streaming](../media-services-deliver-streaming-content).

## Borrado de una directiva de entrega de recursos 

El método **ConfigureClearAssetDeliveryPolicy** siguiente especifica que no se aplique el cifrado dinámico y se entregue la secuencia en cualquiera de los siguientes protocolos:  MPEG DASH, HLS y Smooth Streaming. 
  
Para obtener información sobre los valores que se pueden especificar al crear un AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) . 

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

## Directiva de entrega de activos de DynamicCommonEncryption 


El método siguiente **CreateAssetDeliveryPolicy** crea el **AssetDeliveryPolicy** que se configura para aplicar el cifrado común dinámico (**DynamicCommonEncryption**) a un protocolo de Smooth Streaming (no se aplicará el streaming en otros protocolos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) e **IContentKey** (la clave de contenido de tipo **CommonEncryption**; para obtener más información, consulte: [Creación de una clave de contenido](../media-services-dotnet-create-contentkey#common_contentkey)).

Para obtener información sobre los valores que se pueden especificar al crear un AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) . 


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }



## Directiva de entrega de recursos DynamicEnvelopeEncryption 

El método siguiente **CreateAssetDeliveryPolicy** crea el **AssetDeliveryPolicy** que se configura para aplicar el cifrado de sobre dinámico (**DynamicEnvelopeEncryption**) para protocolos HLS y DASH (no se aplicará el streaming en otros protocolos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) e **IContentKey** (la clave de contenido de tipo **EnvelopeEncryption**; para obtener más información, consulte: [Creación de una clave de contenido](../media-services-dotnet-create-contentkey#envelope_contentkey)).


Para obtener información sobre los valores que se pueden especificar al crear un AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) .   

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


## <a id="types"></a>Tipos usados al definir AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    }
<!--HONumber=45--> 
