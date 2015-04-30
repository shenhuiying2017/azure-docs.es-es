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
	ms.date="03/05/2015" 
	ms.author="juliako"/>

#Codificación de contenido a petición con Servicios multimedia de Azure

Este tema forma parte del [flujo de trabajo de vídeo a petición de Servicios multimedia](media-services-video-on-demand-workflow.md).

##Información general

Servicios multimedia admite los codificadores siguientes:

- [Codificador multimedia de Azure](#azure_media_encoder)
- [Flujo de trabajo del Codificador multimedia](#media_encoder_premium_workflow) (vista previa pública)

La [sección siguiente](#compare_encoders) Compara las capacidades de codificación de ambos codificadores.

De forma predeterminada cada cuenta de Servicios multimedia puede tener una tarea de codificación activa a la vez. Puede reservar unidades de codificación que permiten la ejecución simultánea de varias tareas de codificación, una para cada unidad reservada de codificación que ha adquirido. Para obtener información acerca de cómo ampliar unidades de codificación, vea los siguientes temas **Portal** y **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Codificador multimedia de Azure

[Formatos compatibles con Codificador de Servicios multimedia](media-services-azure-media-encoder-formats.md): describe los formatos de archivo y secuencias compatibles con **Codificador multimedia de Azure**.

**Codificador multimedia de Azure** se configura con una de las cadenas preestablecidas del codificador descritas [aquí](https://msdn.microsoft.com/library/azure/dn619392.aspx). También puede obtener los verdaderos archivos preestablecidos de Codificador multimedia de Azure [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Codifique con el **Codificador de Servicios multimedia** mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

####Otros temas relacionados

[Empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx): describe cómo codificar a los MP4 de velocidad de bits adaptativa y ofrecer de forma dinámica Smooth Streaming, Apple HLS o MPEG-DASH.

[Control los nombres de archivo de salida de Codificador de servicio multimedia](https://msdn.microsoft.com/library/azure/dn303341.aspx): describe la convención de nomenclatura de archivos utilizada por el Codificador multimedia de Azure y cómo modificar los nombres de archivo de salida.

[Codificación de elementos multimedia con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) : explica cómo codificar pistas de audio con codificación Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Flujo de trabajo del Codificador multimedia Premium (vista previa pública)

**Nota** El procesador multimedia de flujo de trabajo del Codificador multimedia Premium que se trata en este tema no está disponible en China.

[Formatos que admite el flujo de trabajo del Codificador multimedia Premium](media-services-premium-workflow-encoder-formats.md) : trata los formatos de archivo y códecs admitidos por el **flujo de trabajo del Codificador multimedia Premium**.

El **flujo de trabajo del Codificador multimedia Premium** se configura mediante flujos de trabajo complejos. Los archivos de flujo de trabajo pueden crearse con [Diseñador de flujo de trabajo](media-services-workflow-designer.md) . 

Puede obtener los archivos de flujo de trabajo predeterminados [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Codifique con el **flujo de trabajo del Codificador multimedia Premium** con **.NET**. Para obtener más información, consulte [Codificación avanzada con el flujo de trabajo del Codificador multimedia Premium (vista previa pública)](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Comparación de codificadores

En esta sección se comparan las capacidades de codificación de **Codificador multimedia de Azure** y **flujo de trabajo de Codificador multimedia Premium**.

###Formatos de entrada

Formatos de archivo/contenedor de entrada

<table border="1">
<tr><th>Input Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Yes</td><td>Limited</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 Transport Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-2 Program Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Yes</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>No</td><td>Yes</td></tr>
</table>

Códecs de vídeo de entrada

<table border="1">
<tr><th>Input Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-bit/10-bit, up to 4:2:2, including AVCIntra</td><td>Yes</td><td>Only 8bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>Up to 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Yes</td></tr>
</table>

Códecs de audio de entrada

<table border="1">
<tr><th>Input Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) E</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3)</td><td>Yes</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
<tr><td>WAV/PCM</td><td>Yes</td><td>Yes</td></tr>
</table>

###Formatos de salida

Formatos de archivo/contenedor de salida

<table border="1">
<tr><th>Output Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM and AS02)</td><td>Yes</td><td>No</td></tr>
<tr><td>DPP (including AS11)</td><td>Yes</td><td>No</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>No</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>Yes</td><td>Yes</td></tr>
</table>

Códecs de vídeo de salida

<table border="1">
<tr><th>Output Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-bit; up to High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Yes</td><td>Only 8bit 4:2:0 up to 1080p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>JPEG thumbnail creation</td><td>Yes</td><td>Yes</td></tr>
</table>

Códecs de audio de salida

<table border="1">
<tr><th>Output Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3) up to 7.1</td><td>Yes</td><td>Up to 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
</table>
##Artículos relacionados

- [Introducción de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Uso de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)


<!--HONumber=52-->