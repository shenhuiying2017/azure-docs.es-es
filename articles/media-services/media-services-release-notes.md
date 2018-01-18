---
title: "Notas de la versión de Media Services | Microsoft Docs"
description: "Notas de la versión de Media Services"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako
ms.openlocfilehash: 4775374b7e91930daa686e48e2869b4891615c4c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="azure-media-services-release-notes"></a>Notas de la versión de Azure Media Services
Estas notas de la versión de Azure Media Services resumen los cambios realizados desde las versiones anteriores y los problemas conocidos.

> [!NOTE]
> Queremos recibir opiniones de nuestros clientes para poder centrarnos en la solución de los problemas que les afectan. Para informar de un problema o formular una pregunta, envíe su consulta al [foro de MSDN de Azure Media Services].
> 
> 

## <a name="a-idissuescurrently-known-issues"></a><a id="issues"/>Problemas actualmente conocidos
### <a name="a-idgeneralissuesmedia-services-general-issues"></a><a id="general_issues"/>Problemas generales de Media Services

| Problema | DESCRIPCIÓN |
| --- | --- |
| Varios encabezados HTTP comunes no se proporcionan en la API de REST. |Si desarrolla aplicaciones de Media Services mediante la API de REST, encontrará que algunos campos de encabezado HTTP comunes (como CLIENT-REQUEST-ID, REQUEST-ID y RETURN-CLIENT-REQUEST-ID) no se admiten. Los encabezados se agregarán en una futura actualización. |
| No se permite la codificación porcentual. |Media Services usa el valor de la propiedad IAssetFile.Name al generar direcciones URL para el contenido de streaming (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Por esta razón, no se permite la codificación porcentual. El valor de la propiedad Name no puede tener ninguno de los siguientes [caracteres reservados para la codificación porcentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Además, solo puede haber un "." para la extensión del nombre de archivo. |
| El método ListBlobs que es parte del SDK de Azure Storage, versión 3.x, no funciona correctamente. |Media Services genera direcciones URL de SAS basadas en la versión del [12-02-2012](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) . Si desea usar el SDK de Azure Storage para mostrar los blobs de un contenedor de blobs, utilice el método [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que es parte del SDK de Azure Storage, versión 2.x. |
| El mecanismo de limitación de Media Services restringe el uso de recursos en las aplicaciones que realizan un número excesivo de solicitudes al servicio. El servicio puede devolver el código de estado HTTP 503 "Servicio no disponible". |Para más información, consulte la descripción del código de estado HTTP 503 en el tema [Códigos de error de Media Services](media-services-encoding-error-codes.md). |
| Al consultar entidades, hay un límite de 1 000 entidades devueltas a la vez, porque la REST v2 pública limita los resultados de consulta a 1 000 resultados. |Debe usar Skip y Take (.NET) o top (REST) como se describe en [este ejemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) y [este ejemplo de la API de REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Algunos clientes pueden experimentar un problema de etiqueta de repetición en el manifiesto de streaming con velocidad de transmisión adaptable. |Para más información, consulte [esta sección](media-services-deliver-content-overview.md#known-issues). |
| Los objetos del SDK de Media Services para .NET no se pueden serializar y, como resultado, no funcionan con Azure Redis Cache. |Si intenta serializar el objeto AssetCollection del SDK para agregarlo a Azure Redis Cache, se produce una excepción. |


## <a name="a-idrestversionhistoryrest-api-version-history"></a><a id="rest_version_history"/>Historial de versiones de la API de REST
Para obtener información sobre el historial de versiones de la API de REST de Media Services, consulte la [Referencia de la API de REST de Azure Media Services].

## <a name="october-2017-release"></a>Versión de octubre de 2017
> [!IMPORTANT] 
> Media Services está dejando el soporte técnico para las claves de autenticación de Azure Access Control Service. A partir del 1 de junio de 2018, ya no se podrá usar código para autenticarse en el back-end de Media Services con claves de Access Control Service. Debe actualizar el código para que use Azure Active Directory (Azure AD) por [autenticación basada en Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Inspeccione las advertencias sobre este cambio en Azure Portal.

### <a name="updates-for-october-2017"></a>Actualizaciones de octubre de 2017
#### <a name="sdks"></a>SDK
* El SDK para .NET se ha actualizado para admitir la autenticación de Azure AD. Se ha eliminado la compatibilidad con la autenticación de Access Control Service del SDK para .NET más reciente en Nuget.org, con el fin de promover una migración más rápida a Azure AD. 
* El SDK para Java se ha actualizado para admitir la autenticación de Azure AD. Se ha agregado compatibilidad con la autenticación de Azure AD al SDK para Java. Para información acerca de cómo usar el SDK para Java con Media Services, consulte [Introducción al SDK de cliente de Java para Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificación basada en archivo
* Ahora puede usar el codificador Premium para codificar el contenido para el códec de vídeo H.265 de codificación de vídeo de alta eficacia (HEVC). No afecta a los precios si elige H.265 frente a otros códecs, por ejemplo, H.264. Para obtener información acerca de las licencias de la patente HEVC, consulte las [Condiciones de los servicios en línea](https://azure.microsoft.com/support/legal/).
* Para un vídeo de origen codificado con el códec de vídeo H.265(HEVC), como un vídeo capturado mediante iOS11 o GoPro Hero 6, ahora puede usar el codificador Premium o el Estándar para codificarlo. Para obtener información acerca de las licencias de la patente, consulte las [Condiciones de los servicios en línea](https://azure.microsoft.com/support/legal/).
* Para el contenido que contiene varias pistas de audio de idioma, los valores del idioma deben estar etiquetados correctamente según la especificación del formato de archivo correspondiente (por ejemplo, ISO MP4). A continuación, puede usar el codificador estándar para codificar el contenido para streaming. El localizador de streaming resultante muestra una lista de los idiomas de audio disponibles.
* El codificador estándar ahora admite dos nuevos valores preestablecidos del sistema de audio, "Audio AAC" y "Audio AAC de buena calidad". Ambos producen una salida de codificación de audio avanzada (AAC) en estéreo, a velocidades de bits de 128 kbps y 192 kbps, respectivamente.
* El codificador Premium ahora admite los formatos de archivo QuickTime y MOV como entrada. El códec de vídeo debe ser uno de los [tipos de ProRes de Apple que se enumeran en este artículo de GitHub](https://docs.microsoft.com/azure/media-services/media-services-media-encoder-standard-formats). El audio debe ser AAC o modulación de código de pulso (PCM). El codificador Premium no admite, por ejemplo, vídeo DVC o DVCPro encapsulado en archivos QuickTime o MOV como entrada. El codificador estándar admite estos códecs de vídeo.
* Se realizaron las correcciones de errores siguientes en los codificadores:

    * Ahora puede enviar trabajos mediante el uso de un recurso de entrada. Una vez finalizadas estas tareas, puede modificar el recurso (por ejemplo, agregar, eliminar o cambiar el nombre de archivos del recurso) y enviar trabajos adicionales.
    * Se ha mejorado la calidad de las miniaturas JPEG que genera el codificador estándar.
    * El codificador estándar controla mejor los metadatos de entrada y la generación de miniaturas en vídeos de muy corta duración.
    * Las mejoras en el descodificador H.264 que se usa en el codificador estándar eliminan ciertos artefactos poco frecuentes. 

#### <a name="media-analytics"></a>Media Analytics
Disponibilidad general de Azure Media Redactor: este procesador multimedia realiza la anonimización desenfocando las caras de las personas seleccionadas y su uso es ideal en casos de seguridad pública y medios informativos. 

Para obtener información general sobre este nuevo procesador, consulte [esta publicación del blog](https://azure.microsoft.com/blog/azure-media-redactor/). Para obtener información sobre la documentación y configuración, consulte [Censura de rostros con Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versión de junio de 2017

Media Services ahora admite la [autenticación basada en Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Actualmente, Media Services es compatible con el modelo de autenticación de Access Control Service. La autorización de Access Control Service dejará de usarse el 1 de junio de 2018. Se recomienda migrar tan pronto como sea posible al modelo de autenticación de Azure AD.

## <a name="march-2017-release"></a>Versión de marzo de 2017

Ahora puede usar el codificador Estándar para [generar automáticamente una escalera de velocidad de bits](media-services-autogen-bitrate-ladder-with-mes.md) especificando la cadena preestablecida "Adaptive Streaming" (Streaming adaptable) al crear la tarea de codificación. Puede utilizar el valor preestablecido "Adaptive Streaming" para codificar un vídeo para streaming con Media Services. Para personalizar un valor preestablecido de codificación para su escenario específico, puede comenzar con [estos valores preestablecidos](media-services-mes-presets-overview.md).

Ahora puede usar Media Encoder Standard o Media Encoder Premium Workflow para [crear una tarea de codificación que genera fragmentos de fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Versión de febrero de 2017

A partir del 1 de abril de 2017, se eliminarán automáticamente los registros de trabajo de más de 90 días de su cuenta, junto con los registros de tarea asociados. La eliminación se produce aunque el número total de registros no llegue a la cuota máxima. Para archivar la información del trabajo o la tarea, puede usar el código descrito en [Administración de recursos y entidades relacionadas con el SDK de Media Services para .NET](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versión de enero de 2017

En Media Services, un punto de conexión de streaming representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. Media Services también proporciona una integración perfecta con Azure Content Delivery Network. La secuencia de salida de un servicio de punto de conexión de streaming puede ser una secuencia en vivo, un vídeo bajo demanda o una descarga progresiva de su recurso en su cuenta de Media Services. Cada cuenta de Media Services incluye un punto de conexión de streaming predeterminado. Es posible crear puntos de conexión de streaming adicionales en la cuenta. 

Existen dos versiones de puntos de conexión de streaming, 1.0 y 2.0. A partir del 10 de enero de 2017 las nuevas cuentas de Media Services creadas incluirán la versión 2.0 del punto de conexión de streaming predeterminado. Los puntos de conexión de streaming adicionales que agregue a esta cuenta también se generarán en la versión 2.0. Este cambio no afecta a las cuentas existentes. Los puntos de conexión de streaming existentes son versión 1.0 y se pueden actualizar a la versión 2.0. Hay cambios de comportamiento, facturación y características con este cambio. Para obtener más información, consulte [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Información general de puntos de conexión de streaming).

A partir de la versión 2.15, se agregaron en Media Services las siguientes propiedades a la entidad punto de conexión de streaming:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* Versión de punto de conexión de streaming 

Para más información acerca de estas propiedades, consulte [Punto de conexión de streaming](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versión de diciembre de 2016

 Ahora puede usar Media Services para acceder a los datos de telemetría y métricas de sus servicios. Puede usar la versión actual de Media Services para recopilar datos de telemetría de un canal en vivo, de un punto de conexión de streaming y de entidades de archivo. Para más información, consulte [Telemetría en Media Services](media-services-telemetry-overview.md).

## <a name="a-idjulychanges16july-2016-release"></a><a id="july_changes16"/>Versión de julio de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Actualizaciones al archivo de manifiesto (*.ism) generado por las tareas de codificación
Cuando se envía una tarea de codificación a Media Encoder Standard o Media Encoder Premium, la tarea de codificación genera un [archivo de manifiesto de streaming](media-services-deliver-content-overview.md) (*.ism) en el recurso de salida. Con la última versión del servicio se ha actualizado la sintaxis de este archivo de manifiesto de streaming.

> [!NOTE]
> La sintaxis del archivo de manifiesto de streaming (.ism) está reservada para uso interno. Está sujeto a cambios en versiones futuras. No modifique ni manipule el contenido de este archivo.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Se genera un nuevo archivo de manifiesto de cliente (*.ismc) en el recurso de salida cuando una tarea de codificación genera uno o varios archivos MP4
A partir de la versión del servicio más reciente, después de completar una tarea de codificación que genera uno más archivos MP4, el recurso de salida también contiene un archivo de manifiesto de cliente de streaming (*.ismc). El archivo .ismc ayuda a mejorar el rendimiento del streaming dinámico. 

> [!NOTE]
> La sintaxis del archivo de manifiesto de cliente (.ismc) está reservada para uso interno. Está sujeto a cambios en versiones futuras. No modifique ni manipule el contenido de este archivo.
> 
> 

Para más información, vea [este blog](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/).

### <a name="known-issues"></a>Problemas conocidos
Algunos clientes pueden experimentar un problema de etiqueta de repetición en el manifiesto de streaming con velocidad de transmisión adaptable. Para más información, consulte [esta sección](media-services-deliver-content-overview.md#known-issues).

## <a id="apr_changes16"></a>Versión de abril de 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services presentó Media Analytics para proporcionar una inteligencia de vídeo eficaz. Para más información, consulte [Introducción a Analytics de Media Services](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (versión preliminar)
Ahora puede utilizar Media Services para cifrar dinámicamente el contenido HTTP Live Streaming (HLS) con Apple FairPlay. Ahora también puede utilizar el servicio de entrega de licencias de Media Services para entregar licencias de FairPlay a los clientes. Para más información, consulte "Uso de Azure Media Services para transmitir contenido HLS protegido con Apple FairPlay".

## <a id="feb_changes16"></a>Versión de febrero de 2016
La última versión del SDK de Media Services para .NET (3.5.3) contiene una corrección de errores relacionada con Google Widevine. AssetDeliveryPolicy no se podía reutilizar para varios recursos cifrados con Widevine. Como parte de esta corrección de errores se ha agregado la siguiente propiedad al SDK: WidevineBaseLicenseAcquisitionUrl.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Versión de enero de 2016
Se cambia el nombre de las unidades reservadas de codificación para reducir la confusión con los nombres de codificador.

Los nombres de las unidades reservadas de codificación Básica, Estándar y Premium se cambian por unidades reservadas S1, S2 y S3, respectivamente. Los clientes que utilizan unidades reservadas de codificación Básica hoy en día ven S1 como etiqueta en Azure Portal (y en la factura). Los clientes que usen Estándar y Premium ven las etiquetas S2 y S3, respectivamente. 

## <a id="dec_changes_15"></a>Versión de diciembre de 2015

### <a name="media-encoder-deprecation-announcement"></a>Anuncio de entrada en desuso de Media Encoder

 Media Encoder dejará de utilizarse unos 12 meses después del lanzamiento de Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>SDK de Azure para PHP
El equipo del SDK de Azure publicó una nueva versión del paquete [SDK de Azure para PHP](http://github.com/Azure/azure-sdk-for-php) que contiene actualizaciones y nuevas características de Media Services. En concreto, el SDK de Media Services para PHP ahora admite las características más recientes de [protección de contenido](media-services-content-protection-overview.md). Estas características son el cifrado dinámico con AES y DRM (PlayReady y Widevine) con y sin restricciones de token. También admite el escalado de [unidades de codificación](media-services-dotnet-encoding-units.md).

Para obtener más información, consulte 

* El blog del [SDK de Media Services para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/).
* Los siguientes [ejemplos de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) le ayudan a empezar a trabajar rápidamente:
  * **vodworkflow_aes.php**: se trata de un archivo PHP que muestra cómo usar el cifrado dinámico AES-128 y el servicio de entrega de claves. Se basa en el ejemplo de .NET que se explica en [Uso del cifrado dinámico AES-128 y el servicio de entrega de claves](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: se trata de un archivo PHP que muestra cómo usar el cifrado dinámico de PlayReady y el servicio de entrega de licencias. Se basa en el ejemplo de .NET que se explica en [Uso del cifrado dinámico común de PlayReady y Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: se trata de un archivo PHP que muestra cómo escalar las unidades reservadas de codificación.

## <a id="nov_changes_15"></a>Versión de noviembre de 2015
 Media Services ofrece ahora el servicio de entrega de licencias en la nube Widevine. Para más información, vea [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Consulte también [este tutorial](media-services-protect-with-playready-widevine.md) y el [repositorio de GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Los servicios de entrega de licencias de Widevine proporcionados por Media Services se encuentran en versión preliminar. Para más información, vea [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versión de octubre de 2015
Media Services ahora está disponible en los centros de datos siguientes: Sur de Brasil, Oeste de la India, Sur de la India y Centro de la India. Ahora se puede usar Azure Portal para [crear cuentas de Media Services](media-services-portal-create-account.md) y realizar varias tareas que se describen en la [página web de documentación de Media Services](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding no está habilitado en estos centros de datos. Además, no todos los tipos de unidades reservadas de codificación están disponibles en estos centros de datos.

* Sur de Brasil: solo están disponibles las unidades reservadas de codificación Estándar y Básica.
* Oeste de la India, Sur de la India y Centro de la India: solo están disponibles las unidades reservadas de codificación Básicas.

## <a id="september_changes_15"></a>Versión de septiembre de 2015
Media Services ofrece ahora la posibilidad de proteger tanto vídeo bajo demanda como secuencias en vivo con la tecnología DRM modular de Widevine. Puede usar los siguientes asociados de los servicios de entrega para ayudarle a entregar licencias de Widevine:
* [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](http://ezdrm.com/) 
* [castLabs](http://castlabs.com/company/partners/azure/) 

Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Puede usar el [SDK de Media Services para .NET](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir de la versión 3.5.1) o la API de REST para configurar AssetDeliveryConfiguration para usar Widevine. 
* Media Services agregó compatibilidad para vídeos ProRes de Apple. Ahora puede cargar sus archivos de vídeos de origen QuickTime que usan ProRes de Apple u otros códecs. Para más información, vea [este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Ahora puede usar Media Encoder Standard para realizar recortes secundarios y extracción de archivos en vivo. Para más información, vea [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Se realizaron las siguientes actualizaciones de filtrado: 
  
  * Ahora puede usar el formato Apple HLS con un filtro solo de audio. Esta actualización le permite quitar la pista solo de audio mediante la especificación de (audio-only=false) en la dirección URL.
  * Al definir filtros para los recursos, ahora puede combinar varios filtros (hasta tres) en una sola dirección URL.
    
    Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services admite ahora I-frames en HLS versión 4. La compatibilidad con I-frame optimiza las operaciones de avance rápido y rebobinado. De forma predeterminada, todas las salidas de HLS versión 4 incluyen la lista de reproducción de I-frame (EXT-X-I-FRAME-STREAM-INF).
Para más información, vea [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a id="august_changes_15"></a>Versión de agosto de 2015
* Ya están disponibles el SDK de Media Services para la versión de Java 0.8.0 y nuevos ejemplos. Para obtener más información, consulte 
  
  * [Esta publicación del blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [El Repositorio de ejemplos de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Actualización de Azure Media Player con compatibilidad con secuencias de audio múltiples. Para más información, vea [esta publicación del blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a id="july_changes_15"></a>Versión de julio de 2015
* Se anuncia la disponibilidad general de Media Encoder Standard. Para más información, vea [esta publicación del blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard usa valores preestablecidos que se describen en [esta sección](http://go.microsoft.com/fwlink/?LinkId=618336). Cuando se usa un valor preestablecido para codificaciones de 4K, debe obtener el tipo de unidad reservada Premium. Para más información, consulte [Escalado de codificación](media-services-scale-media-processing-overview.md).
* Se utilizaron títulos en vivo en tiempo real con Media Services y Media Player. Para más información, vea [esta publicación del blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Actualizaciones del SDK de Media Services para .NET
La versión del SDK de Media Services para .NET ahora es la 3.4.0.0. Se realizaron las siguientes actualizaciones: 

* Se ha implementado la compatibilidad con los archivos activos. No se puede descargar un recurso que contenga un archivo activo.
* Se ha implementado compatibilidad con los filtros dinámicos.
* La funcionalidad se implementa para que los usuarios puedan mantener un contenedor de almacenamiento cuando eliminan un recurso.
* Se han realizado correcciones de errores relacionados con directivas de reintentos en canales.
* Se ha habilitado Media Encoder Premium Workflow.

## <a id="june_changes_15"></a>Versión de junio de 2015
### <a name="media-services-net-sdk-updates"></a>Actualizaciones del SDK de Media Services para .NET
La versión del SDK de Media Services para .NET ahora es la 3.3.0.0. Se realizaron las siguientes actualizaciones: 

* Se agregó compatibilidad para la especificación de detección de OpenId Connect.
* Se agregó compatibilidad con el control de sustitución de claves en el lado del proveedor de identidades.

Si usa un proveedor de identidades que expone un documento de detección de OpenID Connect (algo que hacen Azure AD, Google y Salesforce), puede indicar a Media Services que obtenga las claves de firma para la validación de los token web JSON (JWT) de la especificación de detección de OpenID Connect. 

Para más información, consulte [Uso de claves web JSON de la especificación de detección OpenID Connect para trabajar con la autenticación JWT en Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Versión de mayo de 2015
Se anunciaron las nuevas características siguientes:

* [Una versión preliminar de Live Encoding con Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Manifiesto dinámico](media-services-dynamic-manifest-overview.md)
* [Una versión preliminar del procesador multimedia de Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Versión de abril de 2015
### <a name="general-media-services-updates"></a>Actualizaciones generales de Media Services
* Se anunció [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* A partir de Media Services REST 2.10, los canales configurados para ingerir un protocolo de mensajes en tiempo real (RTMP) se crean con direcciones URL de ingesta principal y secundaria. Para más información, consulte [Configuraciones de ingesta de canales](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Se actualizó Azure Media Indexer.
* Se agregó compatibilidad con el idioma español.
* Se agregó una nueva configuración para el formato XML.

Para más información, vea [este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Actualizaciones del SDK de Media Services para .NET
La versión del SDK de Media Services para .NET ahora es la 3.2.0.0. Se realizaron las siguientes actualizaciones:

* Cambios importantes: el tipo de TokenRestrictionTemplate.Issuer y TokenRestrictionTemplate.Audience se ha cambiado a cadena.
* Se han realizado actualizaciones relacionadas con la creación de directivas de reintento personalizadas.
* Se han realizado correcciones de errores relacionados con la carga y descarga de archivos.
* La clase MediaServicesCredentials ahora acepta los puntos de conexión de control de acceso principal y secundario para la autenticación.

## <a id="march_changes_15"></a>Versión de marzo de 2015
### <a name="general-media-services-updates"></a>Actualizaciones generales de Media Services
* Media Services ahora proporciona integración con Content Delivery Network. Para admitir la integración, se ha agregado la propiedad CdnEnabled a StreamingEndpoint. CdnEnabled puede utilizarse con las API de REST a partir de la versión 2.9. Para obtener más información, consulte [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). CdnEnabled puede utilizarse con el SDK para .NET a partir de la versión 3.1.0.2. Para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx).
* Se anunció Media Encoder Premium Workflow. Para más información, consulte [Introducción a la codificación Premium en Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Versión de febrero de 2015
### <a name="general-media-services-updates"></a>Actualizaciones generales de Media Services
La versión de la API de REST de Media Services es ahora la 2.9. A partir de esta versión, puede habilitar la integración de Content Delivery Network con los puntos de conexión de streaming. Para obtener más información, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Versión de enero de 2015
### <a name="general-media-services-updates"></a>Actualizaciones generales de Media Services
Anuncio de la disponibilidad general de la protección de contenido con cifrado dinámico. Para más información, consulte [Media Services mejora la seguridad en streaming con la disponibilidad general de la tecnología DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Actualizaciones del SDK de Media Services para .NET
La versión del SDK de Media Services para .NET ahora es la 3.1.0.1.

Esta versión marcó el constructor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate como obsoleto. El nuevo constructor toma TokenType como argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versión de diciembre de 2014
### <a name="general-media-services-updates"></a>Actualizaciones generales de Media Services
* Se agregaron algunas actualizaciones y nuevas características a Media Indexer. Para más información, consulte [Notas de la versión de Azure Media Indexer versión 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Se agregó una nueva API de REST que puede usar para actualizar las unidades reservadas de codificación. Para más información, consulte [EncodingReservedUnitType con REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Se agregó compatibilidad con CORS para el servicio de entrega de claves.
* Se realizaron mejoras en el rendimiento de la consulta de opciones de directivas de autorización.
* En el centro de datos de China, la [dirección URL de entrega de claves](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) es ahora por cliente (al igual que en otros centros de datos).
* Se agregó la duración de destino automático de HLS. Cuando se realiza el streaming en vivo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Media Services calcula automáticamente la proporción de empaquetado por segmento HLS (FragmentsPerSegment) según el intervalo de fotogramas clave (KeyFrameInterval). Este método también se conoce como un grupo de imágenes (GOP) que se recibe desde el codificador en directo. Para más información, consulte [Información general de streaming en vivo con Azure Media Services](http://msdn.microsoft.com/library/azure/dn783466.aspx).

### <a name="media-services-net-sdk-updates"></a>Actualizaciones del SDK de Media Services para .NET
La versión del [SDK de Media Services para .NET](http://www.nuget.org/packages/windowsazure.mediaservices/) ahora es la 3.1.0.0. Se realizaron las siguientes actualizaciones:

* Se ha actualizado la dependencia del SDK para .NET a .NET Framework 4.5.
* Se agregó una nueva API que puede usar para actualizar las unidades reservadas de codificación. Para más información, consulte [Actualización del tipo de unidades reservadas y aumento de las unidades reservadas de codificación mediante .NET](media-services-dotnet-encoding-units.md).
* Se agregó compatibilidad con la autenticación de token JWT. Para más información, consulte [Autenticación de token JWT en Media Services y cifrado dinámico](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Se agregaron desplazamientos relativos para BeginDate y ExpirationDate en la plantilla de licencia de PlayReady.

## <a id="november_changes_14"></a>Versión de noviembre de 2014
* Ahora puede usar Media Services para ingerir contenido de Smooth Streaming (fMP4) en vivo sobre una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS. Actualmente, Media Services no admite SSL con dominios personalizados. Para más información sobre streaming en vivo, consulte [Información general de streaming en vivo con Azure Media Services](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* En la actualidad no se puede ingerir una secuencia en vivo RTMP por una conexión SSL.
* Solo puede transmitir por SSL si el punto de conexión de streaming desde el que se entrega el contenido se creó después del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en puntos de conexión de streaming creados después del 10 de septiembre de 2014, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene el formato antiguo y desea transmitir a través de SSL, [cree un punto de conexión de streaming nuevo](media-services-portal-manage-streaming-endpoints.md). Utilice direcciones URL basadas en el nuevo punto de conexión de streaming para transmitir el contenido a través de SSL.

## <a id="october_changes_14"></a>Versión de octubre de 2014
### <a id="new_encoder_release"></a>Versión del codificador de Media Services
 Anuncio de la nueva versión de Azure Media Encoder en Media Services. Con la versión más reciente de Media Encoder, se le cobrará solo por GB de salida. Por otro lado, el nuevo codificador es compatible en características con el codificador anterior. Para más información, consulte [Detalle de precios de Media Services].

### <a id="oct_sdk"></a>SDK .NET de Media Services
La versión de las extensiones del SDK de Media Services para .NET es ahora la 2.0.0.3.

La versión del SDK de Media Services para .NET es ahora la 3.0.0.8. Se realizaron las siguientes actualizaciones:

* Se ha implementado la refactorización en las clases de directivas de reintento.
* Se ha agregado una cadena de agente de usuario a los encabezados de solicitud HTTP.
* Se ha agregado un paso de compilación de restauración de NuGet.
* Se corrigieron las pruebas del escenario para utilizar el certificado x509 del repositorio.
* Se agregaron valores para la validación posterior a la actualización del canal y la transmisión.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nuevo repositorio de GitHub para hospedar las muestras de Media Services
Hay ejemplos en el [repositorio de GitHub de ejemplos de Media Services](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Versión de septiembre de 2014
La versión de los metadatos de REST de Media Services es ahora la 2.7. Para más información sobre las últimas actualizaciones de REST, consulte [Referencia de la API de REST de Media Services](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

La versión del SDK de Media Services para .NET es ahora la 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Cambios importantes
* Origin ha cambiado a [StreamingEndpoint].
* Se ha realizado un cambio en el comportamiento predeterminado al usar Azure Portal para codificar y después publicar archivos MP4.

### <a id="sept_14_GA_changes"></a>Nuevas características y escenarios que forman parte de la versión de disponibilidad general
* Se anuncia el procesador de multimedia de Media Indexer. Para más información, consulte [Indexación de archivos multimedia con Media Indexer](http://msdn.microsoft.com/library/azure/dn783455.aspx).
* Puede usar la entidad [StreamingEndpoint] para agregar nombres de dominio personalizados (host).
  
    Para usar un nombre de dominio personalizado como nombre del punto de conexión de streaming de Media Services, debe agregar nombres de host personalizados al punto de conexión de streaming. Puede usar las API de REST o el SDK de Media Services para .NET para agregar nombres de host personalizados.
  
    Se aplican las siguientes consideraciones:
  
  * Debe tener la propiedad del nombre de dominio personalizado.
  * Media Services debe validar la propiedad del nombre de dominio. Para validar el dominio, cree un objeto CName que asigne el dominio primario MediaServicesAccountId para verificar el servidor DNS mediaservices-dns-zone.
  * Debe crear otro objeto CName que asigne el nombre de host personalizado (por ejemplo, sports.contoso.com) al nombre de host del punto de conexión de streaming de Media Services (por ejemplo, amstest.streaming.mediaservices.windows.net).

    Para más información, consulte la propiedad CustomHostNames en el artículo [Punto de conexión de streaming](http://msdn.microsoft.com/library/azure/dn783468.aspx).

### <a id="sept_14_preview_changes"></a>Nuevas características/escenarios que forman parte de la versión preliminar pública
* Versión preliminar de streaming en vivo. Para más información, consulte [Información general de streaming en vivo con Azure Media Services](http://msdn.microsoft.com/library/azure/dn783466.aspx).
* Servicio de entrega de claves. Para más información, consulte [Uso del cifrado dinámico AES-128 y el servicio de entrega de claves](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Cifrado dinámico AES. Para más información, consulte [Uso del cifrado dinámico AES-128 y el servicio de entrega de claves](http://msdn.microsoft.com/library/azure/dn783457.aspx).
* Servicio de entrega de licencias PlayReady. Para más información, consulte [Uso del cifrado dinámico de PlayReady y el servicio de entrega de licencias](http://msdn.microsoft.com/library/azure/dn783467.aspx).
* Cifrado dinámico de PlayReady. Para más información, consulte [Uso del cifrado dinámico de PlayReady y el servicio de entrega de licencias](http://msdn.microsoft.com/library/azure/dn783467.aspx).
* Plantilla de licencias de PlayReady de Media Services. Para más información, consulte [Introducción a la plantilla de licencias de PlayReady de Media Services].
* Streaming de recursos de almacenamiento cifrado. Para más información, consulte [Streaming de contenido de almacenamiento cifrado](http://msdn.microsoft.com/library/azure/dn783451.aspx).

## <a id="august_changes_14"></a>Versión de agosto de 2014
Cuando se codifica un recurso, se produce un recurso de salida tras la finalización del trabajo de codificación. Hasta esta versión, el codificador de Media Services producía metadatos sobre los recursos de salida. A partir de esta versión, el codificador también produce metadatos sobre los recursos de entrada. Para más información, consulte [Metadatos de entrada] y [Metadatos de salida].

## <a id="july_changes_14"></a>Versión de julio de 2014
Se han corregido los siguientes errores para Azure Media Services Packager y Encryptor:

* Cuando un recurso de archivo dinámico se transmite a HLS se reproduce solo el audio: este problema se ha corregido y ahora se pueden reproducir el audio y el vídeo.
* Al empaquetar un recurso en HLS y cifrado de sobre AES de 128 bits, las secuencias empaquetadas no se reproducen en dispositivos Android: este error se ha corregido y la secuencia empaquetada se reproduce en dispositivos Android compatibles con HLS.

## <a id="may_changes_14"></a>Versión de mayo de 2014
### <a id="may_14_changes"></a>Actualizaciones generales de Media Services
Ahora puede usar el [empaquetado dinámico] para transmitir HLS versión 3. Para transmitir HLS versión 3, agregue el siguiente formato a la ruta del localizador de origen: *.ism/manifest(format=m3u8-aapl-v3). Para más información, vea [este blog](http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/).

El empaquetado dinámico admite ahora también la entrega de HLS (versión 3 y versión 4) cifrado con PlayReady basado en Smooth Streaming cifrado estáticamente con PlayReady. Para obtener información sobre cómo cifrar Smooth Streaming con PlayReady, consulte [Protección de Smooth Streaming con PlayReady](http://msdn.microsoft.com/library/azure/dn189154.aspx).

### <a name="may_14_donnet_changes"></a>Actualizaciones del SDK de Media Services para .NET
La versión del SDK de Media Services para .NET ahora es la 3.0.0.5. Se realizaron las siguientes actualizaciones:

* La velocidad y la resistencia son mejores al cargar y descargar recursos multimedia.
* Se han realizado mejoras en la gestión de excepciones transitorias y la lógica de reintento: 
  
  * La detección de errores transitorios y la lógica de reintento se han mejorado en las excepciones ocasionadas al realizar consultas, guardar cambios y cargar y descargar archivos. 
  * Al obtener excepciones web (por ejemplo, durante una solicitud de token de Access Control Service), los errores irrecuperables se producen más rápido ahora.

Para más información, consulte [Lógica de reintento en el SDK de Media Services para .NET].

## <a id="april_changes_14"></a>Versión del codificador de abril de 2014
### <a name="april_14_enocer_changes"></a>Actualizaciones del codificador de Media Services
* Se ha agregado compatibilidad para ingerir archivos AVI creados con el editor no lineal Grass Valley EDIUS. En este proceso, el vídeo se comprime ligeramente con el códec Grass Valley HQ/HQX. Para más información, consulte [Grass Valley anuncia la transmisión en streaming EDIUS 7 a través de la nube].
*  Se ha agregado compatibilidad con la especificación de la convención de nomenclatura para los archivos producidos por el codificador de Media Services. Para más información, consulte [Control de los nombres de los archivos de salida del codificador de Media Services](http://msdn.microsoft.com/library/azure/dn303341.aspx).
*  Se ha agregado compatibilidad con las superposiciones de vídeo y/o audio. Para más información, consulte [Creación de superposiciones](http://msdn.microsoft.com/library/azure/dn640496.aspx).
*  Se ha agregado compatibilidad con la unión de varios segmentos de vídeo. Para más información, consulte [Unión de segmentos de vídeo](http://msdn.microsoft.com/library/azure/dn640504.aspx).
* Se corrigió un error relacionado con la transcodificación de archivos MP4 en los que el audio se ha codificado con MPEG-1 Audio Layer 3 (también conocido como MP3).

## <a id="jan_feb_changes_14"></a>Versiones de enero/febrero de 2014
### <a name="jan_fab_14_donnet_changes"></a>SDK de Media Services para .NET 3.0.0.1, 3.0.0.2 y 3.0.0.3
Los cambios en 3.0.0.1 y 3.0.0.2 incluyen los siguientes:

* Se han corregido los problemas relacionados con el uso de consultas LINQ con instrucciones OrderBy.
* Se han dividido las soluciones de prueba de [GitHub] en pruebas basadas en unidades y pruebas basadas en escenarios.

Para más información acerca de los cambios, consulte [SDK de Media Services para .NET 3.0.0.1 y 3.0.0.2](http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/).

Se han realizado los siguientes cambios en la versión 3.0.0.3:

* Se han actualizado las dependencias de Azure Storage para usar la versión 3.0.3.0.
* Se ha corregido un problema de compatibilidad con versiones anteriores en las versiones 3.0.*.* .

## <a id="december_changes_13"></a>Versión de diciembre de 2013
### <a name="dec_13_donnet_changes"></a>SDK de Media Services para .NET 3.0.0.0
> [!NOTE]
> Las versiones 3.0.x.x no son compatibles con las versiones 2.4.x.x.
> 
> 

La última versión del SDK de Media Services es ahora la 3.0.0.0. Puede descargar el último paquete de NuGet u obtener los bits de [GitHub].

A partir de la versión 3.0.0.0 del SDK de Media Services, puede reutilizar los tokens de [Azure AD Access Control Service](http://msdn.microsoft.com/library/hh147631.aspx). Para más información, consulte la sección "Reutilización de los tokens de Access Control Service" en [Conexión a Media Services con el SDK de Media Services para .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

### <a name="dec_13_donnet_ext_changes"></a>Extensiones del SDK de Media Services para .NET 2.0.0.0
 Las extensiones del SDK de Media Services para .NET son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Media Services. Puede obtener los bits más recientes en [Extensiones del SDK de Media Services para .NET](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a id="november_changes_13"></a>Versión de noviembre de 2013
### <a name="nov_13_donnet_changes"></a>Cambios en el SDK de Media Services para .NET
A partir de esta versión, el SDK de Media Services para .NET administra los errores transitorios que se pueden producir al realizar llamadas a la capa de la API de REST de Media Services.

## <a id="august_changes_13"></a>Versión de agosto de 2013
### <a name="aug_13_powershell_changes"></a>Cmdlets de PowerShell de Media Services incluidos en Herramientas de Azure SDK
Los siguientes cmdlets de PowerShell de Media Services se incluyen ahora en [Herramientas de Azure SDK](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Por ejemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por ejemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`
* New-AzureMediaServicesKey 
  
    Por ejemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por ejemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`

## <a id="june_changes_13"></a>Versión de junio de 2013
### <a name="june_13_general_changes"></a>Cambios en Media Services
Los cambios siguientes mencionados en esta sección son actualizaciones incluidas en las versiones de Media Services de junio de 2013:

* Posibilidad de vincular varias cuentas de almacenamiento a una cuenta de Servicios multimedia. 
    * StorageAccount
    * Asset.StorageAccountName and Asset.StorageAccount
* Posibilidad de actualizar Job.Priority. 
* Entidades y propiedades relacionadas con notificaciones: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Trabajo
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Cambios en el SDK de Media Services para .NET
Los siguientes cambios se incluyen en las versiones del SDK de Media Services de junio de 2013. El último SDK de Media Services está disponible en GitHub.

* A partir de la versión 2.3.0.0, el SDK de Media Services admite la vinculación de varias cuentas de almacenamiento a una cuenta de Media Services. Las siguientes API admiten esta característica:
  
    * El tipo IStorageAccount
    * La propiedad Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * La propiedad StorageAccount
    * La propiedad StorageAccountName
  
    Para más información, consulte [Administración de recursos de Media Services entre varias cuentas de almacenamiento](http://msdn.microsoft.com/library/azure/dn271889.aspx).
* API relacionadas con notificaciones. A partir de la versión 2.2.0.0, tiene la posibilidad de escuchar notificaciones de Azure Queue Storage. Para más información, consulte [Control de notificaciones de trabajos de Media Services](http://msdn.microsoft.com/library/azure/dn261241.aspx).
  
    * La propiedad Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * El tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * El tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Dependencia del SDK del cliente de Storage 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dependencia de OData 5.5 (Microsoft.Data.OData.dll)

## <a id="december_changes_12"></a>Versión de diciembre de 2012
### <a name="dec_12_dotnet_changes"></a>Cambios en el SDK de Media Services para .NET
* IntelliSense: se ha agregado documentación de IntelliSense que faltaba para muchos tipos.
* Microsoft.Practices.TransientFaultHandling.Core: se ha corregido un problema en el que el SDK tenía aún dependencia con una versión anterior de este ensamblado. El SDK hace referencia ahora a la versión 5.1.1209.1 de este ensamblado.

Correcciones de problemas encontrados en el SDK de noviembre de 2012:

* IAsset.Locators.Count: este recuento se notifica ahora correctamente en las nuevas interfaces IAsset después de que todos los localizadores se hayan eliminado.
* IAssetFile.ContentFileSize: este valor está ahora correctamente definido después de una carga por IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: esta propiedad se puede establecer ahora al crear un archivo de recurso. Anteriormente era de solo lectura.
* IAssetFile.Upload (filepath): se ha corregido un problema en el que el método de carga sincrónico mostraba el siguiente error al cargar varios archivos en el recurso. El error era "El servidor no pudo autenticar la solicitud. Asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma".
* IAssetFile.UploadAsync: se corrigió un problema que limitaba la carga simultánea de archivos a cinco archivos.
* AssetFile.UploadProgressChanged: este evento se proporciona ahora en el SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): ahora se proporciona esta sobrecarga del método.
* IAssetFile.DownloadAsync: se corrigió un problema que limitaba la descarga simultánea de archivos a cinco archivos.
* IAssetFile.Delete(): se ha corregido un problema en el que al llamar al método delete se puede producir una excepción si no se ha cargado ningún archivo para IAssetFile.
* Jobs: se ha corregido un problema en el que al encadenar una "tarea de MP4 a Smooth Streams" con una "tarea de protección de PlayReady" mediante una plantilla de trabajo, no se creaba ninguna tarea.
* EncryptionUtils.GetCertificateFromStore(): este método ya no produce una excepción de referencia nula debido a un error de búsqueda del certificado basado en problemas de configuración del certificado.

## <a id="november_changes_12"></a>Versión de noviembre de 2012
Los cambios mencionados en esta sección eran actualizaciones incluidas en el SDK de noviembre de 2012 (versión 2.0.0.0). Estos cambios pueden requerir la modificación o reescritura de cualquier código escrito para la versión preliminar del SDK de junio de 2012.

* Recursos
  
    * IAsset.Create(assetName) es la *única* función de creación de recursos. IAsset.Create ya no carga archivos como parte de la llamada al método. Use IAssetFile para cargar.
    * El método IAsset.Publish y el valor de enumeración AssetState.Publish se han eliminado del SDK de Media Services. Todo código que dependa de este valor se debe reescribir.
* FileInfo
  
    * Esta clase se ha eliminado y se ha sustituido por IAssetFile.
  
* IAssetFiles
  
    * IAssetFile sustituye a FileInfo y tiene un comportamiento diferente. Para usarla, cree una instancia del objeto IAssetFiles, seguida de una carga de archivos mediante el SDK de Media Services o el SDK de Storage. Se pueden usar las siguientes sobrecargas de IAssetFile.Upload:
  
        * IAssetFile.Upload(filePath): este método sincrónico bloquea el subproceso y solo se recomienda al cargar un único archivo.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): este método asincrónico es el mecanismo de carga recomendado. 
    
            Problema conocido: si usa el token de cancelación, se cancela la carga. Las tareas pueden tener muchos estados de cancelación. Debe capturar y gestionar correctamente las excepciones.
* Localizadores
  
    * Las versiones específicas del origen se han eliminado. El comando context.Locators.CreateSasLocator(asset, accessPolicy) específico de SAS se marcará como obsoleto o se eliminará de la disponibilidad general. Consulte la sección "Localizadores" en "Nuevas funcionalidades" para información sobre el comportamiento actualizado.

## <a id="june_changes_12"></a>Versión preliminar de junio de 2012
La siguiente funcionalidad era nueva en la versión de noviembre del SDK:

* Eliminación de entidades
  
    * Los objetos IAsset, IAssetFile, ILocator, IAccessPolicy e IContentKey se han eliminado ahora en el nivel de objeto, es decir, IObject.Delete(), en lugar de requerir una eliminación en la colección, que es cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    * Ahora los localizadores deben crearse mediante el método CreateLocator. Se deben usar los valores de enumeración LocatorType.SAS o LocatorType.OnDemandOrigin como argumento para el tipo específico de localizador que desea crear.
    * Se han agregado nuevas propiedades a los localizadores para que sea más fácil obtener identificadores URI utilizables para su contenido. Este rediseño de los localizadores proporciona una mayor flexibilidad para la futura extensibilidad de terceros y aumenta la facilidad de uso de las aplicaciones cliente multimedia.
* Compatibilidad con el método asincrónico
  
    * Se ha agregado compatibilidad asincrónica a todos los métodos.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[foro de MSDN de Azure Media Services]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referencia de la API de REST de Azure Media Services]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Detalle de precios de Media Services]: http://azure.microsoft.com/pricing/details/media-services/
[Metadatos de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadatos de salida]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Deliver content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Index media files with the Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Work with Media Services live streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Use AES-128 dynamic encryption and the key delivery service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Use PlayReady dynamic encryption and the license delivery service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Introducción a la plantilla de licencias de PlayReady de Media Services]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Stream storage-encrypted content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://portal.azure.com
[Empaquetado dinámico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de reintento en el SDK de Media Services para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia la transmisión en streaming EDIUS 7 a través de la nube]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Create overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitch video segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: http://msdn.microsoft.com/library/hh147631.aspx
[Connect to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handle Media Services job notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

