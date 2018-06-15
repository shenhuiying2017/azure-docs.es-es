---
title: Escalado del procesamiento de medios mediante Azure Portal | Microsoft Docs
description: Este tutorial lo guiará a través de los pasos de escalado de procesamiento de medios con Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 51973916c97282ac93032ab833402d9d1356647e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785784"
---
# <a name="change-the-reserved-unit-type"></a>Cambio del tipo de unidad reservada
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Para obtener la versión más reciente del SDK de Java y empezar a realizar desarrollos con Java, consulte [Introducción al SDK de cliente de Java para Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para descargar el SDK de PHP más reciente para Media Services, busque la versión 0.5.7 del paquete de Microsoft/WindowAzure en el [repositorio Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Información general

Una cuenta de Media Services está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo**S1**.

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con **unidades reservadas**. El número de unidades reservadas aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada.

>[!NOTE]
>Las unidades reservadas sirven para establecer paralelismos en todo el procesamiento multimedia, incluida la indexación de trabajos mediante Azure Media Indexer. Sin embargo, a diferencia de la codificación, la indexación de los trabajos no se procesará más rápido con unidades reservadas de mayor rapidez.

> [!IMPORTANT]
> Para más información sobre cómo escalar el procesamiento de medios, consulte este tema de [introducción](media-services-scale-media-processing-overview.md) .
> 
> 

## <a name="scale-media-processing"></a>Escalar el procesamiento multimedia
Para cambiar el tipo de unidad reservada y el número de unidades reservadas, haga lo siguiente:

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. En la ventana **Configuración**, seleccione **Unidades reservadas de medios**.
   
    Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **Unidades reservadas de medios** que se encuentra en la parte superior de la pantalla.
   
    Para cambiar el valor de **RESERVED UNIT TYPE**, haga clic en la barra **Velocidad de las unidades de procesamiento reservadas**. Luego, seleccione el plan de tarifa que necesita: S1, S2 o S3.
   
3. Presione el botón GUARDAR para guardar los cambios.
   
    Las nuevas unidades reservadas se asignan en cuanto presiona GUARDAR.

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

