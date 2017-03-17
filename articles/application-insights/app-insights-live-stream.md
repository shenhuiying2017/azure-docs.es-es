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
ms.date: 02/22/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc6acf7b24bb017e4a93df1c15ed493a4442d6b5
ms.openlocfilehash: 917261e32845b53f2f2f3d12f60ca5fa87d441c7
ms.lasthandoff: 02/27/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Secuencia de métricas en directo: métricas instantáneas para una estrecha supervisión
Live Metrics Stream muestra las métricas de [Application Insights](app-insights-overview.md) de este mismo momento, con una latencia casi en tiempo real de un segundo. Esta supervisión inmediata resulta muy útil si está publicando una compilación nueva y desea asegurarse de que todo esté funcionando como se esperaba o si quiere investigar un incidente en tiempo real.

A diferencia del [Explorador de métricas](app-insights-metrics-explorer.md), Live Metrics Stream muestra un conjunto fijo de métricas. Los datos se conservan solo mientras se encuentran en el gráfico, y luego se descartan.

Los datos de Live Metrics Stream son gratuitos: no se agregarán a la facturación.

![Vídeo de Live Metrics Stream](./media/app-insights-live-stream/youtube.png) [Vídeo de Live Metrics Stream](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![En la hoja de información general, haga clic en Stream en vivo.](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>Errores de Live

Si se registran errores o excepciones, Live Stream guardará una copia de estos. Haga clic en **Pausa** para guardar una muestra específica y seleccione un evento para mostrar sus detalles.

![Errores de muestreo de Live](./media/app-insights-live-stream/live-stream-failures.png)


Live Metrics Stream está disponible con la versión más reciente del [SDK de Application Insights para la web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).

## <a name="filter-by-server-instance"></a>Filtrado por instancia de servidor

Si quiere supervisar una instancia de rol de servidor en particular, puede filtrar por servidor.

![Errores de muestreo de Live](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>Solución de problemas

¿No hay datos? Live Metrics Stream usa un puerto diferente al de otros datos de telemetría de Application Insights. Asegúrese de que [esos puertos](app-insights-ip-addresses.md) están abiertos en el firewall.



## <a name="next-steps"></a>Pasos siguientes
* [Supervisión del uso con Application Insights](app-insights-overview-usage.md)
* [Uso de la Búsqueda de diagnóstico](app-insights-diagnostic-search.md)


