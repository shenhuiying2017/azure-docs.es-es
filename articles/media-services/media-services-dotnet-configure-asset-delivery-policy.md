---
title: "Configuración de directivas de entrega de recursos con el SDK de .NET | Microsoft Docs"
description: "En este tema se muestra cómo configurar distintas directivas de entrega de recursos con .NET SDK de Servicios multimedia de Azure."
services: media-services
documentationcenter: 
author: Mingfeiy
manager: cfowler
editor: 
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/13/2017
ms.author: juliako;mingfeiy
ms.openlocfilehash: 282fd9e24dc147e31613469926128894d48366f4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Configuración de directivas de entrega de recursos con el SDK de .NET
[!INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Información general
Si tiene pensado entregar activos cifrados, uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar directivas de entrega para los activos. La directiva de entrega de recursos indica a los Servicios multimedia cómo desea usted que se entregue el recurso: en qué protocolo de streaming se debe empaquetar de forma dinámica el recurso (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos) o si desea o no cifrar de forma dinámica el recurso y de qué manera (cifrado de sobre o común).

En este tema se explica por qué y cómo crear y configurar directivas de entrega de recursos.

>[!NOTE]
>Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 
>
>Además, para poder usar el empaquetado dinámico y el cifrado dinámico, el recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable.


Puede aplicar diferentes directivas al mismo recurso. Por ejemplo, puede aplicar cifrado PlayReady a Smooth Streaming y cifrado de sobre AES a MPEG DASH y HLS. Se bloqueará la transmisión para todos los protocolos que no estén definidos en una directiva de entrega (por ejemplo, si agrega una sola directiva que solo especifica HLS como el protocolo). La excepción a esta regla se produce en el caso de que no haya definido ninguna directiva de entrega de recursos. En tal caso, todos los protocolos estarán habilitados sin cifrar.

Si desea entregar un recurso de almacenamiento cifrado, debe configurar la directiva de entrega de recursos. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada. Por ejemplo, para entregar el recurso cifrado con la clave de cifrado de sobre de Estándar de cifrado avanzado (AES), establezca el tipo de directiva en **DynamicEnvelopeEncryption**. Para quitar el cifrado de almacenamiento y transmitir el recurso sin cifrar, establezca el tipo de directiva en **NoDynamicEncryption**. A continuación se muestran ejemplos de configuración de estos tipos de directiva.

Según cómo configure la directiva de entrega de recursos podrá realizar el empaquetamiento de forma dinámica, cifrar de forma dinámica y hacer streaming de los protocolos de streaming siguientes: secuencias Smooth Streaming, HLS y MPEG-DASH.

En la lista siguiente se muestran los formatos usados para transmitir Smooth Streaming, HLS y DASH.

Smooth Streaming:

{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{filename}.ism/Manifest

HLS:

{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=m3u8-aapl)

MPEG DASH

{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=mpd-time-csf)


## <a name="considerations"></a>Consideraciones
* No puede eliminar una entidad AssetDeliveryPolicy asociada a un activo mientras hay un localizador OnDemand (transmisión) para ese activo. Se recomienda quitar la directiva del activo antes de eliminar la directiva.
* No se puede crear un localizador de transmisión en un activo cifrado de almacenamiento cuando no se establece ninguna directiva de entrega de activos.  Si el activo no está cifrado para almacenamiento, el sistema le permitirá crear un localizador y transmitir el activo sin cifrar, sin una directiva de entrega de activos.
* Puede tener varias directivas de entrega de activos asociadas a un único activo, pero solo se puede especificar una forma de controlar un AssetDeliveryProtocol determinado,  es decir, si intenta vincular dos directivas de entrega que especifican el protocolo AssetDeliveryProtocol.SmoothStreaming que producirá un error porque el sistema no sabe cuál quiere que aplique cuando un cliente realiza una solicitud de Smooth Streaming.
* Si tiene un activo con un localizador de transmisión existente, no puede vincular una nueva directiva al activo (puede desvincular una directiva existente del activo o actualizar una directiva de entrega asociada al activo).  Primero debe quitar el localizador de transmisión, ajustar las directivas y volver a crear el localizador de transmisión.  Puede usar el mismo locatorId al volver a crear el localizador de transmisión, pero debe asegurarse de que no causará problemas para los clientes ya que se puede almacenar en caché el contenido por el origen o una red CDN de nivel inferior.

## <a name="clear-asset-delivery-policy"></a>Directiva de entrega de recursos sin cifrar

El método **ConfigureClearAssetDeliveryPolicy** siguiente especifica que no se aplique el cifrado dinámico y se entregue la transmisión en cualquiera de los siguientes protocolos: MPEG DASH, HLS y Smooth Streaming. Querrá aplicar esta directiva a los activos cifrados de almacenamiento.

Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) .

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>Directiva de entrega de recursos DynamicCommonEncryption

El método siguiente **CreateAssetDeliveryPolicy** crea la entidad **AssetDeliveryPolicy** que se configura para aplicar el cifrado común dinámico (**DynamicCommonEncryption**) a un protocolo de streaming con velocidad de transmisión adaptable (se bloqueará el streaming para otros protocolos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) e **IContentKey** (la clave de contenido de tipo **CommonEncryption**. Para más información, consulte [Creación de una clave de contenido](media-services-dotnet-create-contentkey.md#common_contentkey)).

Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) .

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

Los Servicios multimedia de Azure también permiten agregar el cifrado de Widevine. En el ejemplo siguiente se muestra cómo se agrega tanto PlayReady como Widevine a la directiva de entrega de recursos.

    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
        // to append /? KID =< keyId > to the end of the url when creating the manifest.
        // As a result Widevine license acquisition URL will have KID appended twice, 
        // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

        Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
        UriBuilder uriBuilder = new UriBuilder(widevineUrl);
        uriBuilder.Query = String.Empty;
        widevineUrl = uriBuilder.Uri;

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

> [!NOTE]
> Al cifrar con Widevine, solo podría entregar con DASH. Asegúrese de especificar DASH en el protocolo de entrega de activos.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>Directiva de entrega de recursos DynamicEnvelopeEncryption
El método **CreateAssetDeliveryPolicy** siguiente crea la entidad **AssetDeliveryPolicy** que se configura para aplicar el cifrado de sobre dinámico (**DynamicEnvelopeEncryption**) para los protocolos Smooth Streaming, HLS y DASH (si decide no especificar algunos protocolos, se bloqueará el streaming en ellos). El método toma dos parámetros: **Asset** (el recurso al que desea aplicar la directiva de entrega) e **IContentKey** (la clave de contenido de tipo **EnvelopeEncryption**. Para más información, consulte [Creación de una clave de contenido](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Para obtener información sobre los valores que puede especificar al crear una entidad AssetDeliveryPolicy, consulte la sección [Tipos usados al definir AssetDeliveryPolicy](#types) .   

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
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


## <a id="types"></a>Tipos usados al definir AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

La enumeración siguiente describe los valores que puede establecer para el protocolo de entrega de recursos.

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

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

La enumeración siguiente describe los valores que puede establecer para el tipo de directiva de entrega de recursos.  

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

La enumeración siguiente describe los valores que puede utilizar para configurar el método de entrega de la clave de contenido al cliente.
    
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

La enumeración siguiente describe los valores que puede establecer para configurar las claves usadas para obtener la configuración específica para una directiva de entrega de recursos.

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

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

