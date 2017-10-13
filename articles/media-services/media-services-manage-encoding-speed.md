---
title: "Administración de la velocidad y la simultaneidad de la codificación con Azure Media Services | Microsoft Docs"
description: "En este artículo se proporciona una breve introducción sobre cómo puede administrar la velocidad y la simultaneidad de las trabajos y las tareas de codificación con Azure Media Services."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 0463904fd9bf1138587d0d214e572ddd38cc2184
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Administración de la velocidad y la simultaneidad de la codificación

En este artículo se proporciona una breve introducción sobre cómo puede administrar la velocidad y la simultaneidad de las trabajos y las tareas de codificación.

## <a name="overview"></a>Información general

En Media Services, el valor de **Tipo de unidad reservada** determina la velocidad con que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo**S1**. En el tema sobre el [escalado de unidades de codificación](media-services-scale-media-processing-overview.md), se muestra una tabla que ayuda a elegir entre distintas velocidades de codificación.

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con **Unidades reservadas**. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente cuando finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

Para ver información detallada y ejemplos que muestran cómo escalar unidades de codificación, consulte [este](media-services-scale-media-processing-overview.md) tema.

## <a name="next-step"></a>Paso siguiente

[Escalar unidades de codificación](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

