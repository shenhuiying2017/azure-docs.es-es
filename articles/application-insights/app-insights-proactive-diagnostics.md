---
title: "Detección inteligente en Application Insights | Microsoft Docs"
description: "Application Insights realiza un análisis profundo automático de la telemetría de la aplicación y le advierte de los posibles problemas."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 30124e697af5f7560e8070ce43c5a985bd2c403b


---
# <a name="smart-detection-in-application-insights"></a>Detección inteligente en Application Insights
 La detección inteligente avisa automáticamente de posibles problemas de rendimiento en su aplicación web. Realiza un análisis proactivo de la telemetría que su aplicación envía a [Application Insights](app-insights-overview.md). Si hay un aumento repentino de las tasas de error o patrones de rendimiento anormales en el cliente o el servidor, recibirá una alerta. Esta característica no necesita ninguna configuración. Funciona si la aplicación envía suficiente telemetría.

Puede acceder a las alertas de detección inteligente desde los correos electrónicos que recibe y desde la hoja de detección inteligente.

## <a name="review-your-smart-detections"></a>Revisión de las detecciones inteligentes
Puede detectar las detecciones de dos maneras:

* **Recibirá un correo electrónico** de Application Insights. Este es un ejemplo típico:
  
    ![Alerta de correo electrónico](./media/app-insights-proactive-diagnostics/03.png)
  
    Haga clic en el botón grande para abrir más detalles en el portal.
* **El icono de detección inteligente** en la hoja de información general de la aplicación muestra un recuento de las alertas recientes. Haga clic en el icono para ver una lista de las alertas recientes.

![Visualización de detecciones recientes](./media/app-insights-proactive-diagnostics/04.png)

Seleccione una alerta para ver sus detalles.

## <a name="what-problems-are-detected"></a>¿Qué problemas se detectan?
Hay tres tipos de detección:

* [Detección inteligente: anomalías de errores](app-insights-proactive-failure-diagnostics.md). Usamos el aprendizaje automático para establecer la tasa esperada de solicitudes con error para su aplicación, correlacionando la carga y otros factores. Si la tasa de error queda fuera del rango esperado, se enviará una alerta.
* [Detección inteligente: anomalías de rendimiento](app-insights-proactive-performance-diagnostics.md). Buscamos patrones anómalos en los tiempos de respuesta y las tasas de error cada día. Correlacionamos estos problemas con propiedades como la ubicación, el explorador, el sistema operativo del cliente, la instancia del servidor y la hora del día.
* [Detección inteligente: problemas del servicio en la nube de Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Recibe alertas si la aplicación se hospeda en Servicios en la nube de Azure y una instancia de rol tiene errores de inicio, repetición de ciclos frecuentes o bloqueos en tiempo de ejecución.

(Los vínculos a la Ayuda de cada notificación llevan a los artículos pertinentes).

## <a name="next-steps"></a>Pasos siguientes
Estas herramientas de diagnóstico lo ayudarán a inspeccionar los datos de telemetría de su aplicación:

* [Explorador de métricas](app-insights-metrics-explorer.md)
* [Explorador de búsqueda](app-insights-diagnostic-search.md)
* [Analytics: Lenguaje de consulta eficaz](app-insights-analytics-tour.md)

La detección inteligente es completamente automática. Pero ¿quizás le gustaría configurar algunas alertas más?

* [Alertas de métricas configuradas manualmente](app-insights-alerts.md)
* [Pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) 




<!--HONumber=Nov16_HO3-->


