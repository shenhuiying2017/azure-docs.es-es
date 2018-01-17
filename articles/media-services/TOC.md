# [Información general](media-services-overview.md)
## [Escenarios y disponibilidad](scenarios-and-availability.md)
## [Conceptos](media-services-concepts.md)

# Introducción
## [Creación y administración de cuentas](media-services-portal-create-account.md)
## [Configuración de un entorno de desarrollo](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Uso de la autenticación de AAD para acceder a la API](media-services-use-aad-auth-to-access-ams-api.md)
### [Usar del portal para administrar la autenticación de AAD](media-services-portal-get-started-with-aad.md)
### [Acceso a la API con .NET](media-services-dotnet-get-started-with-aad.md)
### [Acceso a la API con REST](media-services-rest-connect-with-aad.md)
### [Uso de la CLI de Azure para crear y configurar la aplicación AAD](media-services-cli-create-and-configure-aad-app.md)
### [Uso de Azure PowerShell para crear y configurar la aplicación AAD](media-services-powershell-create-and-configure-aad-app.md)

## Entrega de vídeos a petición
### [portal de Azure](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Realización de streaming en vivo
### [portal de Azure](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedimientos
## administración
### Entidades
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Puntos de conexión de streaming](media-services-streaming-endpoints-overview.md)
#### [portal de Azure](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Storage
#### [Actualización de Media Services después de regenerar las claves de acceso de almacenamiento](media-services-roll-storage-access-keys.md)
#### [Administración de recursos en varias cuentas de Storage](meda-services-managing-multiple-storage-accounts.md)
### [Cuotas y limitaciones](media-services-quotas-and-limitations.md)
## [Configuración de Postman](media-rest-apis-with-postman.md)
### [Entorno](postman-environment.md)
### [Colección](postman-collection.md)
## Carga de contenido
### Carga de archivos en una cuenta
#### [portal de Azure](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Carga de archivos de gran tamaño con Aspera](media-services-upload-files-with-aspera.md)
### [Carga de archivos con StorSimple](media-services-upload-files-from-storsimple.md)
### [Copia de blobs existentes](media-services-copying-existing-blob.md)

## [Codificación de contenido](media-services-encode-asset.md)
### [Comparación de codificadores](media-services-compare-encoders.md)
### [Administración de la velocidad y la simultaneidad de la codificación](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Códecs y formatos de Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Uso de MES para generar automáticamente una escalera de velocidad de bits](media-services-autogen-bitrate-ladder-with-mes.md)
#### Codificación con Media Encoder Standard
##### [portal de Azure](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Codificación avanzada con MES](media-services-advanced-encoding-with-mes.md)
##### [Personalización de valores preestablecidos de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Generación de vistas en miniatura mediante Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Recorte de vídeos con Media Encoder Standard](media-services-crop-video.md)
#### Esquemas de MES
##### [Esquema de Media Encoder Standard](media-services-mes-schema.md)
##### [Metadatos de entrada](media-services-input-metadata-schema.md)
##### [Metadatos de salida](media-services-output-metadata-schema.md)
#### [Valores preestablecidos de MES](media-services-mes-presets-overview.md) 
##### [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Flujo de trabajo de Media Encoder Premium
#### [Códecs y formatos de flujo de trabajo de Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
#### Codificación con flujo de trabajo de Media Encoder Premium
##### [Flujo de trabajo de Media Encoder Premium](media-services-encode-with-premium-workflow.md)
##### [Tutoriales sobre el flujo de trabajo de Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Creación de flujos de trabajo de Encoding avanzado con el Diseñador de flujo de trabajo](media-services-workflow-designer.md)
##### [Flujo de trabajo premium con varias entradas](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Creación de una tarea que genera fragmentos fMP4](media-services-generate-fmp4-chunks.md)
### Procesadores de multimedia
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Códigos de error](media-services-encoding-error-codes.md)
### En desuso
#### [Cifrado y empaquetado estático](media-services-static-packaging.md)

## [Streaming en vivo](media-services-manage-channels-overview.md)
### [Codificadores locales](media-services-live-streaming-with-onprem-encoders.md)
#### [Codificadores locales recomendados](media-services-recommended-encoders.md)
#### [portal de Azure](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Streaming en vivo con codificador en la nube](media-services-manage-live-encoder-enabled-channels.md)
#### [portal de Azure](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Configuración de codificadores locales para usarlos con un codificador en la nube](media-services-live-encoders-overview.md)
#### [Codificador Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Codificador FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificador NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificador Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Control de operaciones de larga ejecución](media-services-dotnet-long-operations.md)
### [Especificación de ingesta en vivo de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)

## [Recorte del contenido](media-services-azure-media-clipper-overview.md)
### [Introducción](media-services-azure-media-clipper-getting-started.md)
### [Cargar vídeos](media-services-azure-media-clipper-load-assets.md)
### [Configuración de métodos abreviados de teclado](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [Configuración de la localización](media-services-azure-media-clipper-localization.md)
### [Envío de trabajos de recorte](media-services-azure-media-clipper-submit-job.md)
### [portal de Azure](media-services-azure-media-clipper-portal.md)

