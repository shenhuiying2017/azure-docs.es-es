---
title: Un paseo por Analytics de Azure Application Insights | Microsoft Docs
description: "Ejemplos breves de todas las principales consultas de Analytics, la potente herramienta de búsqueda de Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: bddf4a6d-ea8d-4607-8531-1fe197cc57ad
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/06/2017
ms.author: mbullwin
ms.openlocfilehash: 271ccc126eeb9411646b68b32fd30ce32b5eef5c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="a-tour-of-analytics-in-application-insights"></a>Un paseo por Analytics de Application Insights
[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Log Analytics.

* **[Vea el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Use una versión de prueba de Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.
* **[La hoja de referencia rápida de usuarios de SQL](https://aka.ms/sql-analytics)** traduce las expresiones más comunes.

Recorramos algunas preguntas básicas para comenzar.

## <a name="connect-to-your-application-insights-data"></a>Conexión a los datos de Application Insights
Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics-tour/001.png)

## <a name="takehttpsdocsloganalyticsioquerylanguagequerylanguagetakeoperatorhtml-show-me-n-rows"></a>[Take](https://docs.loganalytics.io/queryLanguage/query_language_takeoperator.html): mostrarme n filas
Los puntos de datos que registran las operaciones de usuario (normalmente solicitudes HTTP recibidas mediante la aplicación web) se almacenan en una tabla denominada `requests`. Cada fila es un punto de datos de telemetría procedente del SDK de Application Insights en una aplicación.

Comencemos examinando algunas filas de ejemplo de la tabla:

![results](./media/app-insights-analytics-tour/010.png)

> [!NOTE]
> Coloque el cursor en algún lugar de la instrucción antes de hacer clic en Go (Ir). Puede dividir una instrucción en varias líneas, pero no incluya líneas en blanco en una instrucción. Las líneas en blanco son una forma práctica de tener varias consultas diferentes en la ventana.
>
>

Elija columnas, arrástrelas, agrupe por columnas y filtre:

![Hacer clic en la selección de columna en la parte superior derecha de los resultados](./media/app-insights-analytics-tour/030.png)

Expanda cualquier elemento para ver los detalles:

![Seleccione Table (Tabla) y use Configure Columns (Configurar columnas).](./media/app-insights-analytics-tour/040.png)

> [!NOTE]
> Haga clic en el encabezado de una columna para cambiar el orden de los resultados disponibles en el explorador web. Tenga en cuenta que, para un conjunto grande de resultados, el número de filas que se descargan en el explorador es limitado. Esta forma de ordenación simplemente ordena el conjunto de resultados devuelto y no siempre muestra los elementos mayores o menores reales. Para ordenar los elementos de forma confiable, use el operador `top` o `sort`.
>
>

## <a name="query-across-applications"></a>Consultas entre aplicaciones
Si desea combinar datos de varias aplicaciones de Application Insights, use la palabra clave **app** para especificar la aplicación junto con el nombre de tabla.  Esta consulta combina las solicitudes de dos aplicaciones diferentes mediante el comando **union**.


```AIQL

    union app('fabrikamstage').requests, app('fabrikamprod').requests
    
```

## <a name="tophttpsdocsloganalyticsioquerylanguagequerylanguagetopoperatorhtml-and-sorthttpsdocsloganalyticsioquerylanguagequerylanguagesortoperatorhtml"></a>[Top](https://docs.loganalytics.io/queryLanguage/query_language_topoperator.html) y [sort](https://docs.loganalytics.io/queryLanguage/query_language_sortoperator.html)
`take` resulta útil para obtener un ejemplo rápido de un resultado, pero muestra filas de la tabla sin ningún orden determinado. Para obtener una vista ordenada, use `top` (para un ejemplo) o `sort` (toda la tabla).

Muéstrame las primeras n filas, ordenadas por una columna en particular:

```AIQL

    requests | top 10 by timestamp desc
```

* *Sintaxis*: la mayoría de los operadores tienen parámetros de palabra clave como `by`.
* `desc` = orden descendente, `asc` = orden ascendente.

![](./media/app-insights-analytics-tour/260.png)

`top...` es una manera más eficiente de decir `sort ... | take...`. Podríamos haber escrito:

```AIQL

    requests | sort by timestamp desc | take 10
```

El resultado sería el mismo, pero se ejecutaría un poco más lento. (También podría escribir `order`, que es un alias de `sort`).

Los encabezados de columna en la vista de tabla también pueden utilizarse para ordenar los resultados en la pantalla. Pero por supuesto, si ha usado `take` o `top` para recuperar solo parte de una tabla, al hacer clic en el encabezado de la columna solamente reordenará los registros que ha recuperado.

## <a name="wherehttpsdocsloganalyticsioquerylanguagequerylanguagewhereoperatorhtml-filtering-on-a-condition"></a>[Where](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html): filtrado de una condición.

Veamos las solicitudes que devuelven un código de resultado determinado:

```AIQL

    requests
    | where resultCode  == "404"
    | take 10
```

![](./media/app-insights-analytics-tour/250.png)

El operador `where` acepta una expresión booleana. He aquí algunos puntos clave:

* `and`, `or`: Operadores booleanos
* `==`, `<>`, `!=`: igual que y no igual que
* `=~`, `!~`: cadena que no distingue entre mayúsculas y minúsculas igual que y no igual que. Hay muchos más operadores de comparación de cadenas.

<!---Read all about [scalar expressions]().--->

### <a name="find-unsuccessful-requests"></a>Buscar solicitudes incorrectas

Convertir un valor de cadena en un entero para usar la comparación mayor que:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```
<!---
`resultCode` has type string, so we must cast it app-insights-analytics-reference.md#casts for a numeric comparison.
--->

## <a name="time"></a>Hora

De forma predeterminada, las consultas se restringen a las últimas 24 horas. Pero puede cambiar este intervalo:

![](./media/app-insights-analytics-tour/change-time-range.png)

Reemplace el intervalo de tiempo mediante la escritura de cualquier consulta que mencione `timestamp` en una cláusula where. Por ejemplo: 

```AIQL

    // What were the slowest requests over the past 3 days?
    requests
    | where timestamp > ago(3d)  // Override the time range
    | top 5 by duration
```

La característica de intervalo de tiempo es equivalente a una cláusula "where" insertada después de cada mención de una de las tablas de origen.

`ago(3d)` significa "hace tres días". Otras unidades de tiempo son horas (`2h`, `2.5h`), minutos (`25m`) y segundos (`10s`).

Otros ejemplos:

```AIQL

    // Last calendar week:
    requests
    | where timestamp > startofweek(now()-7d)
        and timestamp < startofweek(now())
    | top 5 by duration

    // First hour of every day in past seven days:
    requests
    | where timestamp > ago(7d) and timestamp % 1d < 1h
    | top 5 by duration

    // Specific dates:
    requests
    | where timestamp > datetime(2016-11-19) and timestamp < datetime(2016-11-21)
    | top 5 by duration

```

[Referencia de fechas y horas](https://docs.loganalytics.io/concepts/concepts_datatypes_datetime.html).


## <a name="projecthttpsdocsloganalyticsioquerylanguagequerylanguageprojectoperatorhtml-select-rename-and-compute-columns"></a>[Project](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html): seleccionar, cambiar nombre y calcular columnas
Use [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) para seleccionar solamente las columnas que desea:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)

También puede cambiar el nombre de las columnas y definir otras nuevas:

```AIQL

    requests
    | top 10 by timestamp desc
    | project  
            name,
            response = resultCode,
            timestamp,
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Los nombres de columna pueden incluir espacios o símbolos si están entre corchetes, tal como se muestra: `['...']` o `["..."]`
* `%` es el operador de módulo habitual.
* `1d` (es decir, el dígito uno y luego una "d") es un literal de intervalo de tiempo que significa un día. Aquí se indican otros literales de intervalo de tiempo: `12h`, `30m`, `10s` y `0.01s`.
* `floor` (alias `bin`) redondea un valor a la baja al múltiplo más cercano del valor base que proporciona. Por lo tanto, `floor(aTime, 1s)` redondea un tiempo a la baja al segundo más cercano.

Las expresiones pueden incluir todos los operadores habituales (`+`, `-`, etc.) y hay una amplia gama de funciones útiles.

## <a name="extend"></a>Extend
Si solo desea agregar columnas a las ya existentes, use [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html):

```AIQL

    requests
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

El uso de [`extend`](https://docs.loganalytics.io/queryLanguage/query_language_extendoperator.html) es menos detallado que [`project`](https://docs.loganalytics.io/queryLanguage/query_language_projectoperator.html) si desea conservar todas las columnas existentes.

### <a name="convert-to-local-time"></a>Conversión a la hora local

Las marcas de tiempo siempre se expresan en formato UTC. Por lo que si se encuentra en la costa Pacífico y es temporada de invierno, sería de esta forma:

```AIQL

    requests
    | top 10 by timestamp desc
    | extend localTime = timestamp - 8h
```


## <a name="summarizehttpsdocsloganalyticsioquerylanguagequerylanguagesummarizeoperatorhtml-aggregate-groups-of-rows"></a>[Summarize](https://docs.loganalytics.io/queryLanguage/query_language_summarizeoperator.html): adición de grupos de filas
`Summarize` aplica una *función de agregación* específica sobre grupos de filas.

Por ejemplo, el tiempo que su aplicación web tarda en responder a una solicitud se notifica en el campo `duration`. Veamos el tiempo medio de respuesta de todas las solicitudes:

![](./media/app-insights-analytics-tour/410.png)

O bien, podríamos separar el resultado en solicitudes de nombres diferentes:

![](./media/app-insights-analytics-tour/420.png)

`Summarize` recopila los puntos de datos de la transmisión en grupos que la cláusula `by` calcula por igual. Cada valor de la expresión `by` (cada nombre de operación único en el ejemplo anterior) da como resultado una fila de la tabla de resultados.

O bien, podríamos agrupar los resultados en función de la hora del día:

![](./media/app-insights-analytics-tour/430.png)

Observe cómo usamos la función `bin` (también conocida como "`floor`"). Si solo usáramos `by timestamp`, cada fila de entrada terminaría en su pequeño grupo. Para cualquier escalar continuo, como horas o números, tenemos que dividir el intervalo continuo en un número de valores discretos fácil de administrar. `bin`, que es simplemente la conocida función de redondeo `floor`, es la manera más sencilla de hacerlo.

Podemos usar la misma técnica para reducir los intervalos de cadenas:

![](./media/app-insights-analytics-tour/440.png)

Tenga en cuenta que puede usar `name=` para establecer el nombre de una columna de resultados, en las expresiones de agregación o mediante la cláusula by.

## <a name="counting-sampled-data"></a>Recuento de datos muestreados
`sum(itemCount)` es la agregación recomendada para contar eventos. En muchos casos, itemCount == 1, por lo que la función simplemente cuenta el número de filas del grupo. Pero cuando el [muestreo](app-insights-sampling.md) está en funcionamiento, solo se conserva una fracción de los eventos originales como puntos de datos en Application Insights, de modo que por cada punto de datos que vea, haya eventos `itemCount`.

Por ejemplo, si el muestreo descarta el 75 % de los eventos originales, itemCount ==4 en los registros retenidos; es decir, para cada registro retenido, había cuatro registros originales.

El muestreo adaptable hace que itemCount sea mayor durante los períodos en que la aplicación se utiliza más.

Por lo tanto, resumir itemCount proporciona una buena estimación del número original de eventos.

![](./media/app-insights-analytics-tour/510.png)

También existe una agregación `count()` (y una operación de recuento) para los casos en los que realmente desea contar el número de filas de un grupo.

Existe una gama de [funciones de agregación](https://docs.loganalytics.io/learn/tutorials/aggregations.html).

## <a name="charting-the-results"></a>Crear gráficos de los resultados
```AIQL

    exceptions
       | summarize count=sum(itemCount)  
         by bin(timestamp, 1h)
```

De forma predeterminada, los resultados se muestran como una tabla:

![](./media/app-insights-analytics-tour/225.png)

Podemos hacerlo mejor que la vista de la tabla. Echemos un vistazo a los resultados en la vista del gráfico con la opción de barra vertical:

![Haga clic Chart (Gráfico) y después elija Vertical bar chart (Gráfico de barras verticales) y asigne los ejes x e y.](./media/app-insights-analytics-tour/230.png)

Tenga en cuenta que aunque no ordenamos los resultados por tiempo (como puede ver en la visualización de la tabla), la visualización del gráfico siempre muestra las fechas en el orden correcto.


## <a name="timecharts"></a>Gráficos de tiempo
Mostrar cuántos eventos hay cada hora:

```AIQL

    requests
      | summarize event_count=sum(itemCount)
        by bin(timestamp, 1h)
```

Seleccionar la opción de visualización de gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

## <a name="multiple-series"></a>Varias series
Varias expresiones en la cláusula `summarize` crean varias columnas.

Varias expresiones en la cláusula `by` crea varias filas, una para cada combinación de valores.

```AIQL

    requests
    | summarize count_=sum(itemCount), avg(duration)
      by bin(timestamp, 1h), client_StateOrProvince, client_City
    | order by timestamp asc, client_StateOrProvince, client_City
```

![Tabla de solicitudes por hora y ubicación](./media/app-insights-analytics-tour/090.png)

### <a name="segment-a-chart-by-dimensions"></a>Segmentación de un gráfico por dimensiones
Si utiliza una tabla que tiene una columna de cadena y una columna numérica, la cadena puede utilizarse para dividir los datos numéricos en series independientes de puntos. Si hay más de una columna de cadena, puede elegir qué columna debe utilizar como discriminador.

![Segmentación de un gráfico de análisis](./media/app-insights-analytics-tour/100.png)

#### <a name="bounce-rate"></a>Tasa de devolución

Convierte un valor booleano en una cadena que se utiliza como discriminador:

```AIQL

    // Bounce rate: sessions with only one page view
    requests
    | where notempty(session_Id)
    | where tostring(operation_SyntheticSource) == "" // real users
    | summarize pagesInSession=sum(itemCount), sessionEnd=max(timestamp)
               by session_Id
    | extend isbounce= pagesInSession == 1
    | summarize count()
               by tostring(isbounce), bin (sessionEnd, 1h)
    | render timechart
```

### <a name="display-multiple-metrics"></a>Muestra de varias métricas
Si utiliza una tabla con más de una columna numérica, además de la marca de tiempo, puede mostrar cualquier combinación de ellas.

![Segmentación de un gráfico de análisis](./media/app-insights-analytics-tour/110.png)

Debe seleccionar **Don't Split** (No dividir) para poder seleccionar varias columnas numéricas. No se puede dividir por una columna de cadena al mismo tiempo que se muestra más de una columna numérica.

## <a name="daily-average-cycle"></a>Ciclo medio diario
¿Cómo varía el uso a lo largo del día normal?

Contar solicitudes por el módulo de tiempo un día, discretizadas en horas:

```AIQL

    requests
    | where timestamp > ago(30d)  // Override "Last 24h"
    | where tostring(operation_SyntheticSource) == "" // real users
    | extend hour = bin(timestamp % 1d , 1h)
          + datetime("2016-01-01") // Allow render on line chart
    | summarize event_count=sum(itemCount) by hour
```

![Gráfico de líneas de horas en un día normal](./media/app-insights-analytics-tour/120.png)

> [!NOTE]
> Observe que actualmente tenemos convertir las duraciones de tiempo en fechas y horas para mostrar en el gráfico de líneas.
>
>

## <a name="compare-multiple-daily-series"></a>Comparación de varias series diarias
¿Cómo varía el uso a lo largo de la hora del día en distintos países?

```AIQL

     requests  
     | where timestamp > ago(30d)  // Override "Last 24h"
     | where tostring(operation_SyntheticSource) == "" // real users
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=sum(itemCount)
       by hour, client_CountryOrRegion
     | render timechart
```

![División por client_CountryOrRegion](./media/app-insights-analytics-tour/130.png)

## <a name="plot-a-distribution"></a>Trazado de una distribución
¿Cuántas sesiones existen de longitudes diferentes?

```AIQL

    requests
    | where timestamp > ago(30d) // override "Last 24h"
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sessionDuration = max_timestamp - min_timestamp
    | where sessionDuration > 1s and sessionDuration < 3m
    | summarize count() by floor(sessionDuration, 3s)
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

La última línea es necesaria para convertir a un valor datetime. Actualmente el eje x de un gráfico se muestra como un valor escalar solo si es un valor datetime.

La cláusula `where` excluye las sesiones únicas (sessionDuration==0) y establece la longitud del eje X.

![](./media/app-insights-analytics-tour/290.png)

## <a name="percentileshttpsdocsloganalyticsioquerylanguagequerylanguagepercentilesaggfunctionhtml"></a>[Percentiles](https://docs.loganalytics.io/queryLanguage/query_language_percentiles_aggfunction.html)
¿Qué intervalos de duraciones cubren diferentes porcentajes de sesiones?

Utilice la consulta anterior, pero reemplace la última línea:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s)
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

También eliminamos el límite superior en la cláusula where con el fin de obtener cifras correctas, incluidas las sesiones con más de una solicitud:

![result](./media/app-insights-analytics-tour/180.png)

De lo que podemos ver que:

* El 5 % de las sesiones tienen una duración de menos de 3 minutos 34 s;
* El 50 % de las sesiones dura menos de 36 minutos;
* El 5 % de las sesiones dura más de 7 días.

Para obtener un desglose independiente para cada país, simplemente tiene que conservar la columna client_CountryOrRegion por separado en ambos operadores de resumen:

```AIQL

    requests
    | where isnotnull(session_Id) and isnotempty(session_Id)
    | summarize min(timestamp), max(timestamp)
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
      by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)

## <a name="join"></a>Unión
Tenemos acceso a varias tablas, incluidas las solicitudes y las excepciones.

Para encontrar las excepciones relacionadas con una solicitud que devolvió una respuesta de error, podemos combinar las tablas en `session_Id`:

```AIQL

    requests
    | where toint(resultCode) >= 500
    | join (exceptions) on operation_Id
    | take 30
```


Es recomendable usar `project` para seleccionar solo las columnas que se necesitan antes de realizar la combinación.
En las mismas cláusulas, cambiamos el nombre de la columna de marca de tiempo.

## <a name="lethttpsdocsloganalyticsioquerylanguagequerylanguageletstatementhtml-assign-a-result-to-a-variable"></a>[Let](https://docs.loganalytics.io/queryLanguage/query_language_letstatement.html): asignación de un resultado a una variable

Use `let` para separar las partes de la expresión anterior. Los resultados no cambian:

```AIQL

    let bad_requests =
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id
    | take 30
```

> [!Tip] 
> En el cliente de Analytics, no incluya líneas en blanco entre las partes de la consulta. Asegúrese de ejecutar todo.
>

Use `toscalar` para convertir una celda de tabla única en un valor:

```AIQL
let topCities =  toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City));
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```


### <a name="functions"></a>Functions

Use *Let* para definir una función:

```AIQL

    let usdate = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests  
    | extend PST = usdate(timestamp-8h)
