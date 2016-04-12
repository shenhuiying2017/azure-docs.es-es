<properties 
	pageTitle="Analytics: la eficaz herramienta de búsqueda de Application Insights" 
	description="Información general de Analytics, la eficaz herramienta de búsqueda de Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>


# Analytics de Application Insights


[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

 
Una consulta típica de AIQL consiste en una tabla de *origen* seguida de una serie de *operadores* separados por `|`.

Por ejemplo, vamos a averiguar a qué hora del día prueban los ciudadanos de Hyderabad nuestra aplicación web. Y ya puestos, veamos qué códigos de resultados se devuelven a sus solicitudes HTTP.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Contamos direcciones IP de cliente únicas agrupándolas por la hora del día durante los últimos 30 días.

Vamos a mostrar los resultados con la presentación de gráficos de barras y con la opción de apilar los resultados de distintos códigos de respuesta:

![Elija el gráfico de barras, los ejes x e y, por último, la segmentación.](./media/app-insights-analytics/020.png)

Parece que nuestra aplicación es más popular en Hyderabad a la hora del almuerzo y de acostarse. (Y debemos investigar esos 500 códigos).


También hay operaciones estadísticas potentes:

![](./media/app-insights-analytics/025.png)


El lenguaje tiene muchas características atractivas:

* [Filtre](app-insights-analytics-queries.md) la telemetría de aplicaciones por cualquier campo, como las métricas y las propiedades personalizadas.
* [Una](app-insights-analytics-queries.md#join-operator) varias tablas: correlacione las solicitudes con las vistas de página, las llamadas de dependencia, las excepciones y los seguimiento de registros.
* [Agregaciones](app-insights-analytics-aggregations.md) estadísticas de gran eficacia.
* Son tan eficaces como SQL, pero mucho más fáciles de usar para las consultas complejas: en lugar de anidar instrucciones, canaliza los datos de una operación básica a la siguiente.
* Visualizaciones inmediatas y potentes.



>[AZURE.NOTE] Se recomienda empezar haciendo un [recorrido por el lenguaje](app-insights-analytics-tour.md).




## Conexión a los datos de Application Insights


Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics/001.png)


## Límites

En la actualidad, los resultados de consulta están limitados exclusivamente a solo una semana de datos antiguos.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->