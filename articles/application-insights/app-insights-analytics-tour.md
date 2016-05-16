<properties 
	pageTitle="Un paseo por Analytics de Application Insights" 
	description="Ejemplos breves de todas las principales consultas de Analytics, la potente herramienta de búsqueda de Application Insights." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>


 
# Un paseo por Analytics de Application Insights


[Analytics](app-insights-analytics.md) es la característica de búsqueda eficaz de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]
 
Recorramos algunas preguntas básicas para comenzar.

## Conexión a los datos de Application Insights

Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics/001.png)

	
## [Take](app-insights-analytics-aggregations.md#take): mostrarme n filas

Los puntos de datos que registran las operaciones de usuario (normalmente solicitudes HTTP recibidas mediante la aplicación web) se almacenan en una tabla denominada `requests`. Cada fila es un punto de datos de telemetría procedente del SDK de Application Insights en una aplicación.

Comencemos examinando algunas filas de ejemplo de la tabla:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Coloque el cursor en algún lugar de la instrucción antes de hacer clic en Go (Ir). Puede dividir una instrucción en varias líneas, pero no incluya líneas en blanco en una instrucción. Las líneas en blanco son una forma práctica de tener varias consultas diferentes en la ventana.


Elija las columnas y ajuste sus posiciones:

![Hacer clic en la selección de columna en la parte superior derecha de los resultados](./media/app-insights-analytics-tour/030.png)


Expanda cualquier elemento para ver los detalles:
 