```

## <a name="accessing-nested-objects"></a>Acceso a objetos anidados
Se puede acceder fácilmente a los objetos anidados. Por ejemplo, en la transmisión de excepciones verá objetos estructurados así:

![result](./media/app-insights-analytics-tour/520.png)

Es posible aplanarlo si selecciona las propiedades que le interesan:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Tenga en cuenta que debe utilizar una conversión al tipo adecuado.


## <a name="custom-properties-and-measurements"></a>Propiedades y medidas personalizadas
Si la aplicación adjunta [dimensiones personalizadas (propiedades) y medidas personalizadas](app-insights-api-custom-events-metrics.md#properties) a eventos, las verá en los objetos `customDimensions` y `customMeasurements`.

Por ejemplo, si la aplicación incluye:

```csharp

    var dimensions = new Dictionary<string, string>
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
    telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Para extraer estos valores en Analytics:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      m1 = todouble(customMeasurements.m1) // cast to expected type

```

Para comprobar si una dimensión personalizada es de un tipo determinado:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1,
      iff(notnull(todouble(customMeasurements.m1)), ...
```

## <a name="dashboards"></a>Paneles
Puede anclar los resultados a un panel para reunir todos los gráficos y tablas más importantes.

* [Panel compartido de Azure](app-insights-dashboards.md#share-dashboards): haga clic en el icono de anclar. Antes de hacerlo, debe tener un panel compartido. En Azure Portal, abra o cree un panel y haga clic en Compartir.
* [Panel de Power BI](app-insights-export-power-bi.md): haga clic en Exportar, Consulta de Power BI. Una ventaja de esta alternativa es que puede mostrar la consulta junto con otros resultados procedentes de una gran variedad de orígenes.

## <a name="combine-with-imported-data"></a>Combinación con datos importados

Los informes de análisis se ven perfectos en el panel, pero a veces desea convertir los datos a una forma más simplificada. Por ejemplo, suponga que los usuarios autenticados se identifican en la telemetría mediante un alias. Le gustaría mostrar sus nombres reales en los resultados. Para ello, necesita un archivo CSV que asigna los nombres reales a partir de los alias.

Puede importar un archivo de datos y usarlo como cualquiera de las tablas estándares (solicitudes, excepciones, etc.). Puede consultarla por sí sola o combinarla con otras tablas. Por ejemplo, si tiene una tabla denominada usermap y tiene las columnas `realName` y `userId`, se puede utilizar para traducir el campo `user_AuthenticatedId` en la telemetría de solicitud:

```AIQL

    requests
    | where notempty(user_AuthenticatedId)
    | project userId = user_AuthenticatedId
      // get the realName field from the usermap table:
    | join kind=leftouter ( usermap ) on userId
      // count transactions by name:
    | summarize count() by realName
```

Para importar una tabla, en la hoja de esquema, en **Other Data Sources** (Otros orígenes de datos), siga las instrucciones para agregar un nuevo origen de datos, mediante la carga de una muestra de los datos. Luego puede usar esta definición para cargar las tablas.

La característica de importación está actualmente en versión preliminar, por lo que verá inicialmente un vínculo "Póngase en contacto con nosotros" en "Other data sources" (Otros orígenes de datos). Úselo para suscribirse al programa de versión preliminar y luego el vínculo se reemplazará por un botón "Add new data source" (Agregar nuevo origen de datos).


## <a name="tables"></a>Tablas
A la transmisión de datos de telemetría recibidos de la aplicación se puede acceder a través de varias tablas. El esquema de propiedades disponibles para cada tabla se puede ver a la izquierda de la ventana.

### <a name="requests-table"></a>Tabla de solicitudes
Contar solicitudes HTTP para su aplicación web y segmento por nombre de página:

![Contar solicitudes segmentadas por nombre](./media/app-insights-analytics-tour/analytics-count-requests.png)

Buscar solicitudes con más errores:

![Contar solicitudes segmentadas por nombre](./media/app-insights-analytics-tour/analytics-failed-requests.png)

### <a name="custom-events-table"></a>Tabla de eventos personalizados
Si utiliza [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent) para enviar sus propios eventos, puede leerlo desde esta tabla.

Veamos un ejemplo en el que el código de aplicación contiene estas líneas:

```csharp

    telemetry.TrackEvent("Query",
       new Dictionary<string,string> {{"query", sqlCmd}},
       new Dictionary<string,double> {
           {"retry", retryCount},
           {"querytime", totalTime}})
```

Mostrar la frecuencia de estos eventos:

![Mostrar la tasa de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-rate.png)

Extraer las medidas y dimensiones de los eventos:

![Mostrar la tasa de eventos personalizados](./media/app-insights-analytics-tour/analytics-custom-events-dimensions.png)

### <a name="custom-metrics-table"></a>Tabla de métricas personalizadas
Si utiliza [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) para enviar sus propios valores de métrica, encontrará los resultados en la transmisión **customMetrics**. Por ejemplo:   

![Métricas personalizadas en Application Insights Analytics](./media/app-insights-analytics-tour/analytics-custom-metrics.png)

> [!NOTE]
> En el [Explorador de métricas](app-insights-metrics-explorer.md), todas las medidas personalizadas adjuntas a cualquier tipo de telemetría aparecen juntas en la hoja de métricas, junto con las métricas enviadas mediante `TrackMetric()`. Sin embargo, en Analytics, las medidas personalizadas siguen adjuntas al tipo de telemetría en que se realizaron (eventos, solicitudes, etc.), mientras que las enviadas por TrackMetric aparecen en su propia transmisión.
>
>

### <a name="performance-counters-table"></a>Tabla de contadores de rendimiento
Los [contadores de rendimiento](app-insights-performance-counters.md) muestran métricas del sistema básico de la aplicación, como CPU, memoria y la utilización de la red. Puede configurar el SDK para que envíe contadores adicionales, entre los que se incluyen sus propios contadores.

El esquema **performanceCounters** expone `category`, el nombre de `counter` y el nombre de `instance` de cada contador de rendimiento. Los nombres de instancia de contador solo se pueden aplicar a algunos contadores de rendimiento y suele indicar el nombre del proceso con el que está relacionado el recuento. En la telemetría de cada aplicación, solo se ven los contadores de dicha aplicación. Por ejemplo, para ver qué contadores están disponibles:

![Contadores de rendimiento en Application Insights Analytics](./media/app-insights-analytics-tour/analytics-performance-counters.png)

Para obtener un gráfico de la memoria disponible en un período seleccionado:

![Gráfico de tiempo de la memoria in Application Insights Analytics](./media/app-insights-analytics-tour/analytics-available-memory.png)

Al igual que otros datos de telemetría, **performanceCounters** también tiene una columna `cloud_RoleInstance` que indica la identidad del equipo host en el que se ejecuta la aplicación. Por ejemplo, para comparar el rendimiento de una aplicación en distintas máquinas:

![Rendimiento segmentado por instancia de rol en Application Insights Analytics](./media/app-insights-analytics-tour/analytics-metrics-role-instance.png)

### <a name="exceptions-table"></a>Tabla de excepciones
[Las excepciones que notifica la aplicación](app-insights-asp-net-exceptions.md) están disponibles en esta tabla.

Para buscar la solicitud HTTP que controlaba la aplicación cuando se produjo la excepción, combine operation_Id:

![Combinar excepciones con solicitudes en operation_Id](./media/app-insights-analytics-tour/analytics-exception-request.png)

### <a name="browser-timings-table"></a>Tabla de intervalos de explorador
`browserTimings` muestra los datos de carga de las páginas recopilados en los exploradores de los usuarios.

[Configure su aplicación para la telemetría del lado cliente](app-insights-javascript.md) para ver estas métricas.

El esquema incluye [métricas que indican las duraciones de las distintas fases del proceso de carga de las páginas](app-insights-javascript.md#page-load-performance) (no indican el periodo durante el que los usuarios leen una página).  

Mostrar la popularidad de las distintas páginas y los tiempos de carga de cada página:

![Tiempos de carga de páginas en Analytics](./media/app-insights-analytics-tour/analytics-page-load.png)

### <a name="availability-results-table"></a>Tabla de resultados de la disponibilidad
`availabilityResults` muestra los resultados de sus [pruebas web](app-insights-monitor-web-app-availability.md). Cada ejecución de las pruebas en cada una de las ubicaciones se notifica por separado.

![Tiempos de carga de páginas en Analytics](./media/app-insights-analytics-tour/analytics-availability.png)

### <a name="dependencies-table"></a>Tabla de dependencias
Contiene los resultados de las llamadas que su aplicación realiza a las bases de datos y a las API de REST, así como de otras llamadas a TrackDependency(). También incluye las llamadas AJAX realizadas desde el explorador.

Llamadas AJAX desde el explorador:

```AIQL

    dependencies | where client_Type == "Browser"
    | take 10
```

Llamadas de dependencia desde el servidor:

```AIQL

    dependencies | where client_Type == "PC"
    | take 10
```

Los resultados de la dependencia de servidor siempre muestran `success==False` si no está instalado el agente de Application Insights. Sin embargo, los demás datos son correctos.

### <a name="traces-table"></a>Tabla de seguimientos
Contiene los datos de telemetría que ha enviado la aplicación mediante TrackTrace(), u [otras plataformas de registro](app-insights-asp-net-trace-logs.md).

## <a name="video"></a>Vídeo 

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

Consultas avanzadas:

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/P591/player]


## <a name="next-steps"></a>pasos siguientes
* [Referencia de idioma de Analytics](app-insights-analytics-reference.md)
* [La hoja de referencia rápida de usuarios de SQL](https://aka.ms/sql-analytics) traduce las expresiones más comunes.

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]
