<properties 
	pageTitle="Un paseo por Application Insights Analytics" 
	description="Ejemplos breves de todas las principales consultas de Application Insights Analytics, la potente herramienta de búsqueda de Application Insights." 
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


 
# Un paseo por Application Insights Analytics


Application Insights Analytics es un potente motor de búsqueda de diagnósticos para la telemetría de [Application Insights](app-insights-overview.md).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Recorramos algunas preguntas básicas para comenzar.

## Conexión a los datos de Application Insights

Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su aplicación en Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Analytics.](./media/app-analytics/001.png)

## Recuento (count) de filas

Las métricas como contadores de rendimiento se almacenan en una tabla llamada métricas. Cada fila es un punto de datos de telemetría procedente del SDK de Application Insights en una aplicación. Para averiguar el tamaño de la tabla, canalizaremos su contenido a un operador que sencillamente cuenta las filas:

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Coloque el cursor en algún lugar de la instrucción antes de hacer clic en Go (Ir). Puede dividir una instrucción en varias líneas, pero no incluya líneas en blanco en una instrucción. Para mantener varias consultas en la ventana, sepárelas con líneas en blanco.

Este es el resultado:


![](./media/app-analytics-tour/010.png)

	
`Count` es uno de los muchos [operadores de consulta](app-analytics-queries.md) que podemos organizar en una canalización, filtrado, reforma y unión de datos en varias fases.
	
## Take: Mostrarme n filas


Veamos algunos datos: ¿Qué hay en un ejemplo de 5 filas?

```AIQL

	requests | take 5
```

Y aquí está lo que obtenemos:

![results](./media/app-analytics-tour/020.png)

Elija las columnas y ajuste sus posiciones:

![Hacer clic en la selección de columna en la parte superior derecha de los resultados](./media/app-analytics-tour/030.png)

Expanda cualquier elemento para ver los detalles:
 
![Seleccione Table (Tabla) y use Configure Columns (Configurar columnas).](./media/app-analytics-tour/040.png)


## Top y sort

`take` resulta útil para obtener un ejemplo rápido de un resultado, pero muestra filas de la tabla sin ningún orden determinado. Para obtener una vista ordenada, use `top` (para un ejemplo) o `sort` (toda la tabla).

Muéstrame las primeras n filas, ordenadas por una columna en particular:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Sintaxis:* La mayoría de los operadores tienen parámetros de palabra clave como `by`.
* `desc` = orden descendente, `asc` = ascendente.

![](./media/app-analytics-tour/260.png)

`top...` es una manera más eficiente más de decir `sort ... | take...`. Podríamos haber escrito:

```AIQL

	requests | sort by timestamp desc | take 10
```

El resultado sería el mismo, pero se ejecutaría un poco más lento. (También podría escribir `order`, que es un alias de `sort`.)

Los encabezados de columna en la vista de tabla también pueden utilizarse para ordenar los resultados en la pantalla. Pero por supuesto, si ha usado `take` o `top` para recuperar solo parte de una tabla, solamente reordenará los registros que ha recuperado.


## Project: Seleccionar, cambiar el nombre y calcular columnas

Use `project` para seleccionar solamente las columnas que desea:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


También puede cambiar el nombre de las columnas y definir otras nuevas:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

En la expresión escalar:

* `%` es el operador de módulo habitual. 
* `1d` (es decir, el dígito uno y luego una 'd') es un literal de intervalo de tiempo que significa un día. A continuación se indican algunos literales de intervalo de tiempo más: `12h`, `30m`, `10s` y `0.01s`.
* `floor` (alias `bin`) redondea un valor a la baja al múltiplo más cercano del valor base que se proporciona. Por tanto, `floor(aTime, 1s)` redondea un tiempo a la baja al segundo más cercano.

Las [expresiones](app-analytics-scalars.md) pueden incluir todos los operadores habituales (`+`, `-`, ...) y hay una amplia gama de funciones útiles.

## Extend: Calcular columnas

Si solo desea agregar columnas a las ya existentes, use `extend`:

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

El uso de `extend` es menos detallado que `project` si desea conservar todas las columnas existentes.

## Summarize: Agregar grupos de filas

Al examinar un ejemplo de una tabla, podemos ver los campos en los que se informa de los diferentes datos de telemetría. Por ejemplo, `exception | take 20` rápidamente nos muestra que se informa de los mensajes de excepción en un campo llamado `outerExceptionType`.

