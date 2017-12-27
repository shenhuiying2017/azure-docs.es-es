---
title: "Información general del escalado de procesamiento de medios | Microsoft Docs"
description: "Este tema ofrece una introducción al escalado de procesamiento de medios con on Azure Media Services."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: a82481c4995bfb078e88d7096dff37b52312a296
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="scaling-media-processing-overview"></a>Información general del escalado de procesamiento de medios
Esta página proporciona una introducción a cómo y por qué se debe escalar el procesamiento de medios. 

## <a name="overview"></a>Información general
Una cuenta de Media Services está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo**S1**. Para obtener más información, consulte el blog [Encoding Reserved Unit Types](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)(Codificación de tipos de unidad reservada).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente cuando finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

## <a name="choosing-between-different-reserved-unit-types"></a>Selección de los distintos tipos de unidad reservada
La siguiente tabla sirve de ayuda para tomar la decisión de elegir entre distintas velocidades de codificación. También incluye algunos casos de prueba comparativa y direcciones URL de SAS que puede usar para descargar vídeos en los que puede realizar sus propias pruebas:

| Escenarios | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| Caso de uso previsto |Codificación con velocidad de bits sencilla. <br/>Archivos con resolución SD o menor, no sujetos a limitación temporal y de bajo costo. |Codificación con velocidad de bits sencilla y múltiple.<br/>Uso normal para codificación SD y HD. |Codificación con velocidad de bits sencilla y múltiple.<br/>Vídeos con resolución Full HD y 4K. Codificación con respuesta más rápida, sujeta a limitación temporal. |
| Prueba comparativa |[Archivo de entrada: 5 minutos de duración, 640 x 360 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codificación de un archivo MP4 con velocidad de bits sencilla con la misma resolución tarda aproximadamente 11 minutos. |[Archivo de entrada: 5 minutos de duración, 1280 x 720 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codificación con el valor predeterminado H264 Single Bitrate 720p tardará aproximadamente 5 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 720p" tardará aproximadamente 11,5 minutos. |[Archivo de entrada: 5 minutos de duración, 1920 x 1080 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>La codificación con el valor predeterminado H264 Single Bitrate 1080p tardará aproximadamente 2,7 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 1080p" tarda aproximadamente 5,7 minutos. |

## <a name="considerations"></a>Consideraciones
> [!IMPORTANT]
> Revise las consideraciones descritas en esta sección.  
> 
> 

* Las unidades reservadas sirven para establecer paralelismos en todo el procesamiento multimedia, incluida la indexación de trabajos mediante Azure Media Indexer.  Sin embargo, a diferencia de la codificación, la indexación de los trabajos no se procesará más rápido con unidades reservadas de mayor rapidez.
* Si utiliza el grupo compartido, es decir, sin ninguna unidad reservada, las tareas de codificación tendrán el mismo rendimiento que con las RU S1. Sin embargo, no existe ningún límite superior para el tiempo que las tareas pueden estar en el estado en cola, y en un momento determinado, se estará ejecutando a lo sumo una tarea.
* Los siguientes centros de datos no ofrecen el tipo de unidad reservada **S2**: Sur de Brasil e India occidental.
* El siguiente centro de datos no ofrece el tipo de unidad reservada **S3**: India occidental.

## <a name="billing"></a>Facturación

Se le cobrará en función de los minutos reales de uso de las unidades reservadas de multimedia. Para obtener una explicación detallada, vea la sección de preguntas más frecuentes de la página de [precios de Media Services](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="quotas-and-limitations"></a>Cuotas y limitaciones
Para obtener información sobre las cuotas y limitaciones y sobre cómo abrir una incidencia de soporte técnico, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).

## <a name="next-step"></a>Paso siguiente
Llevar a cabo la tarea de escalado de procesamiento de medios con alguna de estas tecnologías: 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Para obtener la versión más reciente del SDK de Java y empezar a realizar desarrollos con Java, consulte [Introducción al SDK de cliente de Java para Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para descargar el SDK de PHP más reciente para Media Services, busque la versión 0.5.7 del paquete de Microsoft/WindowAzure en el [repositorio Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

