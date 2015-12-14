<properties 
	pageTitle="Información general y comparación de codificadores multimedia a petición de Azure" 
	description="En este tema se proporciona información general y una comparación de los codificadores multimedia a petición de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/29/2015"  
	ms.author="juliako"/>

#Información general y comparación de codificadores multimedia a petición de Azure

##Información general sobre la codificación

Servicios multimedia de Azure ofrece varias opciones para la codificación de medios en la nube.

Cuando comience con Servicios multimedia, es importante comprender la diferencia entre códecs y formatos de archivo. Los códecs son el software que implementa los algoritmos de compresión/descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido.

Servicios multimedia proporciona paquetes dinámicos que permiten entregar contenido codificado MP4 de velocidad de bits adaptable o Smooth Streaming en formatos admitidos por Servicios multimedia (MPEG DASH, HLS, Smooth Streaming, HDS) sin tener que volver a empaquetar en estos formatos de streaming.

Para aprovecharse de los [paquetes dinámicos](media-services-dynamic-packaging-overview.md), deberá hacer lo siguiente:

- Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).
- Obtenga al menos la unidad de streaming a petición para el extremo de streaming desde el que planea entregar el contenido. Para obtener más información, consulte [Escalación de unidades reservadas de streaming a petición](media-services-manage-origins.md#scale_streaming_endpoints/).

Servicios multimedia admite los siguientes codificadores a petición que se describen en este artículo:

- **Media Encoder Estándar**
- **Codificador multimedia de Azure**
- **Flujo de trabajo del Codificador multimedia**

En este artículo se ofrece una breve introducción a los codificadores multimedia a petición y se proporcionan vínculos a artículos con información más detallada. También se proporciona una comparación de los codificadores.

Tenga en cuenta que, de forma predeterminada, cada cuenta de Servicios multimedia puede tener una tarea de codificación activa a la vez. Puede reservar unidades de codificación que permiten la ejecución simultánea de varias tareas de codificación, una para cada unidad reservada de codificación que ha adquirido. Para obtener información, consulte [Escalado de unidades de codificación](media-services-portal-encoding-units.md).

##Media Encoder Estándar

###Información general

Se recomienda usar el codificador Codificador multimedia estándar. Sin embargo, actualmente no se expone a través del Portal de Azure clásico.

En comparación con Codificador multimedia de Azure, este codificador admite más códecs y formatos de entrada y salida. Otras ventajas son:

- Mayor tolerancia hacia el modo en que se crea el archivo de entrada
- Mejor calidad del códec H.264 que Codificador multimedia de Azure
- Se basa en una canalización más reciente y flexible
- Es más sólido y resistente

###Modo de uso

[Codificación con Codificador multimedia estándar](media-services-dotnet-encode-with-media-encoder-standard.md)

###Formatos

[Códecs y formatos](media-services-media-encoder-standard-formats.md)

###Valores preestablecidos

Codificador multimedia estándar se configura mediante uno de los valores preestablecidos descritos [aquí](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

###Metadatos de entrada y salida

[Aquí](http://msdn.microsoft.com/library/azure/dn783120.aspx) se describen los metadatos de entrada de los codificadores.

[Aquí](http://msdn.microsoft.com/library/azure/dn783217.aspx) se describen los metadatos de salida de los codificadores.

###Miniatura

Para obtener información sobre cómo generar vistas en miniatura, vea [Generación de vistas en miniatura con Codificador multimedia estándar](media-services-dotnet-generate-thumbnail-with-mes.md).

###Superposiciones de vídeos y audio

Actualmente, no se admite.

###Consulte también

[El blog de Servicios multimedia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Codificador multimedia de Azure

###Información general

Codificador multimedia de Azure es uno de los codificadores compatibles con Servicios multimedia. A partir de julio de 2015, se recomienda usar [Codificador multimedia estándar](media-services-encode-asset.md#media_encoder_standard).

###Modo de uso

[Codificación con Codificador multimedia de Azure](media-services-dotnet-encode-asset.md)

###Formatos

[Códecs y formatos](media-services-azure-media-encoder-formats.md)

###Valores preestablecidos

El Codificador multimedia de Azure se configura con uno de los valores preestablecidos de codificador descritos [aquí](https://msdn.microsoft.com/library/azure/dn619392.aspx). También puede obtener los verdaderos archivos preestablecidos de Codificador multimedia de Azure [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Metadatos de entrada y salida

[Aquí](http://msdn.microsoft.com/library/azure/dn783120.aspx) se describen los metadatos de entrada de los codificadores.

[Aquí](http://msdn.microsoft.com/library/azure/dn783217.aspx) se describen los metadatos de salida de los codificadores.

###Miniatura

[Creación de una miniatura](https://msdn.microsoft.com/library/hh973624.aspx)

###Superposiciones de vídeos y audio

[Creación de superposiciones](media-services-azure-media-customize-ame-presets.md#creating-overlays)

###Convención de nomenclatura

[Modificación de los nombres de  
archivo de salida](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###Consulte también

[Codificación de elementos multimedia con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md)

##Flujo de trabajo del Codificador multimedia

###Información general

[Introducción de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###Modo de uso

El flujo de trabajo del Codificador multimedia Premium se configura mediante flujos de trabajo complejos. Los archivos de flujo de trabajo pueden crearse y actualizarse con la herramienta [Diseñador de flujo de trabajo](media-services-workflow-designer.md).

[Uso de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>Comparación de codificadores

###<a id="billing"></a>Medidor de facturación usado por cada codificador

Nombre de procesador multimedia|Precios aplicables|Notas
---|---|---
**Media Encoder Estándar** |ENCODER|Las tareas de codificación se cobrarán en función del tamaño de los activos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Codificador multimedia de Azure** |ENCODER|Las tareas de codificación se cobrarán en función del tamaño de los activos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Flujo de trabajo del Codificador multimedia** |CODIFICADOR PREMIUM|Las tareas de codificación se cobrarán en función del tamaño de los activos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR PREMIUM.


En esta sección se comparan las capacidades de codificación de **Codificador multimedia estándar**, **Codificador multimedia de Azure** y **Flujo de trabajo de Codificador multimedia premium**.


###Formatos de archivo/contenedor de entrada

Formatos de archivo/contenedor de entrada|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
Adobe® Flash® F4V |Sí|No |Sí
MXF/SMPTE 377M |Sí|Limitado|Sí
GXF |Sí|No |Sí
Secuencias de transporte MPEG-2 |Sí|Sí |Sí
Secuencias de programa MPEG-2 |Sí|Sí |Sí
MPEG-4/MP4 |Sí|Sí |Sí
Windows Media/ASF |Sí|Sí |Sí
AVI (sin comprimir de 8 bits/10 bits)|Sí|Sí |Sí
3GPP/3GPP2 |Sí|Sí |No
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|Sí|Sí|No
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|Sí|No|No
Matroska/WebM |Sí|No|No
QuickTime (.mov) |Sí|No|No

###Códecs de vídeo de entrada

Códecs de vídeo de entrada|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra |8 bits: 4:2:0 y 4:2:2|Solo 8 bits 4:2:0|Sí
Avid DNxHD (en MXF) |Sí|No|Sí
DVCPro/DVCProHD (en MXF) |Sí|No|Sí
JPEG2000 |Sí|No|Sí
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Hasta 422 Perfil|Hasta 422 Perfil|Sí
MPEG-1 |Sí|Sí|Sí
Windows Media Video/VC-1 |Sí|Sí|Sí
Canopus HQ/HQX |No|Sí|No
MPEG-4, parte 2 |Sí|No|No
[Theora](https://en.wikipedia.org/wiki/Theora) |Sí|No|No
Apple ProRes 422 |Sí|No|No
Apple ProRes 422 LT |Sí|No|No
Apple ProRes 422 HQ |Sí|No|No
Apple ProRes Proxy|Sí|No|No
Apple ProRes 4444 |Sí|No|No
Apple ProRes 4444 XQ |Sí|No|No

###Códecs de audio de entrada

Códecs de audio de entrada|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
AES (SMPTE 331M y 302M, AES3-2003) |No|No|Sí
Dolby® E |No|No|Sí
Dolby® Digital (AC3) |No|Sí|Sí
Dolby® Digital Plus (E-AC3) |No|No|Sí
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí|Sí
MPEG Layer 2|Sí|Sí|Sí
MP3 (MPEG-1 Audio Layer 3)|Sí|Sí|Sí
Windows Media Audio|Sí|Sí|Sí
WAV/PCM|Sí|Sí|Sí
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Sí|No|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Sí|No|No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Sí|No|No


###Formatos de archivo/contenedor de salida

Formatos de archivo/contenedor de salida|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
Adobe® Flash® F4V|No|No|Sí
MXF (OP1a, XDCAM y AS02)|No|No|Sí
DPP (incluido AS11)|No|No|Sí
GXF|No|No|Sí
MPEG-4/MP4|Sí|Sí|Sí
MPEG-TS|Sí|No|Sí
Windows Media/ASF|No|Sí|Sí
AVI (sin comprimir de 8 bits/10 bits)|No|No|Sí
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|No|Sí|Sí

###Códecs de vídeo de salida

Códecs de vídeo de salida|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)|Solo 8 bits 4:2:0|Solo 8 bits 4:2:0 hasta 1080p|Sí
Avid DNxHD (en MXF)|No|No|Sí
DVCPro/DVCProHD (en MXF)|No|No|Sí
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|No|No|Sí
MPEG-1|No|No|Sí
Windows Media Video/VC-1|No|Sí|Sí
Creación de miniaturas JPEG|No|Sí|Sí

###Códecs de audio de salida

Códecs de audio de salida|Media Encoder Estándar|Codificador multimedia de Azure|Flujo de trabajo del Codificador multimedia
---|---|---|---
AES (SMPTE 331M y 302M, AES3-2003)|No|No|Sí
Dolby® Digital (AC3)|No|Sí|Sí
Dolby® Digital Plus (E-AC3) hasta 7.1|No|Hasta 5.1|Sí
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí|Sí
MPEG Layer 2|No|No|Sí
MP3 (MPEG-1 Audio Layer 3)|No|No|Sí
Windows Media Audio|No|Sí|Sí




##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Artículos relacionados

- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=AcomDC_1203_2015-->