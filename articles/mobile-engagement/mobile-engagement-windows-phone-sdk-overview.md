---
title: "Introducción al SDK de Windows Phone Silverlight para Azure Mobile Engagement | Microsoft Docs"
description: "Introducción al SDK de Windows Phone Silverlight para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Introducción al SDK de Windows Phone Silverlight para Azure Mobile Engagement
Comience aquí para obtener los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de Windows Phone Silverlight. Si primero quiere probarlo, asegúrese de completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procedimientos de integración
1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Implementación del plan de etiquetas: [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Notas de la versión
###<a name="331-11032016"></a>3.3.1 (03/11/2016)
Parte del paquete NuGet *MicrosoftAzure.MobileEngagement***v3.4.1**

* Mejoras de estabilidad.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimientos de actualización
Si ya integró una versión anterior de nuestro SDK en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK. Consulte los [procedimientos de actualización](mobile-engagement-windows-phone-upgrade-procedure.md)completos. Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

### <a name="from-200-to-330"></a>De 2.0.0 a 3.3.0
#### <a name="test-logs"></a>Registros de prueba
Los registros de consola generados por el SDK ahora se pueden habilitar, deshabilitar o filtrar. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Actualizar de versiones anteriores
Consulte [Procedimientos de actualización](mobile-engagement-windows-phone-upgrade-procedure.md)