## [Protección del contenido](media-services-content-protection-overview.md)
### [Cifrado de almacenamiento](media-services-rest-storage-encryption.md)
### [Cifrado AES-128](media-services-protect-with-aes128.md)
### [PlayReady/Widevine para streaming](media-services-protect-with-playready-widevine.md)
### [FairPlay para streaming](media-services-protect-hls-with-fairplay.md)
### [PlayReady sin conexión para Windows 10](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [FairPlay sin conexión para iOS](media-services-protect-hls-with-offline-fairplay.md)
### [Widevine sin conexión para Android](offline-widevine-for-android.md)
### [Configuración en Azure Portal](media-services-portal-protect-content.md)
### [Entrega de licencias de DRM](media-services-deliver-keys-and-licenses.md)
### Creación de claves de contenido
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Introducción a las plantillas de licencias
#### [Plantilla de licencia de PlayReady](media-services-playready-license-template-overview.md)
#### [Plantilla de licencia de Widevine](media-services-widevine-license-template-overview.md)
### Configuración de directivas de entrega de recursos
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Configuración de la directiva de autorización de claves de contenido
#### [portal de Azure](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Transferencia de tokens de autenticación a AMS](media-services-pass-authentication-tokens.md)
### Diseños de referencia
#### [Diseño del sistema DRM híbrido](hybrid-design-drm-sybsystem.md)
#### [Diseño con DRM múltiple de referencia](media-services-cenc-with-multidrm-access-control.md)

## [Análisis](media-services-analytics-overview.md)
### [Análisis multimedia mediante Azure Portal](media-services-portal-analyze.md)
### [Proceso con Indexer 2](media-services-process-content-with-indexer2.md)
### [Proceso con Indexer](media-services-index-content.md)
#### [Valor predefinido de tarea](indexer-task-preset.md)
### [Proceso con Hyperlapse](media-services-hyperlapse-content.md)
### [Proceso con Face Detector](media-services-face-and-emotion-detection.md)
### [Proceso con Motion Detector](media-services-motion-detection.md)
### [Proceso con Face Redactor](media-services-face-redaction.md)
#### [Tutorial de Face Redactor](media-services-redactor-walkthrough.md)
### [Proceso con Video Thumbnails](media-services-video-summarization.md)
### [Proceso con OCR](media-services-video-optical-character-recognition.md)

## [Configuración de la telemetría](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Escala
### [Procesamiento multimedia](media-services-scale-media-processing-overview.md)
#### [portal de Azure](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Extremos de streaming
#### [portal de Azure](media-services-portal-scale-streaming-endpoints.md)

## [Entrega de contenido](media-services-deliver-content-overview.md)
### [Empaquetado dinámico](media-services-dynamic-packaging-overview.md)
### [Introducción a filtros y manifiestos dinámicos](media-services-dynamic-manifest-overview.md)
#### [Creación de filtros con .NET](media-services-dotnet-dynamic-manifest.md)
#### [Creación de filtros con REST](media-services-rest-dynamic-manifest.md)
### [Directiva de almacenamiento en caché de CDN en extensión de Media Services](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publicación de contenido
#### [portal de Azure](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Entrega mediante descarga](media-services-deliver-asset-download.md)
### [Escenario de streaming de conmutación por error](media-services-implement-failover.md)

## Consumo
### [Reproducción de medios con los reproductores existentes](media-services-playback-content-with-existing-players.md)
### [Reproducción de medios con Media Player](media-services-develop-video-players.md)
### Otras opciones de reproducción
#### [Aplicación Smooth Streaming de la Tienda Windows](media-services-build-smooth-streaming-apps.md)
#### [Aplicación HTML5 con DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Reproductores de Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Inserción de anuncios en el lado del cliente](media-services-inserting-ads-on-client-side.md)
### [Licencias del kit de portabilidad de cliente de Microsoft Smooth Streaming](media-services-sspk.md)

## Integrate
### [Uso de Azure Functions con Media Services](media-services-dotnet-how-to-use-azure-functions.md)
### [Uso de Azure Functions con ejemplos de Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Supervisión
### Consulta del progreso del trabajo
#### [REST](media-services-rest-check-job-progress.md)
#### [portal de Azure](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Supervisión de notificaciones de trabajos con Queue Storage](media-services-dotnet-check-job-progress-with-queues.md)
### [Supervisión de notificaciones de trabajos con webhooks](media-services-dotnet-check-job-progress-with-webhooks.md)

## Solución de problemas
### [Preguntas más frecuentes](media-services-frequently-asked-questions.md)
### [Guía de solución de problemas para el streaming en vivo](media-services-troubleshooting-live-streaming.md)
### [Códigos de error](media-services-error-codes.md)
### [Lógica de reintento](media-services-retry-logic-in-dotnet-sdk.md)

# Referencia
## [Ejemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (Service Management)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)  

# Recursos
## [Comunidad de Azure Media Services](media-services-community.md)
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Precios](https://azure.microsoft.com/pricing/details/media-services/)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Notas de la versión](media-services-release-notes.md)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=media-services)
