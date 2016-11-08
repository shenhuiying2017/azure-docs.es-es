---
title: Detección proactiva en Application Insights | Microsoft Docs
description: Application Insights realiza un análisis profundo automático de la telemetría de la aplicación y le advierte de los posibles problemas.
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: awills

---
# Detección proactiva en Application Insights
 La detección proactiva avisa automáticamente de posibles problemas de rendimiento en su aplicación web. Realizar un análisis inteligente de la telemetría que su aplicación envía a [Visual Studio Application Insights](app-insights-overview.md). Si hay un aumento repentino de las tasas de error o patrones de rendimiento anormales en el cliente o el servidor, recibirá una alerta. Esta característica no necesita ninguna configuración. Funciona si la aplicación envía suficiente telemetría.

Puede tener acceso a las alertas de detección proactiva desde los correos electrónicos que recibe y desde la hoja Detección proactiva.

## Revisión de las detecciones proactivas
Puede detectar las detecciones de dos maneras:

* **Recibirá un correo electrónico** de Application Insights. Este es un ejemplo típico:
  
    ![Alerta de correo electrónico](./media/app-insights-proactive-detection/03.png)
  
    Haga clic en el botón grande para abrir más detalles en el portal.
* **El icono Detección proactiva** en la hoja de información general de la aplicación muestra un recuento de las alertas recientes. Haga clic en el icono para ver una lista de las alertas recientes.

![Visualización de detecciones recientes](./media/app-insights-proactive-detection/04.png)

Seleccione una alerta para ver sus detalles.

## ¿Qué problemas se detectan?
Hay tres tipos de detección:

* [Alertas de error en tiempo casi real](app-insights-proactive-failure-diagnostics.md). Usamos el aprendizaje automático para establecer la tasa esperada de solicitudes con error para su aplicación, correlacionando la carga y otros factores. Si la tasa de error queda fuera del rango esperado, se enviará una alerta.
* [Diagnósticos de anomalías](app-insights-proactive-anomaly-diagnostics.md). Buscamos patrones anómalos en los tiempos de respuesta y las tasas de error cada día. Correlacionamos estos problemas con propiedades como la ubicación, el explorador, el sistema operativo del cliente, la instancia del servidor y la hora del día.
* [Servicios en la nube de Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Recibe alertas si la aplicación se hospeda en Servicios en la nube de Azure y una instancia de rol tiene errores de inicio, repetición de ciclos frecuentes o bloqueos en tiempo de ejecución.

(Los vínculos a la Ayuda de cada notificación llevan a los artículos pertinentes).

## Pasos siguientes
Estas herramientas de diagnóstico lo ayudarán a inspeccionar los datos de telemetría de su aplicación:

* [Explorador de métricas](app-insights-metrics-explorer.md)
* [Explorador de búsqueda](app-insights-diagnostic-search.md)
* [Analytics: Lenguaje de consulta eficaz](app-insights-analytics-tour.md)

Las detecciones proactivas son completamente automáticas. Pero ¿quizás le gustaría configurar algunas alertas más?

* [Alertas de métricas configuradas manualmente](app-insights-alerts.md)
* [Pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->