![Seleccione Table (Tabla) y use Configure Columns (Configurar columnas).](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Haga clic en el encabezado de una columna para cambiar el orden de los resultados disponibles en el explorador web. Tenga en cuenta que, para un conjunto grande de resultados, el número de filas que se descargan en el explorador es limitado. Recuerde que esta forma de ordenación no siempre muestra los elementos mayores o menores reales. Para ello, debería usar el operador `top` o `sort`.

## [Top](app-insights-analytics-aggregations.md#top) y [sort](app-insights-analytics-aggregations.md#sort)

`take` resulta útil para obtener un ejemplo rápido de un resultado, pero muestra filas de la tabla sin ningún orden determinado. Para obtener una vista ordenada, use `top` (para un ejemplo) o `sort` (toda la tabla).

Muéstrame las primeras n filas, ordenadas por una columna en particular:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Sintaxis:* la mayoría de los operadores tienen parámetros de palabra clave como `by`.
* `desc` = orden descendente, `asc` = orden ascendente.

![](./media/app-insights-analytics-tour/260.png)

`top...` es una manera más eficiente de decir `sort ... | take...`. Podríamos haber escrito:

```AIQL

	requests | sort by timestamp desc | take 10
```

El resultado sería el mismo, pero se ejecutaría un poco más lento. (También podría escribir `order`, que es un alias de `sort`).

Los encabezados de columna en la vista de tabla también pueden utilizarse para ordenar los resultados en la pantalla. Pero por supuesto, si ha usado `take` o `top` para recuperar solo parte de una tabla, solamente reordenará los registros que ha recuperado.


## [Project](app-insights-analytics-aggregations.md#project): seleccionar, cambiar el nombre y calcular columnas

Use [`project`](app-insights-analytics-aggregations.md#project) para seleccionar solamente las columnas que desea:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


También puede cambiar el nombre de las columnas y definir otras nuevas:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-insights-analytics-tour/270.png)

En la expresión escalar:

* `%` es el operador de módulo habitual. 
* `1d` (es decir, el dígito uno y luego una "d") es un literal de intervalo de tiempo que significa un día. Aquí se indican otros literales de intervalo de tiempo: `12h`, `30m`, `10s` y `0.01s`.
* `floor` (alias `bin`) redondea un valor a la baja al múltiplo más cercano del valor base que proporciona. Por lo tanto, `floor(aTime, 1s)` redondea un tiempo a la baja al segundo más cercano.

Las [expresiones](app-insights-analytics-scalars.md) pueden incluir todos los operadores habituales (`+`, `-`, ...) y hay una amplia variedad de funciones útiles.

## [Extend](app-insights-analytics-aggregations.md#extend): calcular columnas

Si solo desea agregar columnas a las ya existentes, use [`extend`](app-insights-analytics-aggregations.md#extend):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

El uso de [`extend`](app-insights-analytics-aggregations.md#extend) es menos detallado que [`project`](app-insights-analytics-aggregations.md#project) si desea conservar todas las columnas existentes.

## [Summarize](app-insights-analytics-aggregations.md#summarize): agregar grupos de filas

`Summarize` aplica una *función de agregación* específica sobre grupos de filas.

Por ejemplo, el tiempo que su aplicación web tarda en responder una solicitud se notifica en el campo `duration`. Veamos el tiempo medio de respuesta de todas las solicitudes:

![](./media/app-insights-analytics-tour/410.png)

O bien, podríamos separar el resultado en solicitudes de nombres diferentes:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` recopila los puntos de datos de la secuencia en grupos que la cláusula `by` calcula por igual. Cada valor de la expresión `by` (cada nombre de la operación en el ejemplo anterior), da como resultado una fila de la tabla de resultados.

O bien, podríamos agrupar los resultados en función de la hora del día:

![](./media/app-insights-analytics-tour/430.png)

Observe cómo usamos la función `bin` (también conocida como `floor`). Si solo usáramos `by timestamp`, cada fila de entrada terminaría en su pequeño grupo. Para cualquier escalar continuo, como horas o números, tenemos que dividir el intervalo continuo en un número manejable de valores discretos y `bin`, que es realmente solo la función `floor` de redondeo a la baja, es la forma más sencilla de hacerlo.

Podemos usar la misma técnica para reducir los intervalos de cadenas:


![](./media/app-insights-analytics-tour/440.png)

Tenga en cuenta que puede usar `name=` para establecer el nombre de una columna de resultados, en las expresiones de agregación o mediante cláusula.

## Recuento de datos muestreados

`sum(itemCount)` es la agregación recomendada para contar eventos. En muchos casos, itemCount == 1, por lo que la función simplemente cuenta el número de filas del grupo. Pero cuando [muestreo](app-insights-sampling.md) está en funcionamiento, solo se retendrá una fracción de los eventos originales como punto de datos en Application Insights, por lo que para cada punto de datos que vea, existen `itemCount` eventos. Por lo tanto, resumir itemCount proporciona una buena estimación del número original de eventos.


![](./media/app-insights-analytics-tour/510.png)

También existe una agregación de `count()` (y una operación de recuento) para los casos en los que realmente desea contar el número de filas de un grupo.


Existe una variedad de [funciones de agregación](app-insights-analytics-aggregations.md).


## Crear gráficos de los resultados



```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

De forma predeterminada, los resultados se muestran como una tabla:

![](./media/app-insights-analytics-tour/225.png)


Podemos hacerlo mejor que la vista de la tabla. Echemos un vistazo a los resultados en la vista del gráfico con la opción de barra vertical:

![Haga clic Chart (Gráfico) y después elija Vertical bar chart (Gráfico de barras verticales) y asigne los ejes x e y.](./media/app-insights-analytics-tour/230.png)

Tenga en cuenta que aunque no ordenamos los resultados por tiempo (como puede ver en la visualización de la tabla), la visualización del gráfico siempre muestra las fechas en el orden correcto.


## [Where](app-insights-analytics-aggregations.md#where): filtrado de una condición

Si ha configurado la supervisión de Application Insights tanto para el [cliente](app-insights-javascript.md) como para el servidor de la aplicación, parte de la telemetría de la base de datos procede de los exploradores.

Veamos solo las excepciones de las que informan los exploradores:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

El operador `where` toma una expresión booleana. He aquí algunos puntos clave:

 * `and`, `or`: operadores booleanos
 * `==`, `<>`: igual que y no igual que
 * `=~`, `!=`: cadena que no distingue entre mayúsculas y minúsculas igual que y no igual que. Hay muchos más operadores de comparación de cadenas.

Lea toda la información acerca de las [expresiones escalares](app-insights-analytics-scalars.md).

### Filtrado de eventos

Buscar solicitudes incorrectas:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` tiene el tipo de cadena, por lo que debemos [convertirlo](app-insights-analytics-scalars.md#casts) para una comparación numérica.

Resumir las diferentes respuestas:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Gráficos de tiempo

Mostrar cuántos eventos hay cada día:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Seleccionar la opción de visualización de gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

El eje x para los gráficos de líneas debe ser de tipo DateTime.

## Varias series 

Use varios valores en un cláusula `summarize by` para crear una fila independiente para cada combinación de valores:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Para mostrar varias líneas en un gráfico, haga clic en **Dividir por** y elija una columna.

![](./media/app-insights-analytics-tour/100.png)



## Ciclo medio diario

¿Cómo varía el uso a lo largo del día normal?

Contar solicitudes por el módulo de tiempo un día, discretizadas en horas:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Gráfico de líneas de horas en un día normal](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Observe que actualmente tenemos convertir las duraciones de tiempo en fechas y horas para mostrar en el gráfico.


## Comparación de varias series diarias

¿Cómo varía el uso a lo largo de la hora del día en distintos estados?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Dividir el gráfico por estado:

![Dividir por client\_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## Trazado de una distribución

¿Cuántas sesiones existen de longitudes diferentes?

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

La última línea es necesaria para convertir a fecha y hora (actualmente, el eje x de un gráfico de líneas solo puede ser una fecha y hora).

La cláusula `where` excluye las sesiones únicas (sessionDuration==0) y establece la longitud del eje x.


![](./media/app-insights-analytics-tour/290.png)



## [Percentiles](app-insights-analytics-aggregations.md#percentiles)

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
* El 50 % de las sesiones dura menos de 36 minutos;
* El 5 % de las sesiones dura más de 7 días.

Para obtener un desglose independiente para cada país, simplemente tiene que conservar la columna client\_CountryOrRegion por separado en ambos operadores de resumen:

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


## [Join](app-insights-analytics-aggregations.md#join)

Tenemos acceso a varias tablas, incluidas las solicitudes y las excepciones.

Para encontrar las excepciones relacionadas con una solicitud que devolvió una respuesta de error, podemos combinar las tablas en `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Es recomendable usar `project` para seleccionar solo las columnas que necesitamos antes de realizar la combinación. En las mismas cláusulas, cambiamos el nombre de la columna de marca de tiempo.



## [Let](app-insights-analytics-aggregations.md#let): asignar un resultado a una variable

Use [let](./app-insights-analytics-syntax.md#let-statements) para separar las partes de la expresión anterior. Los resultados no cambian:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Sugerencia: en el cliente de Analytics, no incluya líneas en blanco entre las partes. Asegúrese de ejecutar todo.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0504_2016-->