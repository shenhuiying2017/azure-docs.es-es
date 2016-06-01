<properties 
	pageTitle="Uso de Analytics: la eficaz herramienta de búsqueda de Application Insights" 
	description="Uso de Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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




# Uso de Analytics en Application Insights


[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## Apertura de Analytics

En el recurso de inicio de la aplicación de Application Insights, haga clic en Analytics.

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics-using/001.png)

El tutorial en línea le dará algunas ideas sobre lo que puede hacer.

Podrá encontrar un [paseo más amplio aquí](app-insights-analytics-tour.md).

## Escritura de consultas

Escriba una consulta que comience por los nombres de cualesquiera de las tablas que se enumeran a la izquierda. Use `|` para crear una canalización de [operadores](app-insights-analytics-reference.md#queries-and-operators).


![](./media/app-insights-analytics-using/150.png)

* No ponga líneas en blanco en la consulta.
* Puede utilizar saltos de línea sencillos en las consultas.
* Puede mantener varias consultas en la ventana separadas por líneas en blanco.
* Para ejecutar una consulta, **coloque el cursor dentro o al final** y haga clic en Go (Ir).


![](./media/app-insights-analytics-using/130.png)

* Puede guardar y recuperar el contenido de la ventana de consulta.

![](./media/app-insights-analytics-using/140.png)

## Organización de los resultados

Puede elegir las columnas que desea ver. Expanda cualquier elemento para ver todos los valores de columna devueltos.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] Haga clic en el encabezado de una columna para cambiar el orden de los resultados disponibles en el explorador web de una forma rápida. Tenga en cuenta que, para un conjunto grande de resultados, el número de filas que se descargan en el explorador es limitado. Por tanto, esta forma de ordenación no siempre muestra los elementos mayores o menores reales. Para ello, es preciso usar los operadores [top](app-insights-analytics-reference.md#top-operator) o [sort](app-insights-analytics-reference.md#sort-operator).

No obstante, es aconsejable usar los operadores [take](app-insights-analytics-reference.md#take-operator), [top](app-insights-analytics-reference.md#top-operator) o [summarize](app-insights-analytics-reference.md#summarize-operator) para evitar que se descarguen tablas muy grandes del servidor. De todas formas, hay un límite automático de 10 000 filas por consulta.


## Diagramas

Seleccione el tipo de diagrama que desea:

![](./media/app-insights-analytics-using/230.png)

Si tiene varias columnas de los tipos correctos, puede elegir los ejes X e Y, así como una columna de dimensiones para dividir los resultados de la siguiente manera:

![](./media/app-insights-analytics-using/100.png)

De manera predeterminada, los resultados se muestran en un principio en forma de tabla y el diagrama se selecciona manualmente. Sin embargo, para seleccionar un diagrama se puede usar la [directiva render](app-insights-analytics-reference.md#render-directive) al final de una consulta.

## Exportación a Excel

Una vez que haya ejecutado una consulta, puede descargar un archivo .csv. Haga clic en **Export (Exportar), Excel**.

## Exportación a Power BI

1. Coloque el cursor en una consulta y elija **Export to Power BI** (Exportar, Power BI).

    ![](./media/app-insights-analytics-using/240.png)

    Esta operación descarga un archivo de script de M.

3. Copie el script del lenguaje M en el editor de consultas avanzadas de Power BI Desktop.
 * Abra el archivo exportado.
 * En Power BI Desktop, seleccione: **Get Data, Blank Query, Advanced Editor** (Obtener datos, Consulta en blanco y Editor avanzado) y pegue el script del lenguaje M.

    ![](./media/app-insights-analytics-using/250.png)

4. Edite las credenciales, en caso de que sea necesario, y podrá generar el informe.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0518_2016-->