---
title: Comparación de codificadores multimedia a petición de Azure | Microsoft Docs
description: En esta tema se comparan las funcionalidades de codificación de **Media Encoder Standard** y **Flujo de trabajo premium de Media Encoder**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 13e8b3bcb7de4093116b3e2198b210950ac16d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparación de codificadores multimedia a petición de Azure

En esta tema se comparan las funcionalidades de codificación de **Media Encoder Standard** y **Flujo de trabajo premium de Media Encoder**.

## <a name="video-and-audio-processing-capabilities"></a>Funcionalidades de procesamiento de vídeo y audio

En la tabla siguiente se compara la funcionalidad entre Media Encoder Standard (MES) y el flujo de trabajo de Media Encoder Premium (MEPW). 

|Capacidad|Media Encoder Estándar|Flujo de trabajo del Codificador multimedia|
|---|---|---|
|Aplicación de la lógica condicional durante la codificación<br/>(por ejemplo, si la entrada es HD, entonces, codifique audio 5.1)|Sin |Sí|
|Subtítulos (CC)|Sin |[Sí](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Corrección de volumen profesional Dolby®](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> con Dialogue Intelligence™|Sin |Sí|
|Deshacer entrelazado, telecine inverso|Básica|Calidad de difusión|
|Detectar y quitar bordes negros <br/>(formato pillarbox y formato letterbox)|Sin |Sí|
|Generación de miniaturas|[Sí](media-services-dotnet-generate-thumbnail-with-mes.md)|[Sí](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Recorte y unión de vídeos|[Sí](media-services-advanced-encoding-with-mes.md#trim_video)|Sí|
|Superposiciones de audio o vídeo|[Sí](media-services-advanced-encoding-with-mes.md#overlay)|[Sí](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Superposiciones de gráficos|De orígenes de imagen|De orígenes de imagen y texto|
|Varias pistas de idiomas de audio|Limitado|[Sí](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Medidor de facturación usado por cada codificador
| Nombre de procesador multimedia | Precios aplicables | Notas |
| --- | --- | --- |
| **Media Encoder Estándar** |ENCODER |Las tareas de codificación se cobrarán en función de la duración total, en minutos, de todos los archivos multimedia producidos como salida, según la tarifa especificada [aquí][1], bajo la columna ENCODER. |
| **Flujo de trabajo del Codificador multimedia** |CODIFICADOR PREMIUM |Las tareas de Encoding se cobrarán en función de la duración total, en minutos, de todos los archivos multimedia producidos como salida, según la tarifa especificada [aquí][1], bajo la columna PREMIUM ENCODER. |

## <a name="input-containerfile-formats"></a>Formatos de archivo/contenedor de entrada
| Formatos de archivo/contenedor de entrada | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sí |Sí |
| MXF/SMPTE 377M |Sí |Sí |
| GXF |Sí |Sí |
| Secuencias de transporte MPEG-2 |Sí |Sí |
| Secuencias de programa MPEG-2 |Sí |Sí |
| MPEG-4/MP4 |Sí |Sí |
| Windows Media/ASF |Sí |Sí |
| AVI (sin comprimir de 8 bits/10 bits) |Sí |Sí |
| 3GPP/3GPP2 |Sí |Sin  |
| Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3) |Sí |Sin  |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sí |Sin  |
| Matroska/WebM |Sí |Sin  |
| QuickTime (.mov) |Sí |Sin  |

## <a name="input-video-codecs"></a>Códecs de vídeo de entrada
| Códecs de vídeo de entrada | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra |8 bits: 4:2:0 y 4:2:2 |Sí |
| Avid DNxHD (en MXF) |Sí |Sí |
| DVCPro/DVCProHD (en MXF) |Sí |Sí |
| JPEG2000 |Sí |Sí |
| MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10) |Hasta 422 Perfil |Sí |
| MPEG-1 |Sí |Sí |
| Windows Media Video/VC-1 |Sí |Sí |
| Canopus HQ/HQX |Sin  |Sin  |
| MPEG-4, parte 2 |Sí |Sin  |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sí |Sin  |
| Apple ProRes 422 |Sí |Sin  |
| Apple ProRes 422 LT |Sí |Sin  |
| Apple ProRes 422 HQ |Sí |Sin  |
| Apple ProRes Proxy |Sí |Sin  |
| Apple ProRes 4444 |Sí |Sin  |
| Apple ProRes 4444 XQ |Sí |Sin  |

## <a name="input-audio-codecs"></a>Códecs de audio de entrada
| Códecs de audio de entrada | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AES (SMPTE 331M y 302M, AES3-2003) |Sin  |Sí |
| Dolby® E |Sin  |Sí |
| Dolby® Digital (AC3) |Sin  |Sí |
| Dolby® Digital Plus (E-AC3) |Sin  |Sí |
| AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1) |Sí |Sí |
| MPEG Layer 2 |Sí |Sí |
| MP3 (MPEG-1 Audio Layer 3) |Sí |Sí |
| Windows Media Audio |Sí |Sí |
| WAV/PCM |Sí |Sí |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sí |Sin  |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Sí |Sin  |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sí |Sin  |

## <a name="output-containerfile-formats"></a>Formatos de archivo/contenedor de salida
| Formatos de archivo/contenedor de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| Adobe® Flash® F4V |Sin  |Sí |
| MXF (OP1a, XDCAM y AS02) |Sin  |Sí |
| DPP (incluido AS11) |Sin  |Sí |
| GXF |Sin  |Sí |
| MPEG-4/MP4 |Sí |Sí |
| MPEG-TS |Sí |Sí |
| Windows Media/ASF |Sin  |Sí |
| AVI (sin comprimir de 8 bits/10 bits) |Sin  |Sí |
| Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3) |Sin  |Sí |

## <a name="output-video-codecs"></a>Códecs de vídeo de salida
| Códecs de vídeo de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra) |Solo 8 bits 4:2:0 |Sí |
| HEVC (H.265; 8 bits y 10 bits;)  |Sin  |Sí |
| Avid DNxHD (en MXF) |Sin  |Sí |
| MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10) |Sin  |Sí |
| MPEG-1 |Sin  |Sí |
| Windows Media Video/VC-1 |Sin  |Sí |
| Creación de miniaturas JPEG |Sí |Sí |
| Creación de miniaturas PNG |Sí |Sí |
| Creación de miniaturas BMP |Sí |Sin  |

## <a name="output-audio-codecs"></a>Códecs de audio de salida
| Códecs de audio de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AES (SMPTE 331M y 302M, AES3-2003) |Sin  |Sí |
| Dolby® Digital (AC3) |Sin  |Sí |
| Dolby® Digital Plus (E-AC3) hasta 7.1 |Sin  |Sí |
| AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1) |Sí |Sí |
| MPEG Layer 2 |Sin  |Sí |
| MP3 (MPEG-1 Audio Layer 3) |Sin  |Sí |
| Windows Media Audio |Sin  |Sí |

>[!NOTE]
>Si utiliza la codificación en Dolby® Digital (AC3), la salida solo puede escribirse en un archivo ISO MP4.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artículos relacionados
* [Realización de tareas de codificación avanzadas mediante la personalización de valores preestablecidos de Media Encoder Estándar](media-services-custom-mes-presets-with-dotnet.md)
* [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
