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
ms.date: 06/27/2017
ms.author: piyushjo;ricksal
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: abb09e0b95c881b9d4bcb4d04341fd3a8e4ef63f
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


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
### <a name="430-06272017"></a>4.3.0 (27/06/2017)
* Compatibilidad con Android 8 (las versiones anteriores del SDK no funcionarán en Android 8).
* Ya no hay dependencia de la biblioteca de soporte.
* Quite la clase `EngagementFragmentActivity`.
* Debido a [límites de ejecución en segundo plano](https://developer.android.com/preview/features/background.html) en Android 8, puede que los registros en segundo plano se retrasen hasta que el usuario interactúe con el dispositivo. Esto tendrá efecto en que las estadísticas sobre la campaña de inserción **entregada** y la **notificación del sistema mostrada** se retrasen si el dispositivo estaba en suspensión (la notificación todavía se mostrará, sonará y vibrará en tiempo real sin problemas).
* Debido a [límites de ubicación en segundo plano](https://developer.android.com/preview/features/background-location-limits.html), la ubicación en tiempo real no se actualizará con frecuencia en Android 8.

Para todas las versiones, consulte las [notas de la versión completas](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimientos de actualización
Si ya ha integrado una versión anterior de nuestro SDK en su aplicación, consulte [Procedimientos de actualización](mobile-engagement-android-upgrade-procedure.md).


