---
title: Información general del empaquetado dinámico | Microsoft Docs
description: El tema proporciona información general sobre el empaquetado dinámico.
author: Juliako
manager: erikre
editor: ''
services: media-services
documentationcenter: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako

---
# Empaquetado dinámico
## Información general
Servicios multimedia de Microsoft Azure puede usarse para proporcionar varios formatos de archivo de origen multimedia, formatos de streaming multimedia y formatos de protección de contenido a diversas tecnologías cliente (por ejemplo, iOS, XBOX, Silverlight y Windows 8). Estos clientes entienden distintos protocolos. Por ejemplo, iOS requiere un formato HTTP Live Streaming (HLS) V4, y Silverlight y Xbox requieren Smooth Streaming. Si tiene un conjunto de archivos MP4 (ISO Base Media 14496-12) de velocidad de bits adaptable (varias velocidades de bits) o un conjunto de archivos Smooth Streaming de velocidad de bits adaptable que desea ofrecer a los clientes que entienden MPEG DASH, HLS o Smooth Streaming, puede aprovechar el empaquetado dinámico de Servicios multimedia.

Con el empaquetado dinámico, lo único que debe hacer es crear un recurso que contenga un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable. Luego, según el formato especificado en la solicitud de manifiesto o fragmento, el servidor de streaming a petición se asegurará de que reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente.

El diagrama siguiente muestra el flujo de trabajo de codificación y empaquetado estático tradicionales.

![Codificación estática](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

El diagrama siguiente muestra el flujo de trabajo de empaquetado dinámico.

![Codificación dinámica](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

> [!NOTE]
> Para aprovechar al máximo el empaquetado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido. Para obtener más información, consulte [Escalación de un Servicio multimedia](media-services-portal-manage-streaming-endpoints.md).
> 
> 

## Escenario común
1. Cargar un archivo de entrada (llamado archivo intermedio). Por ejemplo, H.264, MP4 o WMV (para obtener la lista de formatos compatibles, vea [Formatos de Media Encoder Standard](media-services-media-encoder-standard-formats.md)).
2. Codificar el archivo intermedio en conjuntos MP4 de velocidad de bits adaptable H.264.
3. Publicar el recurso que contiene el conjunto MP4 de velocidad de bits adaptable mediante la creación del localizador a petición.
4. Compilar las direcciones URL de streaming para obtener acceso al contenido y hacer streaming de este.

## Preparación de recursos para el streaming dinámico
Si desea preparar el recurso para el streaming dinámico, tiene dos opciones:

1. [Cargar un archivo maestro](media-services-dotnet-upload-files.md).
2. [Usar el codificador Media Encoder Standard para producir conjuntos MP4 de velocidad de bits adaptable H.264](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [Transmita el contenido](media-services-deliver-content-overview.md).

O

1. Cargue archivos MP4 precodificados.

> [!NOTE]
> Esta opción no se recomienda.
> 
> 

1. [Valide los archivos precodificados](media-services-static-packaging.md#validating-adaptive-bitrate-mp4s-encoded-with-external-encoders).
2. [Transmita el contenido](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Formatos no compatibles con el empaquetado dinámico
El empaquetado dinámico no admite los siguientes formatos de archivo de origen:

* Archivos MP4 Dolby Digital.
* Archivos MP4 Dolby Digital Smooth.

## Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0921_2016-->