<properties 
	pageTitle="Códecs y formatos de Media Encoder Standard" 
	description="En este tema se ofrece información general sobre los códecs y formatos de Azure Media Encoder Standard." 
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
	ms.date="07/08/2015" 
	ms.author="juliako"/>

#Códecs y formatos de Media Encoder Standard


Este documento contiene una lista de los formatos de archivo de importación y exportación más comunes que puede usar con Media Encoder Standard.


[Formatos de importación de Media Encoder ](#import_formats)

[Formatos de exportación del codificador multimedia](#export_formats)


##<a id="import_formats"></a>Formatos de importación de Media Encoder 

En la siguiente sección se enumeran los códecs y el formato de archivo que se admiten para la importación.


##Códecs de vídeo:

- MPEG-2
- H.264
- YUV420 sin comprimir o intermedio
- DNxHD
- VC-1/WMV9
- MPEG-4, parte 2
- JPEG 2000
- Theora

###Códecs de audio

- PCM
- AAC (AAC-LC, AAC-HE y AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Formatos

Formato de archivo|Extensiones de archivo
---|---
FLV (con códecs H.264 y AAC) |.flv
MP4/ISMV|*.ismv
MPEG2-PS, MPEG2-TS, 3GP|.ts, .ps, .3gp
MXF|.mxf
WMV/ASF|.mwv, .asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Formatos de exportación de Media Encoder

La siguiente tabla enumera los códecs y los formatos de archivo compatibles para exportación.


Formato de archivo|Códec de vídeo|Códec de audio
---|---|---
MP4 (*.mp4)<br/><br/>(incluidos los contenedores de MP4 de velocidad de bits múltiple) |H.264 (perfil alto, perfil principal y perfil de base de línea)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (perfil alto, perfil principal y perfil de base de línea)|AAC-LC, HE-AAC v1, HE-AAC v2 

##Consulte también

[Codificación de contenido a petición con Servicios multimedia de Azure](media-services-encode-asset.md)

<!---HONumber=August15_HO6-->