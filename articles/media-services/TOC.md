# [Información general](media-services-overview.md)
## [Conceptos](media-services-concepts.md)
## [Precios](https://azure.microsoft.com/pricing/details/media-services/)
## [Notas de la versión](media-services-release-notes.md)
# Primeros pasos
## [Creación y administración de cuentas](media-services-portal-create-account.md)
## [Configuración de un entorno de desarrollo](media-services-set-up-computer.md)
## Vídeo a petición
### [Portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Streaming en directo
### [Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Procedimientos
## Administrar
### [Administración puntos de conexión de streaming en el portal](media-services-portal-manage-streaming-endpoints.md)
### Administrar entidades
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Administración de cuentas con PowerShell](media-services-manage-with-powershell.md)
### [Recorte de vídeos con Media Encoder Standard](media-services-crop-video.md)
### [Actualización de Media Services después de revertir las claves de acceso de almacenamiento](media-services-roll-storage-access-keys.md)
### [Cuotas y limitaciones](media-services-quotas-and-limitations.md)
### Filtros
#### [Creación de filtros con SDK de .NET de Azure Media Services](media-services-dotnet-dynamic-manifest.md)
#### [Codificación de un recurso mediante Media Encoder Standard](media-services-rest-encode-asset.md)
### Conexión mediante programación
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST](media-services-rest-connect-programmatically.md)

## Carga de contenido
### Carga de archivos en una cuenta
#### [Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Copia de blobs existentes](media-services-copying-existing-blob.md)

## Codificación
### [Contenido](media-services-encode-asset.md)
#### Codificación de un recurso mediante Media Encoder Standard
##### [Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [Generación de vistas en miniatura mediante Media Encoder Standard con .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [Codificación avanzada](media-services-advanced-encoding-with-mes.md)
##### [Flujo de trabajo premium de codificación de medios](media-services-encode-with-premium-workflow.md)
##### [Tutoriales sobre el flujo de trabajo premium de codificación multimedia](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Creación de flujos de trabajo de codificación avanzada con el Diseñador de flujo de trabajo](media-services-workflow-designer.md)
##### [Flujo de trabajo premium con varias entradas](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### Esquemas 
#####[Media Encoder Standard](media-services-mes-schema.md)
#####[Metadatos de entrada](media-services-input-metadata-schema.md)
#####[Metadatos de salida](media-services-output-metadata-schema.md)

#### Codificadores heredados
##### [Uso de Azure Media Packager](media-services-static-packaging.md)

### [Transmisiones en vivo](media-services-manage-channels-overview.md)
#### [Codificadores locales](media-services-live-streaming-with-onprem-encoders.md)
#### Tutoriales sobre codificadores locales
##### [Portal](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [Streaming en vivo con codificador en la nube](media-services-manage-live-encoder-enabled-channels.md)
#### Tutoriales sobre codificadores en la nube
##### [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [Configuración de codificadores locales para usarlos con un codificador en la nube](media-services-live-encoders-overview.md)
#### [Control de operaciones de larga ejecución](media-services-dotnet-long-operations.md)
#### [Especificación de ingesta en vivo de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)
#### [Empaquetado dinámico](media-services-dynamic-packaging-overview.md)

### Procesamiento multimedia
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)

### Configuración de codificadores para una transmisión en vivo con velocidad de bits única
#### [Codificador Elemental Live](media-services-configure-elemental-live-encoder.md)
#### [Codificador FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificador NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificador Wirecast](media-services-configure-wirecast-live-encoder.md)

## [Protección](media-services-content-protection-overview.md)
### [Configuración de Content Protection en el portal](media-services-portal-protect-content.md)
### [Configuración de la clave sin cifrado AES 128 bits para transmisiones](media-services-protect-with-aes128.md)
### [Cifrado de contenido con cifrado de Storage mediante la API de REST de AMS](media-services-rest-storage-encryption.md)
### [Introducción a las plantillas de licencias de PlayReady de Media Services](media-services-playready-license-template-overview.md)
### [Entrega de licencia DRM](media-services-deliver-keys-and-licenses.md)
### [Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure](media-services-licenses-partner-integration.md)
### [Uso de cifrado dinámico común de PlayReady o Widevine.](media-services-protect-with-drm.md)
### [Uso de Azure Media Services para transmitir contenido HLS protegido con Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [CENC con varios DRM y Access Control: diseño e implementación de referencia en Azure y Azure Media Services](media-services-cenc-with-multidrm-access-control.md)

### Entrega de recursos
#### Configuración de directivas de entrega de recursos
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Creación de claves de contenido
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Configuración de la directiva de autorización de claves de contenido
#### [Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [Análisis](media-services-analytics-overview.md)
### [Proceso con Indexer 2](media-services-process-content-with-indexer2.md)
### [Proceso con Indexer](media-services-index-content.md)
### [Proceso con Hyperlapse](media-services-hyperlapse-content.md)
### [Proceso con Face Detector](media-services-face-and-emotion-detection.md)
### [Proceso con Motion Detector](media-services-motion-detection.md)
### [Proceso con censura de rostros](media-services-face-redaction.md)
### [Proceso con Video Thumbnails](media-services-video-summarization.md)
### [Proceso con OCR](media-services-video-optical-character-recognition.md)

## Escala
### [Procesamiento multimedia](media-services-scale-media-processing-overview.md)
#### [Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### Extremos de streaming
#### [Portal](media-services-portal-scale-streaming-endpoints.md)

## [Entrega de contenido](media-services-deliver-content-overview.md)
### [Introducción a filtros y manifiestos dinámicos](media-services-dynamic-manifest-overview.md)
### Crear filtros
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST](media-services-rest-dynamic-manifest.md)
### Publicación de contenido
#### [Portal](media-services-portal-publish.md)
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

## Integrate
### [Directiva de almacenamiento en caché de CDN en extensión de Media Services](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [Licencias del kit de portabilidad de cliente de Microsoft® Smooth Streaming](media-services-sspk.md)
### [Administración de recursos en varias cuentas de Storage](meda-services-managing-multiple-storage-accounts.md)
### [Uso de Axinom para entregar licencias de Widevine a Azure Media Services](media-services-axinom-integration.md)
### [Uso de castLabs para entregar licencias de Widevine a Azure Media Services](media-services-castlabs-integration.md)
### [Introducción a las plantillas de licencias de Widevine](media-services-widevine-license-template-overview.md)

## Supervisión
### Consulta del progreso del trabajo
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Queue Storage para supervisar notificaciones de trabajos](media-services-dotnet-check-job-progress-with-queues.md)

## Solución de problemas
### [Preguntas más frecuentes](media-services-frequently-asked-questions.md)
### [Guía de solución de problemas para el streaming en vivo](media-services-troubleshooting-live-streaming.md)
###[Códigos de error](media-services-error-codes.md)
###[Lógica de reintento](media-services-retry-logic-in-dotnet-sdk.md)

# Referencia
## [SDK de .NET de Media Services](media-services-dotnet-how-to-use.md)
## [API de REST de Media Services](media-services-rest-how-to-use.md)
## [Códecs y formatos de flujo de trabajo del Codificador multimedia Premium](media-services-premium-workflow-encoder-formats.md)
## [Códecs y formatos de Media Encoder Standard](media-services-media-encoder-standard-formats.md)

# Temas relacionados
## [Comunidad de Azure Media Services](media-services-community.md)











<!--HONumber=Nov16_HO2-->


