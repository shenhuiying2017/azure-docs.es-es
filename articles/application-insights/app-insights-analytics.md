---
title: "Analytics: la eficaz herramienta de búsqueda de Azure Application Insights | Microsoft Docs"
description: "Información general de Analytics, la eficaz herramienta de búsqueda de Application Insights. "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: es-es
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Analytics de Application Insights
[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Log Analytics. 

* **[Vea el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Use una versión de prueba de Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.
* **[La hoja de referencia rápida de usuarios de SQL](https://aka.ms/sql-analytics)** traduce las expresiones más comunes.
* **[Referencia de lenguaje](app-insights-analytics-reference.md)** Aprenda a utilizar las eficaces características del lenguaje de consulta de Log Analytics.


## <a name="queries-in-analytics"></a>Funciones de consultas de Analytics
Una consulta típica consta de una tabla de *origen* seguida de una serie de *operadores* separados por `|`. 

Por ejemplo, vamos a averiguar a qué hora del día prueban los ciudadanos de Hyderabad nuestra aplicación web. Y ya puestos, veamos qué códigos de resultados se devuelven a sus solicitudes HTTP. 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

Contamos direcciones IP de cliente únicas agrupándolas por la hora del día durante los últimos 7 días. 

> [!NOTE]
> Para obtener resultados más allá de las 24 horas anteriores, puede incluir explícitamente "timestamp" en la consulta, o bien usar el menú desplegable de intervalo de tiempo.
>

Vamos a mostrar los resultados con la presentación de gráficos de barras y con la opción de apilar los resultados de distintos códigos de respuesta:

![Elija el gráfico de barras, los ejes x e y, por último, la segmentación.](./media/app-insights-analytics/020.png)

Parece que nuestra aplicación es más popular en Hyderabad a la hora del almuerzo y de acostarse. (Y debemos investigar esos 500 códigos).

También hay operaciones estadísticas potentes:

![Resultados de consulta estadística](./media/app-insights-analytics/025.png)

El lenguaje tiene muchas características atractivas:


* [Filtre](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) la telemetría de la aplicación sin procesar por cualquier campo, incluidas sus métricas y propiedades personalizadas.
* [Una](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) varias tablas: ponga en correlación las solicitudes con vistas de página, llamadas de dependencia, excepciones y seguimiento de registros.
* [Agregaciones](https://docs.loganalytics.io/learn/tutorials/aggregations.html)estadísticas de gran eficacia.
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



## <a name="query-examples"></a>Ejemplos de consultas

Pruebe estos tutoriales para ilustrar la eficacia del uso de Analytics:

 *  [Diagnóstico automático de picos y saltos escalonados en duraciones de solicitudes](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *  [Análisis de las degradaciones de rendimiento con análisis de series temporales](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *  [Análisis de los errores de aplicaciones con autocluster y diffpatterns](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *  [Detecciones de formas avanzadas con análisis de series temporales](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *  [Uso de operaciones de ventana deslizante para analizar el uso de aplicaciones (MAU/DAU acumulados, etc.)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *  [Detección de interrupciones del servicio en función del análisis de registros de depuración](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) y la entrada de blog correspondiente [aquí](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics)
 *  [Generación de perfiles de rendimiento de las aplicaciones mediante registros de depuración simples](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) y la entrada de blog correspondiente [aquí](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/)
 *  [Medición de la duración de cada paso en el flujo de código mediante registros de depuración simples](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) y la entrada de blog correspondiente [aquí](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/)
 *  [Análisis de la simultaneidad mediante registros de depuración simples](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) y la entrada de blog correspondiente [aquí](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/)



## <a name="next-steps"></a>Pasos siguientes
* Se recomienda empezar por el [paseo por el lenguaje](app-insights-analytics-tour.md). 
* Más información acerca del [uso de Analytics](app-insights-analytics-using.md). 
* [Referencia de lenguaje](app-insights-analytics-reference.md). 

