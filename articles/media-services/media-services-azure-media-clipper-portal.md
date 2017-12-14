---
title: Uso de Azure Media Clipper en el portal | Microsoft Docs
description: "Creación de clips con Azure Media Clipper en Azure Portal"
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Creación de clips con Azure Media Clipper en el portal
Puede usar Azure Media Clipper en el portal para crear clips a partir de activos de las cuentas de Media Services. Para comenzar, navegue a la cuenta de Media Services en el portal. Luego, seleccione la pestaña **Subclip**.

En la pestaña **Subclip**, puede comenzar a crear clips. En el portal, Clipper carga MP4 con velocidad de bits única, MP4 con múltiples velocidades de bits y archivos activos que se publican con un localizador de streaming válido. Los activos no publicados no se cargan.

Clipper actualmente está en su versión preliminar pública. Para acceder a Clipper en Azure Portal, vaya a esta [página de vista previa pública](https://portal.azure.com/?feature.subclipper=true).

En la imagen siguiente se ilustra la página de aterrizaje de Clipper en su cuenta de Media Services: ![Azure Media Clipper en Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Producción de clips
Para crear un clip, arrastre y suelte un activo en la interfaz de clip. Si se conocen los tiempos de marcas, puede especificarlos manualmente en la interfaz. De lo contrario, reproduzca el recurso o arrastre el cabezal de reproducción para encontrar el tiempo de marca de entrada y de marca de salida deseado. Si estos valores no se proporcionan, el clip comienza desde el principio y sigue hasta el final del activo de entrada, respectivamente.

Para navegar con precisión de fotograma o precisión de GOP, use los botones de avanzar fotograma/avanzar GOP o de retroceder fotograma/retroceder GOP. Para crear clips respecto de varios activos, arrastre y suelte varios activos a la interfaz de clip desde el panel de selección de activos. Puede seleccionar y reordenar los activos en la interfaz como prefiera. El panel de selección de activos proporciona la duración de los activos, el tipo y los metadatos de resolución de cada activo. Cuando varios activos se concatenan en conjunto, considere la resolución de origen de cada archivo de entrada. Si las resoluciones de origen son distintas, la entrada de menor resolución se mejora para coincidir con la resolución del activo de mayor resolución. Para obtener una vista previa de la salida del trabajo de creación de clip, seleccione el botón de vista previa y el clip se reproducirá desde las marcas de tiempo seleccionadas.

## <a name="producing-dynamic-manifest-filters"></a>Generación de filtros de manifiesto dinámico
Los [filtros de manifiesto dinámico](https://azure.microsoft.com/blog/dynamic-manifest/) describen un conjunto de reglas en función de los atributos de manifiesto y la escala de tiempo de activo. Estas reglas determinan cómo el punto de conexión de streaming manipula la lista de reproducción de salida (manifiesto). El filtro se puede usar para cambiar los segmentos que se transmiten para su reproducción. Los filtros que genera Clipper son filtros locales y son específicos para el activo de origen. A diferencia de los clips emitidos, los filtros no son activos nuevos y no requieren un trabajo de codificación para su producción. Se pueden crear rápidamente a través del [SDK de .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) o de la [API de REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), sin embargo, solo tienen precisión de GOP. Habitualmente, los activos codificados para streaming tienen un tamaño de GOP de dos segundos.

Para crear un filtro de manifiesto dinámico, navegue a la pestaña **Activos** y seleccione el activo deseado. Seleccione el botón **Clip secundario** para el menú superior. Seleccione el filtro de manifiesto dinámico como el modo de creación de clips desde el menú de configuración avanzada. Luego puede seguir el mismo proceso para producir un clip representado para crear el filtro. Los filtros solo se pueden producir a partir de un activo único.

En la imagen siguiente se ilustra Clipper en el modo de filtro de manifiesto dinámico en Azure Portal: ![Azure Media Clipper en el modo de filtro de manifiesto dinámico en Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Envío de trabajos de creación de clips
Cuando termine de crear el clip, seleccione el botón para enviar el trabajo para iniciar el trabajo de creación de clips correspondiente o una llamada del manifiesto dinámico.

## <a name="next-steps"></a>Pasos siguientes
Para comenzar a usar Azure Media Clipper, ea el artículo de [introducción](media-services-azure-media-clipper-getting-started.md) para detalles sobre cómo implementar el widget.