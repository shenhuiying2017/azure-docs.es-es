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
	ms.date="03/23/2016" 
	ms.author="awills"/>




# Uso de Analytics de Application Insights

[Analytics](app-analytics.md) permite ejecutar consultas eficaces sobre la telemetría desde su aplicación recopiladas por [Application Insights](app-insights-overview.md). En estas páginas se describe su lenguaje de consulta.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Abrir Analytics

En el recurso de inicio de su aplicación de Application Insights, haga clic en Analytics.

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-analytics/001.png)

El tutorial en línea le dará algunas ideas sobre lo que puede hacer.

Podrá encontrar un [paseo más amplio aquí](app-analytics-tour.md).

## Escribir consultas

Escriba una consulta que comience con los nombres de cualquiera de las tablas que aparecen a la izquierda. Use `|` para crear una canalización de [operadores](app-analytics-queries.md).


![](./media/app-analytics-using/150.png)

* No ponga líneas en blanco en la consulta.
* Puede utilizar saltos de línea sencillos en una consulta.
* Puede mantener varias consultas en la ventana separadas por líneas en blanco.
* Para ejecutar una consulta **coloque el cursor dentro o al final**, y haga clic en Go (Ir).


![](./media/app-analytics-using/130.png)

* Puede guardar y recuperar el contenido de la ventana de consulta.

![](./media/app-analytics-using/140.png)

## Organización de los resultados

Puede elegir las columnas que desea ver. Expanda cualquier elemento para ver todos los valores de columna devueltos.

![](./media/app-analytics-using/030.png)

> [AZURE.NOTE] Haga clic en el encabezado de una columna para cambiar el orden de los resultados disponibles en el explorador web de una forma rápida. Tenga en cuenta que, para un conjunto grande de resultados, el número de filas que se descargan en el explorador es limitado. Por tanto, esta forma de ordenación no siempre muestra los elementos mayores o menores reales. Para ello, debería usar el operador [top](app-analytics-queries.md#top-operator) o [sort](app-analytics-queries.md#sort-operator).

Sin embargo, es recomendable usar los operadores [take](app-analytics-queries.md#take-operator), [top](app-analytics-queries.md#top-operator) o [summarize](app-analytics-queries.md#summarize-operator) para evitar que se descarguen tablas enormes del servidor. De todas formas, hay un límite automático de 10 000 filas por consulta.


## Diagramas

Seleccione el tipo de diagrama que desea:

![](./media/app-analytics-using/230.png)

Si tiene varias columnas de los tipos correctos, puede elegir los ejes X e Y, así como una columna de dimensiones para dividir los resultados de la siguiente manera:

![](./media/app-analytics-using/100.png)

De manera predeterminada, resultados se muestran en un principio como una tabla y el usuario selecciona el diagrama manualmente. Sin embargo, puede usar la [directiva render](app-analytics-queries.md#render-directive) al final de una consulta para seleccionar un diagrama.

## Exportación a Excel

Una vez ejecutada una consulta, puede descargar un archivo .csv. Haga clic en **Export (Exportar), a Excel**.

## Exportación a Power BI

1. Coloque el cursor en una consulta y elija **Export to Power BI** (Exportación a Power BI).

    ![](./media/app-analytics-using/240.png)

    Esta operación descarga un archivo de secuencia de comandos de M.

3. Copie la secuencia de comandos de lenguaje M en el editor de consultas avanzadas de Power BI Desktop.
 * Abra el archivo exportado.
 * En Power BI Desktop seleccione: **Get Data, Blank Query, Advanced Editor** (Obtener datos, Consulta en blanco y Editor avanzado) y pegue la secuencia de comandos de lenguaje M.

    ![](./media/app-analytics-using/250.png)

4. Edite las credenciales si es necesario y ya podrá generar el informe.

    ![](./media/app-analytics-using/260.png)



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->