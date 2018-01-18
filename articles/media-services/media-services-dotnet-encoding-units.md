---
title: "Escalado de procesamiento multimedia mediante la adición de unidades de codificación - Azure | Microsoft Docs"
description: "Información sobre cómo agregar unidades de codificación con .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 9274e2807beeb0fd517ba1e848cfc26db1c57655
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Escalado de codificación con el SDK de .NET
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Para obtener la versión más reciente del SDK de Java y empezar a realizar desarrollos con Java, consulte [Introducción al SDK de cliente de Java para Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para descargar el SDK de PHP más reciente para Media Services, busque la versión 0.5.7 del paquete de Microsoft/WindowAzure en el [repositorio Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Información general
> [!IMPORTANT]
> Para más información sobre cómo escalar el procesamiento de medios, consulte este tema de [Introducción](media-services-scale-media-processing-overview.md).
> 
> 

Para cambiar el tipo de unidad reservada y el número de unidades reservadas de codificación mediante el SDK de .NET, haga lo siguiente:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Apertura de una incidencia de soporte técnico
De manera predeterminada las cuentas de Media Services pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

