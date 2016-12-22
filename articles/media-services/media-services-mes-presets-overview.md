---
title: Valores preestablecidos de las tareas de MES (Media Encoder Standard) | Microsoft Docs
description: "En este tema se proporciona información general de los valores preestablecidos de las tareas de MES (Media Encoder Standard)."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 0124c884d3d805a407041e8ecbc5961209a9653d
ms.openlocfilehash: 708444a1b2569cf794ce1f6d8d6c97c7d9e339b7


---

# <a name="task-presets-for-mes-media-encoder-standard"></a>Valores preestablecidos de las tareas de MES (Media Encoder Standard)

`Media Encoder Standard` define un conjunto de valores predeterminados de codificación que puede usar al crear trabajos de codificación.  
  
 Las cadenas XML o JSON presentes deben basarse en los valores preestablecidos que se muestran en los archivos siguientes. Puede pasar valores preestablecidos con valores personalizados al codificador (los valores deben ser válidos). Para obtener explicaciones de lo que cada elemento significa en estos nombres predeterminados y los valores válidos para cada elemento, vea el tema sobre el [esquema de Media Encoder Standard](media-services-mes-schema.md).  
  
> [!NOTE]
>  Cuando se usa un valor preestablecido para codificaciones de 4K, debe obtener el tipo de unidad reservada `S3`. Para obtener más información, consulte [Escalación de codificación](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units).  
  
 Cuando se trabaja con Media Encoder Standard, la rotación está habilitada de forma predeterminada. Si se ha registrado el vídeo en un smartphone u otro dispositivo móvil en modo vertical, estos valores, de forma predeterminada, los hará girar en modo horizontal antes de la codificación (salvo cuando se utiliza Azure Media Encoder, donde la rotación de vídeo es una operación manual, tal y como se documenta en [este](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/) blog, en "Video Rotation" [Rotación de vídeos]).  
  
 Los nombres preestablecidos se asignan a valores de este mismo tipo que se muestra en los siguientes temas.  
  
 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) genera un conjunto de 8 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 6000 kbps y audio AAC 5.1.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) genera un conjunto de 8 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 6000 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) genera un conjunto de 8 archivos MP4 alineados con GOP, que abarcan desde 200 kbps hasta 8500 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) genera un conjunto de 5 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 1900 kbps y audio AAC 5.1.  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) genera un conjunto de 5 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 1900 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) genera un conjunto de 12 archivos MP4 alineados con GOP, que abarcan desde 1000 kbps hasta 20000 kbps y AAC 5.1.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) genera un conjunto de 12 archivos MP4 alineados con GOP, que abarcan desde 1000 kbps hasta 20 000 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) genera un conjunto de 8 archivos MP4 alineados con GOP, que abarcan desde 200 kbps hasta 8500 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) genera un conjunto de 5 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 1600 kbps y AAC 5.1.  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) genera un conjunto de 5 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 1600 kbps y audio AAC estéreo.  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) genera un conjunto de 6 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 3400 kbps y audio AAC 5.1.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) genera un conjunto de 6 archivos MP4 alineados con GOP, que abarcan desde 400 kbps hasta 3400 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) genera un único archivo MP4 con una velocidad de bits de 6750 kbps y audio AAC 5.1.  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) genera un único archivo MP4 con una velocidad de bits de 6750 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) genera un único archivo MP4 con una velocidad de bits de 18 000 kbps y audio AAC 5.1.  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) genera un único archivo MP4 con una velocidad de bits de 18 000 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) genera un único archivo MP4 con una velocidad de bits de 18 000 kbps y audio AAC 5.1.  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) genera un único archivo MP4 con una velocidad de bits de 18 000 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) genera un único archivo MP4 con una velocidad de bits de 2200 kbps y audio AAC 5.1.  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) genera un único archivo MP4 con una velocidad de bits de 2200 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) genera un único archivo MP4 con una velocidad de bits de 4500 kbps y audio AAC 5.1.  
  
 [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) genera un único archivo MP4 con una velocidad de bits de 2000 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) genera un único archivo MP4 con una velocidad de bits de 4500 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) genera un único archivo MP4 con una velocidad de bits de 500 kbps y audio AAC estéreo.  
  
 [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) genera un único archivo MP4 con una velocidad de bits de 56 kbps y audio AAC estéreo.  
  
 Para obtener más información relacionada con codificadores de Media Services, consulte [Información general y comparación de codificadores multimedia a petición de Azure](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/).


<!--HONumber=Nov16_HO5-->


