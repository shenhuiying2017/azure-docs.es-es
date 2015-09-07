<properties 
	pageTitle="Formatos y códecs de Codificador multimedia de Azure"
	description="Este tema ofrece información general sobre los formatos y códecs de Azure Media Encoder."
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
	ms.date="08/24/2015"
	ms.author="juliako"/>

#Formatos y códecs de Codificador multimedia de Azure

Este documento contiene una lista de los formatos de archivo de entrada y salida más comunes y códecs que puede usar con Codificador multimedia de Azure.


##Formatos de archivo de vídeo de entrada (contenedores)
 
Formato de archivo (extensiones de archivo)|Compatible
---|---
3GPP, 3GPP2 (.3gp, .3g2, .3gp2) |Sí
Formato Advanced Systems (ASF) (.asf) |Sí
Alta definición de codificación de vídeo avanzada (AVCHD) [Secuencia de transporte MPEG-2] (.mts, .m2ts) |Sí
Audio y vídeo intercalado (AVI) (.avi) |Sí
Videograbadora digital MPEG-2 (MOD) (.mod) |Sí
Archivo de secuencia de transporte de DVD (TS) (.ts) |Sí
Archivo de objeto de vídeo de DVD (VOB) (.vob) |Sí
Archivo Expression Encoder Screen Capture Codec (.xesc) |Sí
MP4 (*.mp4) |Sí
Secuencia de sistema MPEG-1 (.mpeg, .mpg) |Sí
Archivo de vídeo MPEG-2 (.m2v) |Sí
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3) (.ismv) |Sí
Windows Media Video (WMV) (.wmv) |Sí
Adobe® Flash® F4V |No		
MXF/SMPTE 377M |Limitado 
GXF |No		 
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|No
Matroska/WebM |No

Se admiten algunos formatos no comprimidos. Para obtener más información, consulte [Formatos de vídeo no comprimido admitidos](#uncompressed)

##Formatos de archivo de audio de entrada

Formato de archivo (extensiones de archivo)|Compatible
---|---
Audio AC-3 (Dolby Digital) (.ac3)|Sí
Formato de archivo de intercambio de audio (AIFF) (.aiff)|Sí
Formato Broadcast Wave (.bwf)|Sí
MP3 (MPEG-1 Audio Layer 3) (.mp3)|Sí
Audio MP4 (.mp4)|Sí
Audiolibro MPEG-4 (.m4b)|Sí
Archivo WAVE (.wav)|Sí
Windows Media Audio (.wma)|Sí


##Códecs de vídeo de entrada

Códecs de vídeo de entrada|Compatible
---|--- 
H.264 (perfil de línea de base, perfil principal y perfil alto) |Sí
AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra |Solo 8 bits 4:2:0
Avid DNxHD (en MXF) |No
DVCPro/DVCProHD (en MXF) |No
JPEG2000 |No
MPEG-2 (perfil simple y principal y perfil 4:2:2) |Hasta 4:2:2 Perfil
MPEG-1 (incluido MPEG-PS) |Sí
Windows Media Video/VC-1 |Sí
Canopus HQ/HQX |Sí
MPEG-4 v2 (perfil visual simple y perfil avanzado simple) |Sí
[Theora](https://en.wikipedia.org/wiki/Theora) |No
VC-1 (perfil simple, perfil principal y perfil avanzado) |Sí
Windows Media Video (perfil simple, perfil principal y perfil avanzado) |Sí
DV (DVC, DVHD, DVSD, DVSL) |Sí
Grass Valley HQ/HQX |Sí
 

##Códecs de audio de entrada

Códecs de audio de entrada|Compatible
---|---
AES (SMPTE 331M y 302M, AES3-2003) |No
Dolby® E |No
Dolby® Digital (AC3) |Sí
Dolby® Digital Plus (E-AC3) |No
AAC (AAC-LC, HE-AAC v1 con núcleo AAC-LC y HE-AAC v2 con núcleo AAC-LC; hasta 5.1)|Sí
MPEG Layer 2|Sí|Sí|Sí
MP3 (MPEG-1 Audio Layer 3)|Sí
Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional y Windows Media Audio Lossless) |Sí
WAV/PCM|Sí
[FLAC](https://en.wikipedia.org/wiki/FLAC)|No
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |No
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|No


##Formatos de archivo de imagen de entrada

Formato de archivo (extensiones de archivo) | Compatible
---|---
Mapa de bits (.bmp) | Sí
GIF, GIF animado (.gif)| Sí
JPEG (.jpeg, .jpg)| Sí
PNG (.png)| Sí
TIFF (.tif)| Sí
WPF Canvas XAML (.xaml)| Sí


##Códecs y formatos de salida

La siguiente tabla enumera los códecs y los formatos de archivo compatibles para exportación.

Formato de archivo|Códec de vídeo|Códec de audio
---|---|---
Windows Media (*.wmv; *.wma)|VC-1 (perfil avanzado, perfil principal y perfil simple)|Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless
MP4 (*.mp4)|H.264 (perfil alto, perfil principal y perfil de base de línea)|AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.1) (*.ismv; *.isma)|VC-1 (perfil avanzado)<p>H.264 (perfil alto, perfil principal y perfil de base de línea) |Windows Media Audio Standard, Windows Media Audio Professional<p><p>AAC-LC, HE-AAC v1, HE-AAC v2

Para conocer códecs y filtros adicionales admitidos en Servicios multimedia, consulte [Filtros de Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

##<a id="uncompressed"></a>Formatos de vídeo no comprimido admitidos 

Servicios multimedia de Azure proporciona compatibilidad para importar datos de vídeo no comprimido.

La siguiente es una lista parcial de los formatos no comprimidos admitidos.

Formato de vídeo no comprimido|Descripción
---|---
Datos no comprimidos en formato YVU9 estándar|Un formato YUV planar. Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea, una muestra Y en cada línea vertical, una muestra U y V en cada cuarta línea vertical. 9 bits por píxel.
Datos en formato YUV 411|Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 12 bytes son 8 píxeles de imagen.
Datos en formato Y41P|Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 12 bytes son 8 píxeles de imagen.
Datos en formato YUY2|Igual que UYVY, pero con distinto orden de píxeles. El orden de los bytes (el más bajo primero) es Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen
Datos en formato YVYU|Un formato YUV empaquetado. Igual que UYVY, pero con distinto orden de píxeles. El orden de los bytes (el más bajo primero) es Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen
Datos en formato UYVY|Un formato YUV empaquetado. Una muestra Y en cada píxel, una muestra U y V en cada segundo píxel horizontalmente en cada línea; muestra de cada línea vertical. El más popular de los distintos formatos YUV 4:2:2. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, donde el sufijo 0 es el píxel más a la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen
Datos en formato YUV 211|Un formato YUV empaquetado. Una muestra Y en cada segundo píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, donde el sufijo 0 es el píxel más a la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 4 píxeles de imagen.
Formato YUV 411 de Cirrus Logic Jr|Formato YUV 411 de Cirrus Logic Jr con menos de 8 bits por muestra Y, U y V. Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical.
Formato YVU9 generado por Indeo|Formato YUV9 generado por Indeo con información adicional sobre las diferencias del último fotograma. 9,5 bits por píxel, pero informado como 9.

<!---HONumber=August15_HO9-->