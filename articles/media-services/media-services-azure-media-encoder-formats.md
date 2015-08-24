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
	ms.date="08/11/2015" 
	ms.author="juliako"/>

#Formatos y códecs de Codificador multimedia de Azure

Los codificadores comprimen los archivos multimedia digitales mediante códecs. Los codificadores normalmente tienen varias configuraciones que le permiten especificar propiedades de multimedia generadas, por ejemplo, los códecs que se utilizan, el formato de los archivos, la resolución y la velocidad de bits. Los formatos de archivo son contenedores que contienen el vídeo comprimido, así como información sobre los códecs que se utilizaron para comprimir el video.

Los códecs tienen dos componentes: uno para comprimir archivos multimedia digitales para su transmisión y el otro para descomprimir los archivos multimedia digitales para su reproducción. Existen códecs de audio que comprimen y descomprimen el audio y códecs de vídeo que comprimen y descomprimen vídeo. Los códecs pueden utilizar compresión sin pérdida de información o compresión con pérdida de información. Los códecs de compresión sin pérdida de información conservan toda la información cuando se produce la compresión. Cuando se descomprime el archivo, el resultado es un archivo idéntico al archivo multimedia de entrada, lo que hace que los códecs de compresión sin pérdida de información sean muy apropiados para el archivado y el almacenamiento. Los códecs con pérdida de información pierden parte de la información al codificar y generan archivos más pequeños (que el original) a costa de la calidad del vídeo, y son muy apropiados para la transmisión a través de Internet. Los dos principales códecs que el Codificador multimedia de Azure utiliza para codificar son H.264 y VC-1. Puede haber disponibles otros códecs en nuestro ecosistema de socios de codificadores.

Es importante comprender la diferencia entre códecs y formatos de archivo. Los códecs son el software que implementa los algoritmos de compresión/descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido. Para obtener más información, consulte [Codificación frente a empaquetado](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Este documento contiene una lista de los formatos de archivo de importación y exportación más comunes que puede utilizar con el Codificador multimedia de Azure.


[Formatos de importación de Azure Media Encoder](#import_formats)

[Formatos de exportación de Azure Media Encoder](#export_formats)


##<a id="import_formats"></a>Formatos de importación de Azure Media Encoder 

En la siguiente sección se enumeran los códecs y el formato de archivo que se admiten para la importación.

###Códecs de vídeo

- H.264 (perfil de línea de base, perfil principal y perfil alto)
- MPEG-1 (incluido MPEG-PS)
- MPEG-2 (perfil simple y principal y perfil 4:2:2)
- MPEG-4 v2 (perfil visual simple y perfil avanzado simple)
- VC-1 (perfil simple, perfil principal y perfil avanzado)
- Windows Media Video (perfil simple, perfil principal y perfil avanzado)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
###Códecs de audio

- AC-3 (audio Dolby Digital)
- AAC (AAC-LC, HE-AAC v1 con núcleo AAC-LC y HE-AAC v2 con núcleo AAC-LC)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional y Windows Media Audio Lossless)

###Formatos de archivo de vídeo
 
Formato de archivo|Extensiones de archivo
---|---
3GPP, 3GPP2|.3gp, .3g2 y .3gp2
Formato Advanced Systems (ASF)|.asf
Alta definición de codificación de vídeo avanzado (AVCHD) [Secuencia de transporte MPEG-2]|.mts y .m2ts
Audio y vídeo intercalado (AVI)|.avi
Videograbadora digital MPEG-2 (MOD)|.mod
Archivo de secuencia de transporte de DVD (TS)|.ts
Archivo de objeto de video de DVD (VOB)|.vob
Archivo Expression Encoder Screen Capture Codec|.xesc
MP4|.mp4
Secuencia de sistema MPEG-1|.mpeg y .mpg
Archivo de vídeo MPEG-2|.m2v
Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)|.ismv
Windows Media Video (WMV)|.wmv


Se admiten algunos formatos no comprimidos. Para obtener más información, consulte [Formatos de vídeo no comprimido admitidos](#uncompressed)

###Formatos de archivo de audio

Formato de archivo|Extensiones de archivo
---|---
Audio AC-3 (Dolby Digital)|.ac3
Formato de archivo de intercambio de audio (AIFF)|.aiff
Formato Broadcast Wave|.bwf
MP3 (MPEG-1 Audio Layer 3)|.mp3
Audio MP4|.m4A
Audiolibro MPEG-4|.m4b
Archivo WAVE|.wav
Windows Media Audio|.wma

###Formatos de archivo de imagen

Formato de archivo|Extensiones de archivo
---|---
Mapa de bits|.bmp
GIF, GIF animado|.gif
JPEG|.jpeg y .jpg
PNG|.png
TIFF|.tif
WPF Canvas XAML|.xaml


##<a id="export_formats"></a>Formatos de exportación del Codificador multimedia de Azure

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

<!---HONumber=August15_HO7-->