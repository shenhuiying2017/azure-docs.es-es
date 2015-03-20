<properties 
	pageTitle="Formatos y códecs de Codificador multimedia de Azure" 
	description="Este tema ofrece información general sobre los formatos y códecs de Codificador multimedia de Azure." 
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
	ms.date="02/27/2015" 
	ms.author="juliako"/>

# Formatos y códecs de Codificador multimedia de Azure

Los codificadores comprimen los archivos multimedia digitales mediante códecs. Los codificadores normalmente tienen varias configuraciones que le permiten especificar propiedades de multimedia generadas, por ejemplo, los códecs que se utilizan, el formato de los archivos, la resolución y la velocidad de bits. Los formatos de archivo son contenedores que contienen el vídeo comprimido, así como información sobre los códecs que se utilizaron para comprimir el video. 

Los códecs tienen dos componentes: uno para comprimir archivos multimedia digitales para su transmisión y el otro para descomprimir los archivos multimedia digitales para su reproducción. Existen códecs de audio que comprimen y descomprimen el audio y códecs de vídeo que comprimen y descomprimen vídeo. Los códecs pueden utilizar compresión sin pérdida de información o compresión con pérdida de información. Los códecs de compresión sin pérdida de información conservan toda la información cuando se produce la compresión. Cuando se descomprime el archivo, el resultado es un archivo idéntico al archivo multimedia de entrada, lo que hace que los códecs de compresión sin pérdida de información sean muy apropiados para el archivado y el almacenamiento. Los códecs con pérdida de información pierden parte de la información al codificar y generan archivos más pequeños (que el original) a costa de la calidad del vídeo, y son muy apropiados para la transmisión a través de Internet. Los dos principales códecs que el Codificador multimedia de Azure utiliza para codificar son H.264 y VC-1. Puede haber disponibles otros códecs en nuestro ecosistema de socios de codificadores.

