---
title: "Escalado de puntos de conexión de streaming con Azure Portal | Microsoft Docs"
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
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 9bed9392502dae01724c4ca86b8c735ab60a2882


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Escalado de puntos de conexión de streaming con el Portal de Azure
## <a name="overview"></a>Información general

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Este tema es útil para los clientes que tienen un **punto de conexión de streaming** de tipo **Premium**. Para obtener más información acerca de los puntos de conexión de streaming y la configuración de la red CDN, vea el tema [Streaming Endpoint overview](media-services-portal-manage-streaming-endpoints.md) (Información general de puntos de conexión de streaming).
 
Si tiene un tipo **Premium**, de forma predeterminada obtendrá 1 unidad de transmisión por secuencias (SU). Si necesita escalar su punto de conexión de streaming, siga los pasos descritos en este tema.

Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Escalar puntos de conexión de streaming

Para crear y cambiar el número de unidades de streaming, haga lo siguiente:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. En la ventana **Configuración**, haga clic en **Extremos de streaming**.
3. Haga clic en el punto de conexión de streaming que desea modificar. 
4. Mueva el control deslizante para especificar el número de unidades de streaming.

![punto de conexión de streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


