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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Codificación de contenido a petición con Servicios multimedia de Azure

Este tema forma parte del [Flujo de trabajo de vídeo a petición de Servicios multimedia](media-services-video-on-demand-workflow.md).

##Información general

Servicios multimedia admite los codificadores siguientes:

- [Azure Media Encoder](#azure_media_encoder)
- [Flujo de trabajo del Codificador multimedia Premium](#media_encoder_premium_workflow) (vista previa pública)

En la [siguiente sección](#compare_encoders) se comparan las capacidades de codificación de ambos codificadores.

De forma predeterminada cada cuenta de Servicios multimedia puede tener una tarea de codificación activa a la vez. Puede reservar unidades de codificación que permiten la ejecución simultánea de varias tareas de codificación, una para cada unidad reservada de codificación que ha adquirido. Para obtener información acerca de cómo ampliar unidades de codificación, consulte los siguientes temas **Portal** y **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Formatos compatibles con el Codificador de Servicios multimedia](media-services-azure-media-encoder-formats.md): describe los formatos de archivo y secuencias compatibles con **Azure Media Encoder**.

**Azure Media Encoder** se configura con una de las cadenas preestablecidas del codificador descritas [aquí](https://msdn.microsoft.com/library/azure/dn619392.aspx). También puede obtener los verdaderos archivos preestablecidos de Azure Media Encoder [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Codifique con **Azure Media Encoder** mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Otros temas relacionados

[Empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx): describe cómo codificar a los MP4 de velocidad de bits adaptativa y ofrecer de forma dinámica Smooth Streaming, Apple HLS o MPEG-DASH.

[Control de los nombres de archivo de salida de Azure Media Encoder](https://msdn.microsoft.com/library/azure/dn303341.aspx): describe la convención de nomenclatura de archivos utilizada por el Azure Media Encoder y cómo modificar los nombres de archivo de salida.

[Codificación de elementos multimedia con Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): explica cómo codificar pistas de audio con codificación Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Flujo de trabajo del Codificador multimedia Premium (vista previa pública)

**Nota** El procesador multimedia del flujo de trabajo del Codificador multimedia Premium que se trata en este tema no está disponible en China.

[Formatos que admite el flujo de trabajo del Codificador multimedia Premium](media-services-premium-workflow-encoder-formats.md): trata los formatos de archivo y los códecs que admite el **flujo de trabajo del Codificador multimedia Premium**.

El **Flujo de trabajo del Codificador multimedia Premium** se configura mediante flujos de trabajo complejos. Los archivos de flujo de trabajo pueden crearse con la herramienta [Diseñador de flujo de trabajo](media-services-workflow-designer.md).

Puede obtener los archivos de flujo de trabajo predeterminados [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Codifique con el **Flujo de trabajo del Codificador multimedia Premium** con **.NET**. Para obtener más información, consulte [Codificación avanzada con el flujo de trabajo del Codificador multimedia Premium](media-services-encode-with-premium-workflow.md)
 

##<a id="compare_encoders"></a>Comparación de codificadores

En esta sección se comparan las capacidades de codificación de **Azure Media Encoder** y **flujo de trabajo de Codificador multimedia Premium**.

###Formatos de entrada

Formatos de archivo/contenedor de entrada

<table border="1">
<tr><th>Formatos de archivo/contenedor de entrada</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sí</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Sí</td><td>Limitado</td></tr>
<tr><td>GXF</td><td>Sí</td><td>No</td></tr>
<tr><td>Secuencias de transporte MPEG-2</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Secuencias de programa MPEG-2</td><td>Sí</td><td>Sí</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Windows Media/ASF</td><td>Sí</td><td>Sí</td></tr>
<tr><td>AVI (sin comprimir de 8 bits/10 bits)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Sí</td></tr>
<tr><td>Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)</td><td>No</td><td>Sí</td></tr>
</table>

Códecs de vídeo de entrada

<table border="1">
<tr><th>Códecs de vídeo de entrada</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra</td><td>Sí</td><td>Solo 8 bits 4:2:0</td></tr>
<tr><td>Avid DNxHD (en MXF)</td><td>Sí</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (en MXF)</td><td>Sí</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Sí</td><td>No</td></tr>
<tr><td>MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)</td><td>Sí</td><td>Hasta 422 Perfil</td></tr>
<tr><td>MPEG-1</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Sí</td></tr>
</table>

Códecs de audio de entrada

<table border="1">
<tr><th>Códecs de audio de entrada</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>AES (SMPTE 331M y 302M, AES3-2003)</td><td>Sí</td><td>No</td></tr>
<tr><td>Dolby® E</td><td>Sí</td><td>No</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Sí</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>MPEG Layer 2</td><td>Sí</td><td>Sí</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Windows Media Audio</td><td>Sí</td><td>Sí</td></tr>
<tr><td>WAV/PCM</td><td>Sí</td><td>Sí</td></tr>
</table>

###Formatos de salida

Formatos de archivo/contenedor de salida

<table border="1">
<tr><th>Formatos de archivo/contenedor de salida</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Sí</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM y AS02)</td><td>Sí</td><td>No</td></tr>
<tr><td>DPP (incluido AS11)</td><td>Sí</td><td>No</td></tr>
<tr><td>GXF</td><td>Sí</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Windows Media/ASF</td><td>Sí</td><td>Sí</td></tr>
<tr><td>AVI (sin comprimir de 8 bits/10 bits)</td><td>Sí</td><td>No</td></tr>
<tr><td>Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)</td><td>Sí</td><td>Sí</td></tr>
</table>

Códecs de vídeo de salida

<table border="1">
<tr><th>Códecs de vídeo de salida</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)</td><td>Sí</td><td>Solo 8 bits 4:2:0 hasta 1080p</td></tr>
<tr><td>Avid DNxHD (en MXF)</td><td>Sí</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (en MXF)</td><td>Sí</td><td>No</td></tr>
<tr><td>MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)</td><td>Sí</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Sí</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Creación de miniaturas JPEG</td><td>Sí</td><td>Sí</td></tr>
</table>

Códecs de audio de salida

<table border="1">
<tr><th>Códecs de audio de salida</th><th>Flujo de trabajo del Codificador multimedia</th><th>Codificador multimedia de Azure
</th></tr>
<tr><td>AES (SMPTE 331M y 302M, AES3-2003)</td><td>Sí</td><td>No</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) hasta 7.1</td><td>Sí</td><td>Hasta 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)</td><td>Sí</td><td>Sí</td></tr>
<tr><td>MPEG Layer 2</td><td>Sí</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Sí</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Sí</td><td>Sí</td></tr>
</table>
##Artículos relacionados

- [Introducción de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Uso de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->