Es importante comprender la diferencia entre códecs y formatos de archivo. Los códecs son el software que implementa los algoritmos de compresión/descompresión, mientras que los formatos de archivo son contenedores que contienen el vídeo comprimido. Para obtener más información, consulte [Codificación frente a empaquetado](http://blog-ndrouin.azurewebsites.net/streaming-media-terminology-explained/).

Este documento contiene una lista de los formatos de archivo de importación y exportación más comunes que puede utilizar con el Codificador multimedia de Azure.


[Formatos de importación del codificador multimedia ](#import_formats)

[Formatos de exportación del codificador multimedia ](#export_formats)


## <a id="import_formats"></a>Formatos de importación del codificador multimedia 

En la siguiente sección se enumeran los códecs y el formato de archivo que se admiten para la importación.

### Códecs de vídeo

- H.264 (perfil de línea de base, perfil principal y perfil alto)
- MPEG-1 (incluido MPEG-PS)
- MPEG-2 (perfil simple y principal y perfil 4:2:2)
- MPEG-4 v2 (perfil visual simple y perfil avanzado simple)
- VC-1 (perfil simple, perfil principal y perfil avanzado)
- Windows Media Video (perfil simple, perfil principal y perfil avanzado)
- DV (DVC, DVHD, DVSD, DVSL)
- Grass Valley HQ/HQX
 
### Códecs de audio

- AC-3 (audio Dolby Digital)
- AAC (AAC-LC, HE-AAC v1 con núcleo AAC-LC y HE-AAC v2 con núcleo AAC-LC)
- MP3
- Windows Media Audio 9 (Windows Media Audio Standard, Windows Media Audio Professional y Windows Media Audio Lossless)

### Formatos de archivo de vídeo
 
<table border="1">
<tr><th>Formato de archivo</th><th>Extensiones de archivo</th></tr>
<tr><td>3GPP, 3GPP2</td><td>.3gp, .3g2, .3gp2</td></tr>
<tr><td>Formato Advanced Systems (ASF)</td><td>.asf</td></tr>
<tr><td>Alta definición de codificación de vídeo avanzado (AVCHD) [Secuencia de transporte MPEG-2]</td><td>.mts, .m2ts</td></tr>
<tr><td>Audio y vídeo intercalado (AVI)</td><td>.avi</td></tr>
<tr><td>Videograbadora digital MPEG-2 (MOD)</td><td>.mod</td></tr>
<tr><td>Archivo de secuencia de transporte de DVD (TS)</td><td>.ts</td></tr>
<tr><td>Archivo de objeto de video de DVD (VOB)</td><td>.vob</td></tr>
<tr><td>Archivo Expression Encoder Screen Capture Codec</td><td>.xesc</td></tr>
<tr><td>MP4</td><td>.mp4</td></tr>
<tr><td>Secuencia de sistema MPEG-1</td><td>.mpeg, .mpg</td></tr>
<tr><td>Archivo de vídeo MPEG-2</td><td>.m2v</td></tr>
<tr><td>Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)</td><td>.ismv</td></tr>
<tr><td>Windows Media Video (WMV)</td><td>.wmv</td></tr>
</table>

Se admiten algunos formatos no comprimidos. Para obtener más información, consulte [Formatos de vídeo no comprimido admitidos](#uncompressed)

### Formatos de archivo de audio

<table border="1">
<tr><th>Formato de archivo</th><th>Extensiones de archivo</th></tr>
<tr><td>Audio AC-3 (Dolby Digital)</td><td>.ac3</td></tr>
<tr><td>Formato de archivo de intercambio de audio (AIFF)</td><td>.aiff</td></tr>
<tr><td>Formato Broadcast Wave</td><td>.bwf</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>.mp3</td></tr>
<tr><td>Audio MP4</td><td>.m4A</td></tr>
<tr><td>Audiolibro MPEG-4</td><td>.m4b</td></tr>
<tr><td>Archivo WAVE</td><td>.wav</td></tr>
<tr><td>Windows Media Audio</td><td>.wma</td></tr>   
</table>

### Formatos de archivo de imagen

<table border="1">
<tr><th>Formato de archivo</th><th>Extensiones de archivo</th></tr>
<tr><td>Mapa de bits</td><td>.bmp</td></tr>
<tr><td>GIF, GIF animado</td><td>.gif</td></tr>
<tr><td>JPEG</td><td>.jpeg, .jpg</td></tr>
<tr><td>PNG</td><td>.png</td></tr>
<tr><td>TIFF</td><td>.tif</td></tr>
<tr><td>WPF Canvas XAML</td><td>.xaml</td></tr>
</table>


## <a id="export_formats"></a>Formatos de exportación del codificador multimedia

La siguiente tabla enumera los códecs y los formatos de archivo compatibles para exportación.


<table border="1">
<tr><th>Formato de archivo</th><th>Códec de vídeo</th><th>Códec de audio</th></tr>
<tr><td>Windows Media (*.wmv; *.wma)</td><td>VC-1 (perfil avanzado, perfil principal y perfil simple)</td><td>Windows Media Audio Standard, Windows Media Audio Professional, Windows Media Audio Voice, Windows Media Audio Lossless</td></tr>
<tr><td>MP4 (*.mp4)</td><td>H.264 (perfil alto, perfil principal y perfil de base de línea)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2, Dolby Digital Plus</td></tr>
<tr><td>Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.1) (*.ismv; *.isma)</td><td>VC-1 (perfil avanzado)<br/><br/>
H.264 (perfil alto, perfil principal y perfil de base de línea)</td><td>Windows Media Audio Standard, Windows Media Audio Professional<br/><br/>
AAC-LC, HE-AAC v1, HE-AAC v2</td></tr>
</table>

Para conocer códecs y filtros adicionales admitidos en Servicios multimedia, consulte [Filtros de Windows DirectShow](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx).

## <a id="uncompressed"></a>Formatos de vídeo no comprimido admitidos 

Servicios multimedia de Azure proporciona compatibilidad para importar datos de vídeo no comprimido.

La siguiente es una lista parcial de los formatos no comprimidos admitidos.

<table border="1">
<tr><th>Formato de vídeo no comprimido</th><th>Descripción</th></tr>
<tr><td>Datos no comprimidos en formato YVU9 estándar</td><td>Un formato YUV planar. Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea, una muestra Y en cada línea vertical, una muestra U y V en cada cuarta línea vertical. 9 bits por píxel.</td></tr>
<tr><td>Datos en formato YUV 411</td><td>Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 12 bytes son 8 píxeles de imagen.</td></tr>
<tr><td>Datos en formato Y41P</td><td>Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U4, Y2, V4, Y3, Y4, Y5, Y6, Y7, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 12 bytes son 8 píxeles de imagen.</td></tr>
<tr><td>Datos en formato YUY2</td><td>Igual que UYVY, pero con distinto orden de píxeles. El orden de los bytes (el más bajo primero) es Y0, U0, Y1, V0, Y2, U2, Y3, V2, Y4, U4, Y5, V4, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen.</td></tr>
<tr><td>Datos en formato YVYU</td><td>Un formato YUV empaquetado. Igual que UYVY, pero con distinto orden de píxeles. El orden de los bytes (el más bajo primero) es Y0, V0, Y1, U0, Y2, V2, Y3, U2, Y4, V4, Y5, U4, donde el sufijo 0 indica el píxel más hacia la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen.</td></tr>
<tr><td>Datos en formato UYVY</td><td>Un formato YUV empaquetado. Una muestra Y en cada píxel, una muestra U y V en cada segundo píxel horizontalmente en cada línea; muestra de cada línea vertical. El más popular de los distintos formatos YUV 4:2:2. El orden de los bytes (el más bajo primero) es U0, Y0, V0, Y1, U2, Y2, V2, Y3, U4, Y4, V4, Y5, donde el sufijo 0 es el píxel más a la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 2 píxeles de imagen.</td></tr>
<tr><td>Datos en formato YUV 211</td><td>Un formato YUV empaquetado. Una muestra Y en cada segundo píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical. El orden de los bytes (el más bajo primero) es Y0, U0, Y2, V0, Y4, U4, Y6, V4, Y8, U8, Y10, V8, donde el sufijo 0 es el píxel más a la izquierda y los números cada vez mayores son píxeles que aumentan de izquierda a derecha. Cada bloque de 4 bytes son 4 píxeles de imagen.</td></tr>
<tr><td>Formato YUV 411 de Cirrus Logic Jr</td><td>Formato YUV 411 de Cirrus Logic Jr con menos de 8 bits por muestra Y, U y V. Una muestra Y en cada píxel, una muestra U y V en cada cuarto píxel horizontalmente en cada línea; muestra de cada línea vertical.</td></tr>
<tr><td>Formato YVU9 generado por Indeo</td><td>Formato YUV9 generado por Indeo con información adicional sobre las diferencias del último fotograma. 9,5 bits por píxel, pero informado como 9.</td></tr>
</table>

<!--HONumber=47-->
