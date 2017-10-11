---
title: Uso de asociados para proporcionar licencias de Widevine a Azure Media Services | Microsoft Docs
description: "En este artículo se describe cómo puede usar Servicios multimedia de Azure (AMS) para entregar una secuencia que se cifra dinámicamente por AMS con DRM tanto de PlayReady como Widevine. La licencia de PlayReady procede del servidor de licencias PlayReady de Servicios multimedia y la licencia de Widevine se entrega al servidor de licencias de castLabs."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 6867e4f910970121df3858516c6bab3114c3c6f9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure
## <a name="overview"></a>Información general
Servicios multimedia de Microsoft Azure le permite entregar MPEG-DASH protegido con DRM de Widevine, que se cifra según la especificación de cifrado común (CENC).

A partir del SDK de Servicios multimedia para .NET versión 3.5.2, Servicios multimedia permite configurar la plantilla de licencia Widevine y obtener licencias de Widevine. También puede usar los siguientes asociados de AMS para ayudarle a entregar licencias de Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) y [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Puede usar [castLabs](http://castlabs.com/company/partners/azure/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
Puede usar [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) para entregar licencias Widevine. Para obtener más información, consulte [Uso de Axinom para entregar licencias de DRM a Servicios multimedia de Azure](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte también
[Uso de cifrado dinámico común de PlayReady o Widevine.](media-services-protect-with-drm.md)

[Blog de Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

