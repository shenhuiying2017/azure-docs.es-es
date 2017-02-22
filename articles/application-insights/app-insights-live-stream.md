---
title: Live Metrics Stream de Azure Application Insights | Microsoft Docs
description: "Supervise el rendimiento de la aplicación web en tiempo real para observar los efectos de una publicación u otro cambio."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: c453774b04c7005ce5948617beea8770b499b88d
ms.openlocfilehash: 2da6d02055616db5f9854481054ff12c69dc4801


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Secuencia de métricas en directo: métricas instantáneas para una estrecha supervisión
Live Metrics Stream muestra las métricas de [Application Insights](app-insights-overview.md) de este mismo momento, con una latencia casi en tiempo real de un segundo. Esta supervisión inmediata resulta muy útil si está publicando una compilación nueva y desea asegurarse de que todo esté funcionando como se esperaba o si quiere investigar un incidente en tiempo real.

![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-live-stream/live-stream.png)

A diferencia del [Explorador de métricas](app-insights-metrics-explorer.md), Live Metrics Stream muestra un conjunto fijo de métricas. Los datos se conservan solo mientras se encuentran en el gráfico, y luego se descartan.

Los datos de Live Metrics Stream son gratuitos: no se agregarán a la facturación.

## <a name="live-failures"></a>Errores de Live

Si se registran errores o excepciones, Live Stream guardará una copia de estos. Haga clic en **Pausa** para guardar una muestra específica y seleccione un evento para mostrar sus detalles.

![Errores de muestreo de Live](./media/app-insights-live-stream/live-stream-failures.png)


Live Metrics Stream está disponible con la versión más reciente del [SDK de Application Insights para la web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).



## <a name="troubleshooting"></a>Solución de problemas

¿No hay datos? Live Metrics Stream usa un puerto diferente al de otros datos de telemetría de Application Insights. Asegúrese de que [esos puertos](app-insights-ip-addresses.md) están abiertos en el firewall.



## <a name="next-steps"></a>Pasos siguientes
* [Supervisión del uso con Application Insights](app-insights-overview-usage.md)
* [Uso de la Búsqueda de diagnóstico](app-insights-diagnostic-search.md)




<!--HONumber=Feb17_HO2-->


