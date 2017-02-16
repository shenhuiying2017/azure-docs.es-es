---
title: "Comparación de codificadores multimedia a petición de Azure | Microsoft Docs"
description: "En esta tema se comparan las funcionalidades de codificación de **Media Encoder Standard** y **Flujo de trabajo premium de Media Encoder**."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: dca8087230b261f14e876e49b9b0fb12579b96d0


---

# <a name="comparison-of-azure-on-demand-media-encoders"></a>Comparación de codificadores multimedia a petición de Azure

En esta tema se comparan las funcionalidades de codificación de **Media Encoder Standard** y **Flujo de trabajo premium de Media Encoder**.

### <a name="a-idbillingabilling-meter-used-by-each-encoder"></a><a id="billing"></a>Medidor de facturación usado por cada codificador
| Nombre de procesador multimedia | Precios aplicables | Notas |
| --- | --- | --- |
| **Media Encoder Estándar** |ENCODER |Las tareas de codificación se cobrarán en función de la duración total, en minutos, de todos los archivos multimedia producidos como salida, según la tarifa especificada [aquí][1], bajo la columna ENCODER. |
| **Flujo de trabajo de Media Encoder Premium** |CODIFICADOR PREMIUM |Las tareas de codificación se cobrarán en función de la duración total, en minutos, de todos los archivos multimedia producidos como salida, según la tarifa especificada [aquí][1], bajo la columna PREMIUM ENCODER. |

### <a name="input-containerfile-formats"></a>Formatos de archivo/contenedor de entrada
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
| 3GPP/3GPP2 |Sí |No |
| Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3) |Sí |No |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Sí |No |
| Matroska/WebM |Sí |No |
| QuickTime (.mov) |Sí |No |

### <a name="input-video-codecs"></a>Códecs de vídeo de entrada
| Códecs de vídeo de entrada | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra |8 bits: 4:2:0 y 4:2:2 |Sí |
| Avid DNxHD (en MXF) |Sí |Sí |
| DVCPro/DVCProHD (en MXF) |Sí |Sí |
| JPEG2000 |Sí |Sí |
| MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10) |Hasta 422 Perfil |Sí |
| MPEG-1 |Sí |Sí |
| Windows Media Video/VC-1 |Sí |Sí |
| Canopus HQ/HQX |No |No |
| MPEG-4, parte 2 |Sí |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Sí |No |
| Apple ProRes 422 |Sí |No |
| Apple ProRes 422 LT |Sí |No |
| Apple ProRes 422 HQ |Sí |No |
| Apple ProRes Proxy |Sí |No |
| Apple ProRes 4444 |Sí |No |
| Apple ProRes 4444 XQ |Sí |No |

### <a name="input-audio-codecs"></a>Códecs de audio de entrada
| Códecs de audio de entrada | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AES (SMPTE 331M y 302M, AES3-2003) |No |Sí |
| Dolby® E |No |Sí |
| Dolby® Digital (AC3) |No |Sí |
| Dolby® Digital Plus (E-AC3) |No |Sí |
| AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1) |Sí |Sí |
| MPEG Layer 2 |Sí |Sí |
| MP3 (MPEG-1 Audio Layer 3) |Sí |Sí |
| Windows Media Audio |Sí |Sí |
| WAV/PCM |Sí |Sí |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Sí |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Sí |No |

### <a name="output-containerfile-formats"></a>Formatos de archivo/contenedor de salida
| Formatos de archivo/contenedor de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| Adobe® Flash® F4V |No |Sí |
| MXF (OP1a, XDCAM y AS02) |No |Sí |
| DPP (incluido AS11) |No |Sí |
| GXF |No |Sí |
| MPEG-4/MP4 |Sí |Sí |
| MPEG-TS |Sí |Sí |
| Windows Media/ASF |No |Sí |
| AVI (sin comprimir de 8 bits/10 bits) |No |Sí |
| Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3) |No |Sí |

### <a name="output-video-codecs"></a>Códecs de vídeo de salida
| Códecs de vídeo de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra) |Solo 8 bits 4:2:0 |Sí |
| Avid DNxHD (en MXF) |No |Sí |
| MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10) |No |Sí |
| MPEG-1 |No |Sí |
| Windows Media Video/VC-1 |No |Sí |
| Creación de miniaturas JPEG |No |Sí |

### <a name="output-audio-codecs"></a>Códecs de audio de salida
| Códecs de audio de salida | Media Encoder Estándar | Flujo de trabajo del Codificador multimedia |
| --- | --- | --- |
| AES (SMPTE 331M y 302M, AES3-2003) |No |Sí |
| Dolby® Digital (AC3) |No |Sí |
| Dolby® Digital Plus (E-AC3) hasta 7.1 |No |Sí |
| AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1) |Sí |Sí |
| MPEG Layer 2 |No |Sí |
| MP3 (MPEG-1 Audio Layer 3) |No |Sí |
| Windows Media Audio |No |yes |


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artículos relacionados
* [Realización de tareas de codificación avanzadas mediante la personalización de valores preestablecidos de Media Encoder Estándar](media-services-custom-mes-presets-with-dotnet.md)
* [Cuotas y limitaciones](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/



<!--HONumber=Jan17_HO2-->


