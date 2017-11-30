---
title: "Analytics: la eficaz herramienta de búsqueda y consulta de Azure Application Insights | Microsoft Docs"
description: "Información general de Analytics, la eficaz herramienta de búsqueda de Application Insights. "
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: adda6335b702470cd491f07d750236c368325a9e
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2017
---
# <a name="analytics-in-application-insights"></a>Analytics de Application Insights
Analytics es la herramienta eficaz de búsqueda y consulta de [Application Insights](app-insights-overview.md). Analytics es una herramienta web por lo que no requiere ninguna configuración. Si ya ha configurado Application Insights para una de las aplicaciones, puede analizar los datos de la aplicación abriendo Analytics desde la [hoja de información general](app-insights-dashboards.md) de la aplicación.

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics/001.png)

También puede usar el [sitio de prueba de Analytics](https://go.microsoft.com/fwlink/?linkid=859557), que es un entorno de demostración gratuito con una gran cantidad de datos de ejemplo.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="query-data-in-analytics"></a>Consulta de datos en Analytics
Una consulta típica empieza con un nombre de tabla seguido de una serie de *operadores* separados por `|`.
Por ejemplo, vamos a averiguar cuántas solicitudes ha recibido nuestra aplicación de distintos países durante las tres últimas horas:
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

Empezaremos con el nombre de tabla *requests* y agregaremos elementos canalizados según sea necesario.  En primer lugar, definimos un filtro de tiempo para revisar solo los registros de las últimas tres horas.
A continuación, contamos el número de registros por país (esos datos se encuentran en la columna *client_CountryOrRegion*). Por último, representamos los resultados en un gráfico circular.
<br>

![Resultados de la consulta](./media/app-insights-analytics/030.png)

El lenguaje tiene muchas características atractivas:

* [Filtre](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) la telemetría de la aplicación sin procesar por cualquier campo, incluidas sus métricas y propiedades personalizadas.
* [Una](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) varias tablas: ponga en correlación las solicitudes con vistas de página, llamadas de dependencia, excepciones y seguimiento de registros.
* [Agregaciones](https://docs.loganalytics.io/docs/Learn/Tutorials/Aggregation-functions)estadísticas de gran eficacia.
* Visualizaciones inmediatas y potentes.
* [API de REST](https://dev.applicationinsights.io/) que puede usar para ejecutar consultas mediante programación, por ejemplo de Powershell.

La [referencia del lenguaje completa](https://go.microsoft.com/fwlink/?linkid=856079) detalla todos los comandos admitidos y se actualiza periódicamente.

## <a name="next-steps"></a>Pasos siguientes
* Introducción al [portal de Analytics](https://go.microsoft.com/fwlink/?linkid=856587)
* Introducción a la [escritura de consultas](https://go.microsoft.com/fwlink/?linkid=856078)
* Consulte la [hoja de referencia rápida de usuarios de SQL](https://aka.ms/sql-analytics) para ver las traducciones de las expresiones más comunes.
* Use una versión de prueba de Analytics en nuestro [sitio de prueba](https://analytics.applicationinsights.io/demo) si su aplicación aún no envía datos a Application Insights.
* Vea el [vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4).