<properties 
	pageTitle="Application Analytics: la eficaz herramienta de búsqueda de Application Insights" 
	description="Información general de Application Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Application Insights Analytics: información general del lenguaje


[Application Insights Analytics](app-analytics.md) es un eficaz motor de consultas para su telemetría de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Application Insights, AIQL.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Una consulta típica de AIQL consiste en una tabla de *origen* seguida por una serie de *operadores* separados por `|`.

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

![Elija el gráfico de barras, los ejes x e y, por último, la segmentación.](./media/app-analytics/020.png)

Parece que nuestra aplicación es más popular en Hyderabad a la hora del almuerzo y de acostarse. (Y debemos investigar esos 500 códigos).

El lenguaje tiene muchas de las funcionalidades de SQL y muchas más. Al igual que en SQL, puede filtrar datos, agrupar registros, y ordenar y combinar tablas. También puede realizar cálculos en los campos. A diferencia de SQL, estas funciones se dividen en operaciones diferentes y, en lugar de anidar consultas, se canalizan los datos de una operación a la siguiente de una forma muy intuitiva. Esto facilita la redacción de consultas muy complejas.


>[AZURE.NOTE] Se recomienda empezar por el [paseo por el lenguaje](app-analytics-tour.md).


## Conexión a los datos de Application Insights


Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->