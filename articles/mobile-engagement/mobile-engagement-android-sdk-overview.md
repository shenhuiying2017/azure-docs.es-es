---
title: "Integración del SDK de Android para Azure Mobile Engagement"
description: "Describe cómo integrar el SDK de Azure Mobile Engagement en aplicaciones de Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integración del SDK de Android para Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Windows universal](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Este documento describe todas las opciones de integración y configuración disponibles para el SDK de Android para Azure Mobile Engagement.

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Características avanzadas
### <a name="reporting-features"></a>Características de informes
Puede agregar estas características:

1. [Opciones de informes avanzados](mobile-engagement-android-advanced-reporting.md)
2. [Opciones de informes de ubicación](mobile-engagement-android-location-reporting.md)
3. [Opciones de configuración avanzada](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notificaciones:
[Integración de cobertura (notificaciones) en su aplicación Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Google Cloud Messaging (GCM): [Integración de GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Amazon Device Messaging (ADM): [Integración de ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementación del plan de etiquetas:
[Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Notas de la versión
### <a name="423-08102016"></a>4.2.3 (08/10/2016)
* No se producirán más bloqueos de WiFi.
* Se ha corregido un interbloqueo al llamar a getDeviceId antes de init (error de la versión 4.2.0).

Para todas las versiones, consulte las [notas de la versión completas](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimientos de actualización
Si ya ha integrado una versión anterior de nuestro SDK en su aplicación, consulte [Procedimientos de actualización](mobile-engagement-android-upgrade-procedure.md).




<!--HONumber=Nov16_HO3-->


