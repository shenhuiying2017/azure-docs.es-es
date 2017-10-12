---
title: "Configuración avanzada del SDK de Engagement para aplicaciones universales de Windows"
description: "Opciones de configuración para Azure Mobile Engagement con aplicaciones universales de Windows"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: cb9454212c94cf65093219c3d24c71277ede7877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuración avanzada del SDK de Engagement para aplicaciones universales de Windows
> [!div class="op_single_selector"]
> * [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

Este procedimiento describe cómo configurar diversas opciones de configuración de aplicaciones de Android para Azure Mobile Engagement.

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Configuración avanzada
### <a name="disable-automatic-crash-reporting"></a>Deshabilitar los informes automáticos de bloqueo
Puede deshabilitar la característica de informes automáticos de bloqueo de Engagement. Cuando se produzca una excepción no controlada, Engagement no hará nada.

> [!WARNING]
> Si deshabilita esta característica, tenga en cuenta que cuando se produzca un error no controlado en la aplicación, Engagement no enviará la información del bloqueo, **NI** tampoco cerrará la sesión ni los trabajos.
> 
> 

Para deshabilitar los informes automáticos de bloqueo, personalice la configuración según la manera en que la declaró:

#### <a name="from-engagementconfigurationxml-file"></a>Desde el archivo `EngagementConfiguration.xml`
Establezca el informe de bloqueo en `false` entre las etiquetas `<reportCrash>` y `</reportCrash>`.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Desde el objeto `EngagementConfiguration` en tiempo de ejecución
Establezca el informe de bloqueo mediante el objeto EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Deshabilitar los informes en tiempo real
De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si su aplicación notifica registros con mucha frecuencia, es mejor almacenar en búfer los registros y notificarlos todos a la vez de manera periódica. Esto se denomina "modo de ráfaga".

Para ello, llame al método siguiente:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. Cuando desee volver a activar el registro en tiempo real, llame al método sin ningún parámetro o con el valor 0.

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30 000 (30 segundos). Los registros guardados se limitan a 300 elementos. Si el envío es demasiado largo, puede perder algunos registros.

> [!WARNING]
> El umbral de ráfaga no puede configurarse en un periodo inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente en el valor predeterminado, es decir, cero segundos. Esto hará que el SDK informe de los registros en tiempo real.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
