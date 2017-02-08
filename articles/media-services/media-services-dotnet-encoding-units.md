---
title: "Cómo agregar unidades de codificación"
description: "Información sobre cómo agregar unidades de codificación con .NET"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako;milangada;gtrifonov
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: ad0cf996390a792495c492e4205d5be3d27ffa9b


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

## <a name="overview"></a>Información general
> [!IMPORTANT]
> Para más información sobre cómo escalar el procesamiento de medios, consulte este tema de [introducción](media-services-scale-media-processing-overview.md) .
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
De manera predeterminada las cuentas de Servicios multimedia pueden escalar hasta 25 unidades reservadas de codificación y 5 de streaming a petición. Si desea solicitar un límite mayor, abra una incidencia de soporte técnico.

### <a name="open-a-support-ticket"></a>Abrir una incidencia de soporte técnico
Para abrir una incidencia de soporte técnico, haga lo siguiente:

1. Haga clic en [Obtener soporte técnico](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le solicitará que especifique sus credenciales.
2. Seleccione su suscripción.
3. En el tipo de soporte, seleccione "Técnico".
4. Haga clic en "Crear incidencia".
5. Seleccione "Servicios multimedia de Microsoft Azure" en la lista de productos que se muestran en la página siguiente.
6. Seleccione un "Tipo de problema" que sea pertinente en relación con su problema.
7. Haga clic en Continue.
8. Siga las instrucciones que aparecen en la página siguiente y, a continuación, escriba los detalles de su problema.
9. Haga clic en Submit para abrir la incidencia.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


