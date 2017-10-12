---
title: "Códecs y formatos del Flujo de trabajo del Codificador multimedia premium | Microsoft Docs"
description: "En este tema se proporciona información general de códecs y formatos de formatos de flujo de trabajo de Media Encoder premium."
services: media-services
documentationcenter: 
author: juliako
manager: erik43
editor: 
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: e18de2adc9aac585d6890dd7b43a54f1a0ca177e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Códecs y formatos de flujo de trabajo del Codificador multimedia Premium
> [!NOTE]
> Si tiene preguntas sobre el codificador premium, envíe un correo a mepd en Microsoft.com.
> 
> El procesador multimedia del flujo de trabajo premium de Media Encoder del que se habla en este tema no está disponible en China. 
> 
> 

Este documento contiene una lista de los formatos de archivo de entrada y salida y los códecs que son compatibles con la vista previa pública del **Flujo de trabajo del Codificador multimedia premium** .

[Códecs y formatos de entrada del flujo de trabajo del Codificador multimedia Premium](#input_formats)

[Códecs y formatos de salida del flujo de trabajo del Codificador multimedia Premium](#output_formats)

**Flujo de trabajo del Codificador multimedia premium** admite los subtítulos que se describen en [esta](#closed_captioning) sección. 

## <a id="input_formats"></a>Códecs y formatos de entrada de flujo de trabajo del Codificador multimedia Premium
En la sección siguiente se enumeran los códecs y formatos de archivo que admite este procesador multimedia como entrada.

### <a name="input-containerfile-formats"></a>Formatos de archivo/contenedor de entrada
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Secuencias de transporte MPEG-2
* Secuencias de programa MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (sin comprimir de 8 bits/10 bits)

### <a name="input-video-codecs"></a>Códecs de vídeo de entrada
* AVC 8 bits/10 bits, hasta 4:2:2, incluido AVCIntra
* Avid DNxHD (en MXF)
* DVCPro/DVCProHD (en MXF)
* JPEG2000
* MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Códecs de audio de entrada
* AES (SMPTE 331M y 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>Códecs y formatos de salida de flujo de trabajo del Codificador multimedia Premium
En la sección siguiente se enumeran los códecs y formatos de archivo que se admiten como salida de este procesador multimedia.

### <a name="output-containerfile-formats"></a>Formatos de archivo/contenedor de salida
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM y AS02)
* DPP (incluido AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (sin comprimir de 8 bits/10 bits)
* Formato de archivo de streaming con velocidad de transmisión adaptable (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Códecs de vídeo de salida
* AVC (H.264; 8 bits; hasta Perfil alto, Nivel 5.2; 4K Ultra HD; AVC Intra)
* Avid DNxHD (en MXF)
* DVCPro/DVCProHD (en MXF)
* MPEG-2 (hasta 422 Perfil y Nivel alto; incluidas variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs® y D10)
* MPEG-1
* Windows Media Video/VC-1
* Creación de miniaturas JPEG

### <a name="output-audio-codecs"></a>Códecs de audio de salida
* AES (SMPTE 331M y 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) hasta 7.1
* AAC (AAC-LC, AAC-HE y AAC-HEv2; hasta 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media Audio

>[!NOTE]
>Si utiliza la codificación en Dolby® Digital (AC3), la salida solo puede escribirse en un archivo ISO MP4.

## <a id="closed_captioning"></a>Compatibilidad con subtítulos
En la entrada, el **flujo de trabajo del Codificador multimedia Premium** admite:

1. Archivos SCC
2. Archivos SMPTE-TT
3. CEA-608/CEA-708: llevados como datos de usuario (mensajes SEI de secuencias elementales H.264, ATSC/53 y SCTE20) o llevdosa como datos auxiliares en archivos MXF/GXF
4. Archivos de subtítulos STL

En la salida, están disponibles las siguientes opciones:

1. Traducción de CEA-608 a CEA-708
2. Transferencia CEA-608/CEA-708 (incrustado en mensajes SEI de secuencias elementales de H.264 o transportado como datos auxiliares en archivos MXF)
3. SCC
4. Texto sincronizado SMPTE (de origen CEA-608 por SMPTE RP2052; incluida la creación de archivos DFXP)
5. Archivo de subtítulos SRT
6. Secuencias de subtítulos DVB

Nota: no todos los formatos de salida anteriores se admiten para la entrega mediante streaming en Servicios multimedia de Azure.

## <a name="known-issues"></a>Problemas conocidos
Si el vídeo de entrada no contiene subtítulos, el recurso de salida seguirá conteniendo un archivo TTML vacío. 

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

