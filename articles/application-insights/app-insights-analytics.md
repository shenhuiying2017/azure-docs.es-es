---
title: "Analytics: la eficaz herramienta de búsqueda de Azure Application Insights | Microsoft Docs"
description: "Información general de Analytics, la eficaz herramienta de búsqueda de Application Insights. "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 4fbd80f7e9775fe3c12a54302be4a162d2102e2f
ms.lasthandoff: 03/16/2017


---
# <a name="analytics-in-application-insights"></a>Analytics de Application Insights
[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics. 

* **[Vea el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Use una versión de prueba de Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.
* **[La hoja de referencia rápida de usuarios de SQL](https://aka.ms/sql-analytics)** traduce las expresiones más comunes.
* **[Referencia de lenguaje](app-insights-analytics-reference.md)** Aprenda a utilizar las eficaces características del lenguaje de consulta de Analytics.

## <a name="queries-in-analytics"></a>Funciones de consultas de Analytics
Una consulta típica consta de una tabla de *origen* seguida de una serie de *operadores* separados por `|`. 

Por ejemplo, vamos a averiguar a qué hora del día prueban los ciudadanos de Hyderabad nuestra aplicación web. Y ya puestos, veamos qué códigos de resultados se devuelven a sus solicitudes HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Contamos direcciones IP de cliente únicas agrupándolas por la hora del día durante los últimos 7 días. 

Vamos a mostrar los resultados con la presentación de gráficos de barras y con la opción de apilar los resultados de distintos códigos de respuesta:

![Elija el gráfico de barras, los ejes x e y, por último, la segmentación.](./media/app-insights-analytics/020.png)

Parece que nuestra aplicación es más popular en Hyderabad a la hora del almuerzo y de acostarse. (Y debemos investigar esos 500 códigos).

También hay operaciones estadísticas potentes:

![](./media/app-insights-analytics/025.png)

El lenguaje tiene muchas características atractivas:

* [Filtre](app-insights-analytics-reference.md#where-operator) la telemetría de la aplicación sin procesar por cualquier campo, incluidas sus métricas y propiedades personalizadas.
* [Una](app-insights-analytics-reference.md#join-operator) varias tablas: ponga en correlación las solicitudes con vistas de página, llamadas de dependencia, excepciones y seguimiento de registros.
* [Agregaciones](app-insights-analytics-reference.md#aggregations)estadísticas de gran eficacia.
* Son tan eficaces como SQL, pero mucho más fáciles de usar para las consultas complejas: en lugar de anidar instrucciones, canaliza los datos de una operación básica a la siguiente.
* Visualizaciones inmediatas y potentes.
* [Ancle gráficos a los paneles de Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Exporte consultas a Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Hay una [API de REST](https://dev.applicationinsights.io/) que puede usar para ejecutar consultas mediante programación, por ejemplo de Powershell.


## <a name="connect-to-your-application-insights-data"></a>Conexión a los datos de Application Insights
Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights: 

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics/001.png)


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


## <a name="next-steps"></a>Pasos siguientes
* Se recomienda empezar por el [paseo por el lenguaje](app-insights-analytics-tour.md).



