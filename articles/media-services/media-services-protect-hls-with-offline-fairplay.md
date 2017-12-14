---
title: "Protección del contenido HLS con Apple FairPlay - Azure sin conexión | Microsoft Docs"
description: "En este tema se ofrece información general y se muestra cómo usar Azure Media Services para cifrar dinámicamente el contenido HTTP Live Streaming (HLS) con Apple FairPlay en modo sin conexión."
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>FairPlay Streaming sin conexión
Microsoft Azure Media Services proporciona [servicios de protección de contenido](https://azure.microsoft.com/services/media-services/content-protection/) de alto diseño, como:
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- Cifrado AES-128

El cifrado de contenido DRM/AES se realiza dinámicamente a petición para los distintos protocolos de streaming. Azure Media Services también proporciona servicios de entrega de claves de descifrado de licencia de DRM/AES.

Además de la protección del contenido de streaming en línea sobre diversos protocolos de streaming, el modo sin conexión para el contenido protegido también es una característica solicitada con frecuencia. Se requiere compatibilidad con el modo sin conexión para los escenarios siguientes:
1. Reproducción cuando la conexión a Internet no está disponible, como durante los viajes.
2. Algunos proveedores de contenido pueden suspender la entrega de licencias de DRM al cruzar la frontera de un país. Si un usuario desea ver contenido mientras viaja en el extranjero, se necesita la descarga sin conexión.
3. En algunos países, la disponibilidad de Internet o de ancho de banda sigue siendo limitada. Los usuarios pueden decidir descargar primero para poder ver el contenido en una resolución lo suficientemente alta que les permita disfrutar de una experiencia de visualización satisfactoria. En este caso, con mayor frecuencia, el problema no es la disponibilidad de la red, sino que su ancho de banda es limitado. Los proveedores OTT/OVP están solicitando compatibilidad con el modo sin conexión.

En este artículo se trata la compatibilidad con el modo sin conexión de FairPlay Streaming (FPS) destinado a dispositivos que ejecutan iOS 10 o posterior. Esta característica no se admite en otras plataformas de Apple como watchOS, tvOS o Safari en Mac OS.

## <a name="preliminary-steps"></a>Pasos previos
Antes de implementar DRM sin conexión para FairPlay en un dispositivo iOS 10 o superior, primero debe hacer lo siguiente:
1. Familiarizarse con la protección de contenido en línea para FairPlay. Este tema se trata con detalle en los siguientes artículos y ejemplos:
- [Apple FairPlay Streaming for Azure Media Services generally available](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/) (Disponibilidad general de Apple FairPlay Streaming para Azure Media Services)
- [Protección del contenido HLS con Apple FairPlay o Microsoft PlayReady](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [Un ejemplo de streaming FPS en línea](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Obtener el SDK de FPS de Apple Developer Network. El SDK de FPS contiene dos componentes:
- El SDK de FPS Server, que contiene el KSM (módulo de seguridad de claves), ejemplos de cliente, una especificación y un conjunto de vectores de prueba.
- Paquete de implementación de FPS, que contiene la función D, una especificación junto con instrucciones sobre cómo generar el certificado de FPS, la clave privada específica del cliente y la clave de secreto de aplicación (ASK). Apple emite el paquete de implementación de FPS únicamente a los proveedores de contenido con licencia.

## <a name="configuration-in-azure-media-services"></a>Configuración en Azure Media Services
Para establecer la configuración del modo sin conexión de FPS a través del [SDK de .NET de Azure Media Services](https://www.nuget.org/packages/windowsazure.mediaservices), debe usar el SDK de .NET de Azure Media Services en la versión 4.0.0.4 o posterior, que proporciona la API necesaria para configurar el modo sin conexión de FPS.
Como se indica en las suposiciones anteriores, se supone que tiene el código de trabajo para la configuración de la protección del contenido FPS en modo en línea. Una vez que tenga el código para configurar la protección de contenido de modo en línea para FPS, solo necesita los siguientes dos cambios.

## <a name="code-change-in-fairplay-configuration"></a>Cambio de código en la configuración de FairPlay
Vamos a definir un valor booleano para "habilitar el modo sin conexión", denominado objDRMSettings.EnableOfflineMode, que es "true" cuando se habilita el escenario de DRM sin conexión. En función de este indicador, realizamos el siguiente cambio en la configuración de FairPlay:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Cambio de código en la configuración de la directiva de entrega de activos
El segundo cambio consiste en agregar la tercera clave al diccionario Dictionary<AssetDeliveryPolicyConfigurationKey, string>.
El tercer valor AssetDeliveryPolicyConfigurationKey tiene que agregarse del siguiente modo: 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Después de este paso, el diccionario Dictionary<AssetDeliveryPolicyConfigurationKey, string> en la directiva de entrega de activos de FPS contendrá las siguientes tres entradas:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl o AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl en función de factores como el servidor de claves/KSM utilizado y si deseamos volver a utilizar la misma directiva de entrega de activos en los diferentes activos
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Ahora la cuenta de Media Services está configurada para entregar licencias FairPlay sin conexión.

## <a name="sample-ios-player"></a>Reproductor iOS de ejemplo
En primer lugar, debemos mencionar que la compatibilidad con el modo sin conexión de FPS está disponible solo en iOS 10 y versiones posteriores. Debemos obtener el SDK de FPS Server (v3.0 o posterior) que contiene el documento y un ejemplo para el modo sin conexión de FPS. En concreto, el SDK de FPS Server (v3.0 o posterior) contiene los dos elementos siguientes relacionados con el modo sin conexión:
1. Documento: Offline Playback with FairPlay Streaming and HTTP Live Streaming (Reproducción sin conexión con FairPlay Streaming y HTTP Live Streaming). Apple, 14/09/2016. En el SDK de FPS Server v 4.0, este documento se ha combinado con el documento de streaming de FPS principal.
2. Código de ejemplo: ejemplo de HLSCatalog para el modo sin conexión de FPS en \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. En la aplicación de ejemplo HLSCatalog, los archivos de código siguientes tienen como misión fundamental la implementación de las características del modo sin conexión:
- Archivo de código AssetPersistenceManager.swift: AssetPersistenceManager es la clase principal de este ejemplo que muestra
    - Cómo administrar la descarga de secuencias HLS, como las API para iniciar y cancelar la descarga o eliminar activos existentes del dispositivo del usuario.
    - Cómo supervisar el progreso de la descarga.
- Archivos de código AssetListTableViewController.swift y AssetListTableViewCell.swift: AssetListTableViewController es la interfaz principal de este ejemplo. Proporciona una lista de activos en los que el ejemplo puede reproducir, descargar, eliminar o cancelar una descarga. 

A continuación se muestran los pasos detallados para poner en marcha un reproductor de iOS. Supongamos que inicia desde el ejemplo de HLSCatalog en el SDK de FPS Server en la versión 4.0.1.  Es necesario realizar los siguientes cambios en el código:

En HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente el método `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` utilizando el código siguiente: digamos que drmUr es una variable asignada a la dirección URL de streaming de HLS.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

En HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implemente el método `requestApplicationCertificate()`. Esta implementación depende de si inserta el certificado (solo clave pública) en el dispositivo u hospeda el certificado en la web. A continuación se muestra una implementación con el certificado de la aplicación hospedada que se utiliza en nuestros ejemplos de prueba. Supongamos que certUrl es una variable que contiene la dirección URL del certificado de la aplicación.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

Para la prueba final integrada, se proporcionará la dirección URL de vídeo y dirección URL de certificado de la aplicación en la sección "Prueba integrada".

En HLSCatalog\Shared\Resources\Streams.plist, agregue la dirección URL de prueba de vídeo y, para el identificador de clave de contenido, podemos usar simplemente la dirección URL de adquisición de la licencia de FairPlay con el protocolo SDK como valor único.

![Secuencias de aplicación de iOS de FairPlay sin conexión](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Para la dirección URL de vídeo de prueba, la dirección URL de adquisición de licencias FairPlay y la dirección URL de certificado de la aplicación, puede utilizar la suya propia, si la ha configurado, o puede continuar con la siguiente sección en la que figuran ejemplos de pruebas.

## <a name="integrated-test"></a>Prueba integrada
Se han configurado tres ejemplos de pruebas en Azure Media Services que abarcan los tres escenarios siguientes:
1.  FPS protegido, con pista de vídeo, audio y audio alternativo.
2.  FPS protegido, con pista de vídeo y audio pero sin audio alternativo.
3.  FPS protegido, solo con vídeo, sin audio.

Estos ejemplos se pueden encontrar en este [sitio de demostración](http://aka.ms/poc#22), con el correspondiente certificado de la aplicación hospedada en una aplicación web de Azure.
Hemos observado que, con un ejemplo de la versión3 o 4 del SDK de FPS Server, si una lista de reproducción maestra contiene audio alternativo, durante el modo sin conexión reproduce audio solo. Por lo tanto, es necesario quitar el audio alternativo. En otras palabras, en los tres ejemplos anteriores, (2) y (3) funcionan en modo en línea y sin conexión. Pero (1) reproducirá solo audio durante el modo sin conexión, mientras que el streaming en línea funciona correctamente.

## <a name="faq"></a>P+F
Preguntas frecuentes para la solución de problemas:
- **¿Por qué se reproduce solo el audio pero no el vídeo en el modo sin conexión?** Este comportamiento parece deberse al diseño de la aplicación de ejemplo. Cuando hay una pista de audio alternativo presente (como es el caso de HLS), durante el modo sin conexión, tanto iOS 10 como iOS 11 utilizarán de manera predeterminada la pista de audio alternativo. Para compensar este comportamiento para el modo sin conexión de FPS, es necesario quitar la pista de audio alternativo de la secuencia. Para hacer esto en Azure Media Services, basta con agregar el filtro de manifiesto dinámico "audio-only=false". En otras palabras, una dirección URL de HLS finalizaría con .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **¿Por qué se sigue reproduciendo solo audio sin vídeo durante el modo sin conexión después de haber agregado audio-only=false?** En función del diseño de clave de caché de CDN, es posible que el contenido se haya almacenado en la caché. Es necesario purgar la caché.
- **¿El modo sin conexión de FPS es compatible con iOS 11 además de iOS 10?** Sí, el modo sin conexión de FPS se admite tanto en iOS 10 como en iOS 11.
- **¿Por qué no encuentro el documento Offline Playback with FairPlay Streaming and HTTP Live Streaming (Reproducción sin conexión con FairPlay Streaming y HTTP Live Streaming) en el SDK de FPS Server?** A partir de la versión 4 del SDK de FPS Server, este documento se ha combinado con el documento relativo a la guía de programación de streaming de FairPlay.
- **¿Qué significa el último parámetro en la siguiente API para el modo sin conexión de FPS?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

La documentación para esta API se puede encontrar [aquí](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). El parámetro representa la duración del alquiler sin conexión con la hora como unidad.
- **¿Cuál es la estructura del archivo descargado o sin conexión en dispositivos iOS?** La estructura del archivo descargado en un dispositivo iOS es similar a la siguiente (captura de pantalla). La carpeta `_keys` almacena licencias FPS descargadas, un archivo de almacén para cada host de servicio de licencia. La carpeta `.movpkg` almacena el contenido de audio y vídeo. La primera carpeta, cuyo nombre termina con un guion seguido de un valor numérico, contiene datos de vídeo. El valor numérico es el valor "PeakBandwidth" de la reproducción de vídeo. La segunda carpeta, cuyo nombre termina con un guión seguido de 0, contiene datos de audio. La tercera carpeta, denominada "Data", contiene la lista de reproducción maestra del contenido FPS. Boot.xml proporciona una descripción completa del contenido de la carpeta `.movpkg` (vea a continuación un archivo de ejemplo de boot.xml).

![Estructura del archivo de aplicación de ejemplo de iOS de FairPlay sin conexión](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Un archivo boot.xml de ejemplo:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="summary"></a>Resumen
En este documento se proporcionan pasos detallados e información para aplicar el modo sin conexión de FPS, como lo siguiente:
1. Configuración de protección de contenido de Azure Media Services a través de la API de .NET de AMS. Esto configura el cifrado FairPlay dinámico y la entrega de licencias de FairPlay en AMS.
2. Reproductor de iOS basado en el ejemplo del SDK de FPS Server de Apple. Esto podría configurar un reproductor de iOS que pueda reproducir el contenido de FPS en modo de streaming en línea o en modo sin conexión.
3. Vídeos de FPS de ejemplo para probar el modo sin conexión y el streaming en línea.
4. Preguntas frecuentes sobre el modo sin conexión de FPS.
