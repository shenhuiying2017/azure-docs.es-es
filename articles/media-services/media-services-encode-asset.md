<properties 
	pageTitle="Codificación de contenido a petición con Servicios multimedia de Azure" 
	description="Este tema proporciona información general de codificación de contenido a petición con Servicios multimedia." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"  
	ms.author="juliako"/>

#Codificación de contenido a petición con Servicios multimedia de Azure

Este tema forma parte del [Flujo de trabajo de vídeo a petición de Servicios multimedia](media-services-video-on-demand-workflow.md).

##Información general

Servicios multimedia admite los codificadores siguientes:

- [Media Encoder Estándar](#media_encoder_standard)
- [Codificador multimedia de Azure](#azure_media_encoder)
- [Flujo de trabajo del Codificador multimedia](#media_encoder_premium_workflow)

En la [siguiente sección](#compare_encoders) se comparan las capacidades de codificación de los codificadores admitidos.

De forma predeterminada cada cuenta de Servicios multimedia puede tener una tarea de codificación activa a la vez. Puede reservar unidades de codificación que permiten la ejecución simultánea de varias tareas de codificación, una para cada unidad reservada de codificación que ha adquirido. Para obtener información acerca de cómo ampliar unidades de codificación, consulte los siguientes temas **Portal** y **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Media Encoder Estándar

[Formatos compatibles con Media Encoder Estándar](media-services-media-encoder-standard-formats.md): describe los formatos de archivo y secuencias compatibles con **Media Encoder Estándar**.

**Media Encoder Estándar** se configura mediante uno de los valores preestablecidos del codificador descritos [aquí](http://go.microsoft.com/fwlink/?LinkId=618336) o unos valores personalizados basados en[estos](http://go.microsoft.com/fwlink/?LinkId=618336) valores preestablecidos.

Para obtener más información, consulte [este blog](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Formatos compatibles con el Codificador de Servicios multimedia](media-services-azure-media-encoder-formats.md): describe los formatos de archivo y secuencias compatibles con **Azure Media Encoder**.

**Azure Media Encoder** se configura con una de las cadenas preestablecidas del codificador descritas [aquí](https://msdn.microsoft.com/library/azure/dn619392.aspx). También puede obtener los verdaderos archivos preestablecidos de Azure Media Encoder [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

###Ejemplo

Codifique con **Azure Media Encoder** mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Otros temas relacionados

[Empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx): describe cómo codificar los MP4 de velocidad de bits adaptativa y ofrecer de forma dinámica Smooth Streaming, Apple HLS o MPEG-DASH.

[Control de los nombres de archivo de salida de codificador de Servicios multimedia](https://msdn.microsoft.com/library/azure/dn303341.aspx): describe la convención de nomenclatura de archivos utilizada por el Codificador multimedia de Azure y cómo modificar los nombres de archivo de salida.

[Codificación de elementos multimedia con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): explica cómo codificar pistas de audio con codificación Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Flujo de trabajo premium de codificación de medios 

**Nota** El procesador multimedia del flujo de trabajo del Codificador multimedia Premium que se trata en este tema no está disponible en China.

[Formatos que admite el flujo de trabajo premium de codificación de medios](media-services-premium-workflow-encoder-formats.md): se describen los formatos de archivo y los códecs que admite el **flujo de trabajo premium de codificación de medios**.

### Diseñador de flujo de trabajo

El **flujo de trabajo premium de codificación de medios** se configura mediante flujos de trabajo complejos. Los archivos de flujo de trabajo pueden crearse con la herramienta [Diseñador de flujo de trabajo](media-services-workflow-designer.md).

Puede obtener los archivos de flujo de trabajo predeterminado [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

###Ejemplo
Codifique con el **flujo de trabajo premium de codificación de medios** con **.NET**. Para obtener más información, consulte [Codificación avanzada con el flujo de trabajo premium de codificación de medios](media-services-encode-with-premium-workflow.md)
 

##<a id="compare_encoders"></a>Comparación de codificadores

###<a id="billing"></a>Medidor de facturación usado por cada codificador

Nombre de procesador multimedia|Precios aplicables|Notas
---|---|---
**Windows Azure Media Encoder** |CODIFICADOR HEREDADO|Las tareas de codificación se cobrarán en función de la suma de los tamaños de los recursos de entrada y de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR HEREDADO.
**Codificador multimedia de Azure** |ENCODER|Las tareas de codificación se cobrarán en función del tamaño de los recursos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Media Encoder Estándar** |ENCODER|Las tareas de codificación se cobrarán en función del tamaño de los recursos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR.
**Flujo de trabajo del Codificador multimedia** |CODIFICADOR PREMIUM|Las tareas de codificación se cobrarán en función del tamaño de los recursos de salida, en GB, a la velocidad especificada [aquí][1], bajo la columna CODIFICADOR PREMIUM.



En esta sección se comparan las capacidades de codificación del **Codificador multimedia de Azure**, el **Flujo de trabajo premium de codificación de medios** y la **Codificación de medios estándar**.


###Formatos de entrada

Formatos de archivo/contenedor de entrada

Formatos de archivo/contenedor de entrada|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
Adobe® Flash® F4V|Sí|No|Sí
MXF/SMPTE 377M|Sí|Limitado|Sí
GXF|Sí|No|Sí
Secuencias de transporte MPEG-2|Sí|Sí|Sí
Secuencias de programa MPEG-2|Sí|Sí|Sí
MPEG-4/MP4|Sí|Sí|Sí
Windows Media/ASF|Sí|Sí|Sí
AVI (sin comprimir de 8 bits/10 bits)|Sí|Sí|Sí
3GPP/3GPP2|No|Sí|Sí
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|No|Sí|Sí
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|No|No|Sí
Matroska/WebM|No|No|Sí

Códecs de vídeo de entrada

Códecs de vídeo de entrada|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra|Sí|Solo 8 bits 4:2:0|8 bits: 4:2:0 y 4:2:2
Avid DNxHD (en MXF)|Sí|No|Sí
DVCPro/DVCProHD (en MXF)|Sí|No|Sí
JPEG2000|Sí|No|Sí
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Sí|Hasta 422 Perfil|Hasta 422 Perfil
MPEG-1|Sí|Sí|Sí
Windows Media Video/VC-1|Sí|Sí|Sí
Canopus HQ/HQX|No|Sí|No
MPEG-4, parte 2|No|No|Sí
[Theora](https://en.wikipedia.org/wiki/Theora)|No|No|Sí

Códecs de audio de entrada

Códecs de audio de entrada|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
AES (SMPTE 331M y 302M, AES3-2003)|Sí|No|No
Dolby® E|Sí|No|No
Dolby® Digital (AC3)|Sí|Sí|No
Dolby® Digital Plus (E-AC3)|Sí|No|No
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí|Sí
MPEG Layer 2|Sí|Sí|Sí
MP3 (MPEG-1 Audio Layer 3)|Sí|Sí|Sí
Windows Media Audio|Sí|Sí|Sí
WAV/PCM|Sí|Sí|Sí
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|No|No|Sí
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |No|No|Sí
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|No|No|Sí

###Formatos de salida

Formatos de archivo/contenedor de salida

Formatos de archivo/contenedor de salida|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
Adobe® Flash® F4V|Sí|No|No
MXF (OP1a, XDCAM y AS02)|Sí|No|No
DPP (incluido AS11)|Sí|No|No
GXF|Sí|No|No
MPEG-4/MP4|Sí|Sí|Sí
MPEG-TS|Sí|No|Sí
Windows Media/ASF|Sí|Sí|No
AVI (sin comprimir de 8 bits/10 bits)|Sí|No|No
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|Sí|Sí|No

Códecs de vídeo de salida

Códecs de vídeo de salida|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)|Sí|Solo 8 bits 4:2:0 hasta 1080p|Solo 8 bits 4:2:0
Avid DNxHD (en MXF)|Sí|No|No
DVCPro/DVCProHD (en MXF)|Sí|No|No
MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)|Sí|No|No
MPEG-1|Sí|No|No
Windows Media Video/VC-1|Sí|Sí|No
Creación de miniaturas JPEG|Sí|Sí|No

Códecs de audio de salida

Códecs de audio de salida|Flujo de trabajo del Codificador multimedia|Codificador multimedia de Azure|Media Encoder Estándar
---|---|---|---
AES (SMPTE 331M y 302M, AES3-2003)|Sí|No|No
Dolby® Digital (AC3)|Sí|Sí|No
Dolby® Digital Plus (E-AC3) hasta 7.1|Sí|Hasta 5.1|No
AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)|Sí|Sí|Sí
MPEG Layer 2|Sí|No|No
MP3 (MPEG-1 Audio Layer 3)|Sí|No|No
Windows Media Audio|Sí|Sí|No

##Artículos relacionados

- [Introducción de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Uso de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO7-->