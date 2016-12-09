---
title: " Escalado de puntos de conexión de streaming con Azure Portal | Microsoft Docs"
description: "Este tutorial lo guiará a través de los pasos de escalado de puntos de conexión de streaming con el Portal de Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc9b75557ffafb831d3271d36921bac3da9264ee


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Escalado de puntos de conexión de streaming con el Portal de Azure
## <a name="overview"></a>Información general
> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Cuando se trabaja con los Servicios multimedia de Azure, uno de los escenarios más comunes es entregar contenido de vídeo a los clientes mediante streaming con velocidad de bits adaptable. Servicios multimedia admite las siguientes tecnologías de streaming adaptable: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH y HDS (únicamente para licenciatarios de Adobe PrimeTime/Access).

Media Services proporciona empaquetado dinámico que permite entregar contenido codificado MP4 de velocidad de bits adaptable en formatos admitidos por Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) justo a tiempo sin tener que almacenar versiones previamente empaquetadas de cada uno de estos formatos de streaming.

Para aprovecharse de los paquetes dinámicos, deberá hacer lo siguiente:

* Codifique su archivo intermedio (origen) en un conjunto de archivos MP4 de velocidad de bits adaptable (los pasos de codificación se muestran más adelante en este tutorial).  
* Cree al menos una unidad de streaming para el *punto de conexión de streaming* para el que planea entregar el contenido. Para cambiar el número de unidades de streaming, realice los siguientes pasos.

Con el empaquetado dinámico solo necesita almacenar y pagar por los archivos en formato de almacenamiento sencillo y Servicios multimedia creará y servirá la respuesta adecuada en función de las solicitudes del cliente.

Además, puede controlar la capacidad del servicio de punto de conexión de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades de streaming. Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de streaming proporcionan capacidad de salida específica que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional que incluye: [empaquetado dinámico](media-services-dynamic-packaging-overview.md), integración con red CDN y configuración avanzada. Para obtener más información, consulte [Administración de puntos de conexión de streaming con el Portal de Azure](media-services-portal-manage-streaming-endpoints.md).

## <a name="scale-streaming-endpoints"></a>Escalar puntos de conexión de streaming
Para crear y cambiar el número de unidades reservadas de streaming, haga lo siguiente:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. En la ventana **Configuración**, haga clic en **Extremos de streaming**.
3. Haga clic en el punto de conexión de streaming que desea modificar. 
4. Mueva el control deslizante para especificar el número de unidades de streaming.

![punto de conexión de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

Se aplican las siguientes consideraciones:

* La asignación de cualquier nueva unidad de streaming puede tardar unos 20 minutos en finalizarse. 
* Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming a petición hasta una hora.
* Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