Pero en lugar de penetrar a través de instancias individuales, vamos preguntar cuántas excepciones se han notificado de cada tipo:

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` agrupa las filas que tienen los mismos valores en los campos con nombre en la cláusula `by`, produciendo una sola fila de resultados para cada grupo. En este caso, hay una fila para cada tipo de excepción. La función de agregación `count()` cuenta las filas de cada grupo, proporcionando una columna en el resultado.


Hay una amplia gama de [funciones de agregación](app-analytics-aggregations.md) y puede utilizar varias de ellas en un operador de resumen para generar varias columnas calculadas.

Por ejemplo, vamos a mostrar las solicitudes HTTP para las que se producen estas excepciones. De nuevo, mediante la inspección de un ejemplo de la tabla de excepciones, observará que las rutas de acceso de las solicitudes HTTP se notifican en una columna llamada `operation_Name`.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

La función de agregación `makeset()` crea un conjunto de todos los valores especificados en cada grupo. Como ocurre en este ejemplo, hay una única operación que da lugar a cada excepción.


El resultado de un resumen tiene:

* cada columna con nombre en `by`;
* más una columna para cada expresión de agregación;
* una fila para cada combinación de `by` valores.


## Resumen por valores escalares


Puede usar valores escalares (numéricos, tiempo o intervalo) en la cláusula by. Sin embargo, los números normalmente rellenan un intervalo continuo. Para agrupar los puntos de datos, deberá asignarlos a valores binarios de valores discretos. La función `bin` es útil para esto:

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` reduce todas las marcas de tiempo a intervalos de 1 día. Es un alias de `floor`, una función conocida de la mayoría de los lenguajes. Simplemente reduce cada valor al múltiplo más cercano del módulo que se especifica, por lo que `summarize` puede asignar las filas a grupos de un tamaño razonable. (Sin él, tendríamos una fila de resultados para cada fracción de segundo independiente, que no resumiría los datos en absoluto.)

Podemos hacerlo mejor que la vista de la tabla. Echemos un vistazo a los resultados en la vista del gráfico con la opción de barra vertical:

![Haga clic Chart (Gráfico) y después elija Vertical bar chart (Gráfico de barras verticales) y asigne los ejes x e y.](./media/app-analytics-tour/230.png)

Tenga en cuenta que aunque no ordenamos los resultados por tiempo (como puede ver en la visualización de la tabla), la visualización del gráfico siempre muestra las fechas en el orden correcto.


## Where: Filtrado sobre una condición

Si ha configurado la supervisión de Application Insights tanto para el [cliente](app-insights-javascript.md) como para el servidor de la aplicación, parte de la telemetría de la base de datos procede de los exploradores.

Veamos solo las excepciones de las que informan los exploradores:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

El operador `where` toma una expresión booleana. He aquí algunos puntos clave:

 * `and`, `or`: Operadores booleanos
 * `==`, `<>`: Igual que y no igual que
 * `=~`, `!=`: Cadena que no distingue entre mayúsculas y minúsculas igual que y no igual que. Hay muchos más operadores de comparación de cadenas.

Lea todo sobre [expresiones escalares](app-analytics-scalars.md).

### Filtrado de eventos

Buscar solicitudes incorrectas:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` tiene el tipo de cadena, por lo que debemos [convertirla](app-analytics-scalars.md#casts) para una comparación numérica.

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

![timechart](./media/app-analytics-tour/080.png)

El eje x para los gráficos de líneas debe ser de tipo DateTime.

## Varias series 

Use varios valores en un cláusula `summarize by` para crear una fila independiente para cada combinación de valores:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Para mostrar varias líneas en un gráfico, haga clic en **Split by (Dividir por)** y elija una columna.

![](./media/app-analytics-tour/100.png)



## Ciclo medio diario

¿Cómo varía el uso a lo largo del día normal?

Contar solicitudes por el módulo de tiempo un día, discretizadas en horas:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Gráfico de líneas de horas en un día normal](./media/app-analytics-tour/120.png)

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

![Dividir por client\_StateOrProvince](./media/app-analytics-tour/130.png)


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

La cláusula `where` excluye las sesiones monoestables (sessionDuration == 0) y establece la longitud del eje x.


![](./media/app-analytics-tour/290.png)



## Percentiles

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

![result](./media/app-analytics-tour/180.png)

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

![](./media/app-analytics-tour/190.png)


## Join

Tenemos acceso a tres tablas: métrica, excepciones y evento. `event` contiene informes de solicitud, vistas de página, eventos personalizados, etc.

Para encontrar las excepciones relacionadas con una solicitud que devolvió una respuesta de error, podemos combinar las tablas en `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Es recomendable usar `project` para seleccionar solo las columnas que necesitamos antes de realizar la combinación. En las mismas cláusulas, cambiamos el nombre de la columna de marca de tiempo.



## Let: Asignar un resultado a una variable

Use [let](./app-analytics-syntax.md#let-statements) para separar las partes de la expresión anterior. Los resultados no cambian:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Sugerencia: En el cliente de AI Analytics, no incluya líneas en blanco entre las partes de esto. Asegúrese de ejecutar todo.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->