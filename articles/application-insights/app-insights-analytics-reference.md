<properties 
	pageTitle="Referencia de Analytics de Application Insights" 
	description="Referencia de instrucciones de Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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
	ms.date="05/26/2016" 
	ms.author="awills"/>

# Referencia para Analytics

[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

| | | | | 
|---|---|---|---|---
|[abs](#abs)|[dayofweek](#dayofweek)|[isnull](#isnull)|[rand](#rand)|[Operador summarize](#summarize-operator)
|[ago](#ago)|[dayofyear](#dayofyear)|[Operador join](#join-operator)|[range](#range)|[Operador take](#take-operator)
|[cualquiera](#any)|[dcount](#dcount)|[Expresiones de ruta de JSON](#json-path-expressions)|[Operador range](#range-operator)|[todatetime](#todatetime)
|[argmax](#argmax)|[dcountif](#dcountif)|[Cláusula let](#let-clause)|[Operador reduce](#reduce-operator)|[todouble](#todouble)
|[argmin](#argmin)|[Objetos dinámicos en cláusulas let](#dynamic-objects-in-let-clauses)|[Operador limit](#limit-operator)|[Directiva render](#render-directive)|[todynamic](#todynamic)
|[Operadores aritméticos](#arithmetic-operators)|[endofday](#endofday)|[log](#log)|[replace](#replace)|[toint](#toint)
|[Literales de matriz y objeto](#array-and-object-literals)|[endofmonth](#endofmonth)|[makelist](#makelist)|[Cláusula restrict](#restrict-clause)|[tolong](#tolong)
|[arraylength](#arraylength)|[endofweek](#endofweek)|[makeset](#makeset)|[Comparaciones escalares](#scalar-comparisons)|[tolower](#tolower)
|[avg](#avg)|[endofyear](#endofyear)|[max](#max)|[Operador sort](#sort-operator)|[Operador top](#top-operator)
|[bin](#bin)|[exp](#exp)|[min](#min)|[split](#split)|[top-nested op](#top-nested-operator)
|[Literales booleanos](#boolean-literals)|[Operador extend](#extend-operator)|[Operador mvexpand](#mvexpand-operator)|[sqrt](#sqrt)|[toscalar](#toscalar)
|[Operadores booleanos](#boolean-operators)|[extract](#extract)|[notempty](#notempty)|[startofday](#startofday)|[totimespan](#totimespan)
|[buildschema](#buildschema)|[extractjson](#extractjson)|[notnull](#notnull)|[startofmonth](#startofmonth)|[toupper](#toupper)
|[Conversiones de tipos](#casts)|[floor](#floor)|[now](#now)|[startofweek](#startofweek)|[treepath](#treepath)
|[count](#count)|[getmonth](#getmonth)|[Literales numéricos](#numeric-literals)|[startofyear](#startofyear)|[Operador union](#union-operator)
|[Operador count](#count-operator)|[gettype](#gettype)|[Literales de cadena ofuscados](#obfuscated-string-literals)|[stdev](#stdev)|[variance](#variance)
|[countif](#countif)|[getyear](#getyear)|[Operador parse](#parse-operator)|[strcat](#strcat)|[WeekOfYear](#weekofyear)
|[countof](#countof)|[hash](#hash)|[parsejson](#parsejson)|[Comparaciones de cadenas](#string-comparisons)|[Operador where](#where-operator)
|[Expresiones de fecha y hora](#date-and-time-expressions)|[iff](#iff)|[percentile](#percentile)|[Literales de cadena](#string-literals)
|[Literales de fecha y hora](#date-and-time-literals)|[isempty](#isempty)|[percentiles](#percentiles)|[strlen](#strlen)
|[datepart](#datepart)|[isnotempty](#isnotempty)|[Operador project](#project-operator)|[substring](#substring)
|[dayofmonth](#dayofmonth)|[isnotnull](#isnotnull)|[Operador project-away](#project-away-operator)|[sum](#sum)



## Consultas y operadores

Una consulta sobre datos de telemetría se compone de una referencia a una secuencia de origen, seguida de una canalización de filtros. Por ejemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro precedido por el carácter de barra vertical `|` es una instancia de un *operador* con algunos parámetros. La entrada del operador es la tabla que resulta de la canalización anterior. En la mayoría de los casos, los parámetros son [expresiones escalares](##scalars) sobre las columnas de entrada. En algunos casos, los parámetros son los nombres de las columnas de entrada y, en otros casos, el parámetro es una segunda tabla. El resultado de una consulta siempre es una tabla, incluso si solo tiene una columna y una fila.

Las consultas pueden contener saltos de línea únicos, pero terminan en una línea en blanco. Pueden contener comentarios entre `//` y el final de la línea.

Una consulta puede ir precedida por una o más [cláusulas let](#let-clause) que definen valores escalares, tablas o funciones que pueden utilizarse dentro de la consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` se utiliza en los siguientes ejemplos de consultas para indicar la tabla de origen o canalización anterior.
> 

### Operador count

El operador `count` devuelve el número de registros (filas) en el conjunto de registros de entrada.

**Sintaxis**

    T | count

**Argumentos**

* *T*: los datos tabulares cuyos registros se van a contabilizar.

**Devoluciones**

Esta función devuelve una tabla con un único registro y una columna de tipo `long`. El valor de la celda única es el número de registros en *T*.

**Ejemplo**

```AIQL
requests | count
```



### Operador extend

     T | extend duration = stopTime - startTime

Anexa una o más columnas calculadas a una tabla.


**Sintaxis**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* la tabla de entrada.
* *ColumnName:* el nombre de una columna que se va a agregar. Los [nombres](#names) distinguen mayúsculas de minúsculas y pueden contener caracteres alfabéticos, numéricos o de subrayado '\_'. Use `['...']` o `["..."]` para entrecomillar palabras clave o nombres con otros caracteres.
* *Expression:* un cálculo con las columnas existentes.

**Devoluciones**

Una copia de la tabla de entrada con las columnas adicionales especificadas.

**Sugerencias**

* Use como alternativa [`project`](#project-operator) si también desea quitar o cambiar el nombre de algunas columnas.
* No use `extend` simplemente para obtener un nombre más corto que usar en una expresión larga. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Las columnas originales de la tabla se han indexado; el nuevo nombre define una columna adicional que no está indexada, por lo que posiblemente la consulta se ejecute con lentitud

**Ejemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### Operador join

    Table1 | join (Table2) on CommonColumn

Combina las filas de dos tablas haciendo coincidir los valores de la columna especificada.


**Sintaxis**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Table1:* el "lado izquierdo" de la combinación.
* *Table2:* el "lado derecho" de la combinación. Puede ser una expresión de consulta anidada que genera una tabla.
* *CommonColumn:* una columna que tiene el mismo nombre en las dos tablas.
* *Kind:* especifica cómo se hacen coincidir las filas de las dos tablas.

**Devoluciones**

Una tabla con:

* Una columna por cada columna en cada una de las dos tablas, incluidas las claves coincidentes. Si hay conflictos de nombres las columnas de la derecha se cambiarán automáticamente de nombre.
* Una fila por cada correspondencia entre las tablas de entrada. Una coincidencia es una fila seleccionada de una tabla que tiene el mismo valor para todos los campos `on` que una fila en la otra tabla. 

* `Kind` sin especificar.

    Solo se hace corresponder una fila del lado izquierdo para cada valor de la clave `on`. La salida contiene una fila por cada coincidencia de esta fila con filas de la derecha.

* `Kind=inner`
 
     Hay una fila en la salida por cada combinación de filas coincidentes de la izquierda y la derecha.

* `kind=leftouter` (`kind=rightouter` o `kind=fullouter`).

     Además de las coincidencias internas, hay una fila por cada fila de la izquierda (o derecha), incluso si no tiene ninguna coincidencia. En este caso, las celdas de salida sin coincidencias contienen valores NULL.

* `kind=leftanti`

     Devuelve todos los registros del lado izquierdo que no tienen coincidencias en el derecho. La tabla resultante solo tiene las columnas del lado izquierdo.
 
Si hay varias filas con los mismos valores para esos campos, obtendrá filas para todas las combinaciones.

**Sugerencias**

Para obtener el mejor rendimiento:

* Use `where` y `project` para reducir el número de filas y columnas en las tablas de entrada antes de `join`. 
* Si una tabla siempre es menor que la otro, úsela como lado izquierdo (canalizado) de la combinación.
* Las columnas para la coincidencia de la combinación tienen que tener el mismo nombre. Utilice el operador project si es necesario cambiar el nombre de una columna en una de las tablas.

**Ejemplo**

Obtener más actividades de un registro en el que algunas entradas marcan el inicio y fin de una actividad.

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

### Cláusula let

**Cláusula let tabular: dar nombre a una tabla**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Cláusula let escalar: dar nombre a un valor**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Cláusula let lambda: dar nombre a una función**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

Una cláusula let enlaza un [nombre](#names) a un resultado tabular, un valor escalar o una función. La cláusula es un prefijo a una consulta y el ámbito del enlace es esa consulta. (La cláusula let no proporciona una forma para dar nombre a elementos que se utilizan más adelante en la sesión.)

**Sintaxis**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type).
* *plain\_query:* una consulta no precedida por una cláusula let.

**Ejemplos**

    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


Autocombinación:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

### Operador limit

     T | limit 5

Vuelve al número especificado de filas de la tabla de entrada. No hay ninguna garantía de cuáles son los registros que se devuelven. (Para devolver registros específicos, use [`top`](#top-operator)).

**Alias** `take`

**Sintaxis**

    T | limit NumberOfRows


**Sugerencias**

`Take` es una manera sencilla y eficaz de ver un ejemplo de los resultados cuando se está trabajando de forma interactiva. Tenga en cuenta que no hay garantía de que se generen ningunas filas específicas o de que lo hagan en un orden determinado.

Hay un límite implícito en el número de filas devueltas al cliente, aunque no utilice `take`. Para eliminar este límite, use la opción de solicitud de cliente `notruncation`.



### Operador mvexpand

    T | mvexpand listColumn 

Expande una lista de una celda de tipo dinámico (JSON) para que cada entrada tenga una fila independiente. Todas las demás celdas de una fila expandida se duplican.

(Consulte también [`summarize makelist`](#summarize-operator), que realiza la función opuesta).

**Ejemplo**

Supongamos que la tabla de entrada es:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hola"|{"key":"value"}|
|2|"mundo"|[0,1, "k", "v"]|

    mvexpand D

El resultado es:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hola"|{"key":"value"}|
|2|"mundo"|0|
|2|"mundo"|1|
|2|"mundo"|K|
|2|"mundo"|V|


**Sintaxis**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumentos**

* *ColumnName:* en el resultado, las matrices en la columna indicada se expanden a varias filas. 
* *ArrayExpression:* expresión que produce una matriz. Si se usa esta forma, se agrega una nueva columna y se conserva la existente.
* *Name:* un nombre para la nueva columna.
* *Typename:* convierte la expresión expandida en un tipo determinado.
* *RowLimit:* el número máximo de filas generadas a partir de cada fila original. El valor predeterminado es 128.

**Devoluciones**

Varias filas para cada uno de los valores de una matriz en la columna indicada o en la expresión de matriz.

La columna expandida siempre tiene el tipo dinámico. Use una conversión como `todatetime()` o `toint()` si desea calcular o agregar valores.

Se admiten dos modos de expansiones de contenedor de propiedades:

* `bagexpansion=bag`: los contenedores de propiedades se expanden en contenedores de propiedades de entrada única. Esta es la expansión predeterminada.
* `bagexpansion=array`: los contenedores de propiedades se expanden en estructuras de matriz de dos elementos `[`*key*`,`*value*`]`, lo que permite el acceso uniforme a claves y valores (así como, por ejemplo, ejecutar una agregación de recuento distintiva sobre los nombres de propiedad). 

**Ejemplos**


    exceptions | take 1 
    | mvexpand details[0]

Divide un registro de excepciones en filas para cada elemento en el campo de detalles.



### Operador parse

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind="relaxed"
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Extrae los valores de una cadena. Puede usar la coincidencia de expresión regular o simple.

**Sintaxis**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumentos**

* `T`: la tabla de entrada.
* `kind`: 
 * `simple` (valor predeterminado): las cadenas `Match` son cadenas sin formato.
 * `relaxed`: si el texto no se analiza como el tipo de una columna, la columna se establece en null y el análisis continúa. 
 * `regex`: las cadenas `Match` son expresiones regulares.
* `Text`: una columna u otra expresión que se evalúa como cadena o se puede convertir en una.
* *Match:* compara la siguiente parte de la cadena y la descarta.
* *Column:* asigna la siguiente parte de la cadena a esta columna. Si no existe, se crea la columna.
* *Type:* analiza la siguiente parte de la cadena como el tipo especificado, como int, date, double. 


**Devoluciones**

La tabla de entrada ampliada según la lista de columnas.

Los elementos de la cláusula `with` se comparan a su vez con el texto de origen. Cada elemento se encarga de un fragmento del texto de origen:

* Una cadena literal o una expresión regular mueve el cursor coincidente la longitud de la coincidencia.
* En un análisis de regex, una expresión regular puede utilizar el operador de minimización '?' para pasar lo antes posible a la siguiente coincidencia.
* Un nombre de columna con un tipo analiza el texto como el tipo especificado. A menos que kind=relaxed, un análisis incorrecto invalida la coincidencia del patrón completo.
* Un nombre de columna sin tipo, o con el tipo 'string', copia el número mínimo de caracteres que se va a obtener en la siguiente coincidencia.
* ' * ' omite el número mínimo de caracteres que se va a obtener en la siguiente coincidencia. Puede utilizar ' *' al principio y al final del patrón, o bien después de un tipo que no sea una cadena, o entre las coincidencias de la cadena.

Todos los elementos de un patrón de análisis deben coincidir correctamente; de lo contrario, no se producirá ningún resultado. La excepción a esta regla es que cuando kind=relaxed, si no se puede analizar una variable con tipo, el resto del análisis continúa.

**Ejemplos**

*Simple*:

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | counter | present | Year
---|---|---|---
1 | 2 | socks | 63

*Relaxed*:

Cuando la entrada contiene una coincidencia correcta para cada columna con tipo, un análisis relajado produce los mismos resultados que un análisis simple. Pero si una de las columnas con tipo no se analiza correctamente, un análisis relajado continúa procesando el resto del patrón, mientras que un análisis simple se detiene y no genera ningún resultado.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x | present | Year
---|---|---
1 | socks | 63


*Regex*:

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\)"
| project-away x, s
```

resource | slice | lock | release | previous
---|---|---|---|---
Programador | 16 | 02/17/2016 08:41:00 | 02/17/2016 08:41 | 2016-02-17T08:40:00Z

### Operador project

    T | project cost=price*quantity, price

Seleccione las columnas que desea incluir, cambie el nombre o quite e inserte nuevas columnas calculadas. El orden de las columnas en el resultado se especifica con el orden de los argumentos. Solamente las columnas especificadas en los argumentos se incluyen en el resultado: cualquier otra de la entrada se quita. (Consulte también `extend`).


**Sintaxis**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T*: la tabla de entrada.
* *ColumnName*: el nombre de una columna que va a aparecer en la salida. Si no hay ningún valor de *Expression*, tiene que aparecer una columna con ese nombre en la entrada. Los [nombres](#names) distinguen mayúsculas de minúsculas y pueden contener caracteres alfabéticos, numéricos o de subrayado '\_'. Use `['...']` o `["..."]` para entrecomillar palabras clave o nombres con otros caracteres.
* *Expression*: expresión escalar opcional que hace referencia a las columnas de entrada. 

    Es posible devolver una nueva columna calculada con el mismo nombre que una columna existente en la entrada.

**Devoluciones**

Una tabla que tiene las columnas con nombres de argumentos y tantas filas como la tabla de entrada.

**Ejemplo**

El ejemplo siguiente muestra variantes de manipulaciones que se pueden hacer con el operador `project`. La tabla de entrada `T` tiene tres columnas de tipo `int`: `A`, `B` y `C`.

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### Operador project-away

    T | project-away column1, column2, ...

Excluye las columnas especificadas. El resultado contiene todas las columnas de entrada excepto las que se mencionen.

### Operador range

    range LastWeek from ago(7d) to now() step 1d

Genera una tabla de valores de una columna única. Tenga en cuenta que no tiene una entrada de canalización.

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**Sintaxis**

    range ColumnName from Start to Stop step Step

**Argumentos**

* *ColumnName*: el nombre de la única columna de la tabla de salida.
* *Start*: el valor más pequeño de la salida.
* *Stop*: el valor más alto que se genera en la salida (o un límite en el valor más alto, si el valor *step* sobrepasa este valor).
* *Step*: la diferencia entre dos valores consecutivos. 

Los argumentos tienen que ser valores numéricos, de fecha o intervalo de tiempo. No pueden tener como referencia las columnas de una tabla. (Si desea calcular el intervalo en función de una tabla de entrada, utilice la función [range*,*](#range), quizás con el [operador mvexpand](#mvexpand-operator)).

**Devoluciones**

Una tabla con una única columna denominada *ColumnName*, cuyos valores son *Start*, *Start* + *Step*, etc., hasta *Stop*, también incluido.

**Ejemplo**

```AIQL
range Steps from 1 to 8 step 3
```

Una tabla con una única columna denominada `Steps` cuyo tipo es `long` y cuyos valores son `1`, `4` y `7`.

**Ejemplo**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Una tabla de medianoche en los últimos siete días. La función bin (floor) se reduce cada vez hasta el inicio del día.

**Ejemplo**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Muestra cómo el operador `range` puede utilizarse para crear una pequeña tabla de dimensiones ad hoc que, después, se usa para incluir ceros donde los datos de origen no tienen valores.

### Operador reduce

    exceptions | reduce by outerMessage

Intenta agrupar registros similares. Para cada grupo, el operador envía el valor de `Pattern` que cree que mejor describe a ese grupo y el valor de `Count` de registros de ese grupo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintaxis**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName*: la columna que se va a examinar. Tiene que ser de tipo cadena.
* *Threshold*: un valor en el intervalo {0..1}. El valor predeterminado es 0.001. Para las entradas grandes, el valor threshold debe ser pequeño. 

**Devoluciones**

Dos columnas, `Pattern` y `Count`. En muchos casos, el valor Pattern será un valor completo de la columna. En algunos casos, puede identificar términos comunes y reemplazar los elementos variables con "*".

Por ejemplo, el resultado de `reduce by city` podría incluir:

|Patrón | Recuento |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| París | 27163 |


### Directiva render

    T | render [ table | timechart  | barchart | piechart ]

Render indica cómo mostrar la tabla de la capa de presentación. Debe ser el último elemento de la canalización. Es una alternativa cómoda a usar los controles de la pantalla, lo que le permite guardar una consulta con un método de presentación determinado.

### Cláusula restrict 

Especifica el conjunto de nombres de tabla disponibles para los operadores que siguen. Por ejemplo:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### Operador sort 

    T | sort by country asc, price desc

Ordena las filas de la tabla de entrada en una o más columnas.

**Alias** `order`

**Sintaxis**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T*: la entrada de la tabla que se va a ordenar.
* *Column*: columna de *T* por la que se ordena. El tipo de los valores tiene que ser numérico, fecha, hora o cadena.
* `asc`: ordena en orden ascendente, de bajo a alto. El valor predeterminado es `desc`, descendente de alto a bajo.

**Ejemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas las filas de la tabla Traces que tienen un determinado valor de `ActivityId` ordenadas por su marca de tiempo.

### Operador summarize

Crea una tabla que agrega el contenido de la tabla de entrada.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Una tabla que muestra el número, la duración media de las solicitudes y el conjunto de ciudades en cada país. Hay una fila en la salida de cada país específico. Las columnas de salida muestran el recuento, promedio de duración, ciudades y país. El resto de columnas de entrada se ignoran.


    T | summarize count() by price_range=bin(price, 10.0)

Una tabla que muestra cuántos elementos tienen precios en cada intervalo [0,10.0], [10.0,20.0], y así sucesivamente. Este ejemplo tiene una columna para el recuento y otra para el intervalo de precios. El resto de columnas de entrada se ignoran.


**Sintaxis**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Column*: nombre opcional para una columna de resultados. El valor predeterminado es un nombre derivado de la expresión. Los [nombres](#names) distinguen mayúsculas de minúsculas y pueden contener caracteres alfabéticos, numéricos o de subrayado '\_'. Use `['...']` o `["..."]` para entrecomillar palabras clave o nombres con otros caracteres.
* *Aggregation*: una llamada a una función de agregación como `count()` o `avg()`, con nombres de columna como argumentos. Consulte [Agregaciones](#aggregations).
* *GroupExpression*: una expresión sobre las columnas que proporciona un conjunto de valores distintivos. Normalmente, se trata de un nombre de columna que ya proporciona un conjunto limitado de valores o `bin()` con una columna numérica o temporal como argumento. 

Si proporciona una expresión numérica o temporal sin utilizar `bin()`, Analytics la aplica automáticamente con un intervalo de `1h` para horas o `1.0` para números.

Si no proporciona ningún elemento *GroupExpression*, toda la tabla se resume en una única fila de salida.



**Devoluciones**

Las filas de entrada están organizadas en grupos que tienen los mismos valores que las expresiones `by`. A continuación, las funciones de agregación especificadas se calculan sobre cada grupo, generando una fila para cada grupo. El resultado contiene las columnas `by` y también al menos una columna para cada agregación procesada. (Algunas funciones de agregación devuelven varias columnas.)

El resultado tiene tantas filas como el número de combinaciones distintivos de los valores `by`. Si desea resumir intervalos de valores numéricos, utilice `bin()` para reducir los intervalos a valores discretos.

**Nota:**

Aunque puede proporcionar expresiones arbitrarias tanto para las expresiones de agregación como para las de agrupación, resulta más eficaz usar nombres de columna simples, o bien aplicar `bin()` a una columna numérica.



### Operador take

Alias de [limit](#limit-operator)


### Operador top

    T | top 5 by Name desc

Devuelve los primeros *N* registros ordenados por las columnas especificadas.


**Sintaxis**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argumentos**

* *NumberOfRows*: el número de filas de *T* que se devuelve.
* *Sort\_expression*: una expresión por la que se ordenan las filas. Suele ser un nombre de columna. Puede especificar más de una sort\_expression.
* `asc` o `desc` (valor predeterminado) puede aparecer para controlar si la selección se hace realmente desde la parte "superior" o desde la parte "inferior" del intervalo.


**Sugerencias**

`top 5 by name` es superficialmente equivalente a `sort by name | take 5`. Sin embargo, se ejecuta más rápido y siempre devuelve resultados ordenados, mientras que `take` no ofrece ninguna garantía en este sentido.

### top-nested operator

    requests 
    | top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart 

Genera resultados jerárquicos, donde cada nivel es un desglose del nivel anterior. Es útil para responder a preguntas del tipo "¿Cuáles son las 5 solicitudes principales?", para cada una de ellas, "¿cuáles son los 3 depósitos de rendimiento más importantes" y, para cada uno de ellos, "¿cuáles son los 3 principales países de donde proceden dichas solicitudes?"

**Sintaxis**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**Argumentos**

* N:int: número de filas que se devolverá o pasará al siguiente nivel. En una consulta con tres niveles, donde N es 5, 3 y 3, el número total de filas será de 45.
* COLUMN: una columna para agruparla para la agregación. 
* AGGREGATION: una [función de agregación](#aggregations) que se aplicará a cada grupo de filas. Los resultados de estas agrupaciones determinarán los grupos principales que se mostrarán.


### Operador union

     Table1 | union Table2, Table3

Toma dos o más tablas y devuelve las filas de todas ellas.

**Sintaxis**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Table1*, *Table2*...
 *  El nombre de una tabla, como `requests`, o una tabla definida en una [cláusula let](#let-clause); o bien
 *  Una expresión de consulta, como `(requests | where success=="True")`.
 *  Un conjunto de tablas especificado con un carácter comodín. Por ejemplo, `e*` formaría la unión de todas las tablas definidas en las cláusulas let anteriores cuyo nombre comienza por "e", junto con la tabla "excepciones".
* `kind`: 
 * `inner`: el resultado tiene el subconjunto de columnas que son comunes a todas las tablas de entrada.
 * `outer`: el resultado tiene todas las columnas que aparecen en cualquiera de las entradas. Las celdas que no se han definido mediante una fila de entrada se establecen en `null`.
* `withsource=`*ColumnName*: si se especifica, el resultado incluirá una columna denominada *ColumnName*, cuyo valor indica qué tabla de origen ha aportado cada fila.

**Devoluciones**

Una tabla con tantas filas como haya en todas las tablas de entrada y tantas columnas como nombres únicos de columna existan en las entradas.

**Ejemplo**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Unión de todas las tablas cuyos nombres comienzan con "tt".


**Ejemplo**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
El número de usuarios distintivos que han creado un evento `exceptions` o un evento `traces` el día anterior. En el resultado, la columna "SourceTable" indicará "Query" o "Command".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versión más eficaz, produce el mismo resultado. Filtra cada tabla antes de crear la unión.

### Operador where

     T | where fruit=="apple"

Filtra una tabla para el subconjunto de filas que cumplen un predicado.

**Alias** `filter`

**Sintaxis**

    T | where Predicate

**Argumentos**

* *T*: la entrada tabular cuyos registros se van a filtrar.
* *Predicate*: una [expresión](#boolean) `boolean` sobre las columnas de *T*. Se evalúa para cada fila en *T*.

**Devoluciones**

Filas en *T* para las que *Predicate* es `true`.

**Sugerencias**

Para obtener el rendimiento más rápido:

* **Use comparaciones simples** entre los nombres de columna y las constantes. ("Constante" significa constante en la tabla, por lo que `now()` y `ago()` son correctos, y también lo son los valores escalares asignados mediante una [cláusula `let`](#let-clause)).

    Por ejemplo, se prefiere `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Coloque primero los términos más simples**: si tiene varias cláusulas unidas con `and`, coloque primero las cláusulas que afecten a una sola columna. Por tanto, es mejor usar `Timestamp > ago(1d) and OpId == EventId` que ordenarlo al revés.


**Ejemplo**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros con antigüedad no superior a 1 hora y que proceden desde el origen denominado "Kuskus" y tienen dos columnas del mismo valor.

Observe que colocamos la comparación entre dos columnas al final, ya que no puede utilizar el índice y exige un examen.



## Agregaciones

Las agregaciones son funciones utilizadas para combinar valores de los grupos creados en la [operación summarize](#summarize-operator). Por ejemplo, en esta consulta, dcount() es una función de agregación:

    requests | summarize dcount(name) by success

### cualquiera 

    any(Expression)

Selecciona una fila del grupo de forma aleatoria y devuelve el valor de la expresión especificada.

Esto es útil, por ejemplo, si alguna columna tiene un gran número de valores similares (por ejemplo, una columna de "texto de error") y desea tomar una muestra de esa columna por cada valor único de la clave de grupo compuesta.

**Ejemplo**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
### argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Busca una fila en el grupo que minimiza o maximiza *ExprToMaximize* y devuelve el valor de *ExprToReturn* (o `*` para devolver toda la fila).

**Sugerencia**: El nombre de las columnas por las que se pasa se cambia automáticamente. Para asegurarse de que está utilizando los nombres adecuados, revise los resultados mediante `take 5` antes de canalizarlos a otro operador.

**Ejemplos**

Para cada nombre de la solicitud, mostrar cuando se produjo la solicitud más larga:

    requests | summarize argmax(duration, timestamp) by name

Mostrar todos los detalles de la solicitud más larga, no solo la marca de tiempo:

    requests | summarize argmax(duration, *) by name


Buscar el valor más bajo de cada métrica, junto con su marca de tiempo y otros datos:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### avg

    avg(Expression)

Calcula el promedio de *Expression* en todo el grupo.

### buildschema

    buildschema(DynamicExpression)

Devuelve el esquema mínimo que admite todos los valores de *DynamicExpression*.

El tipo de columna del parámetro debe ser `dynamic`, una matriz o un contenedor de propiedades.

**Ejemplo**

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` se utiliza para marcar el punto en el que debe utilizar un índice numérico. En el caso de este esquema, algunas rutas válidas podrían las siguientes (suponiendo que estos índices de ejemplo se encuentren en el intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Ejemplo**

Suponga que la columna de entrada tiene tres valores dinámicos:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


El esquema resultante sería:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

El esquema indica que:

* El objeto raíz es un contenedor con cuatro propiedades denominadas x, y, z y t.
* La propiedad denominada "x" podría ser de tipo "int" o de tipo "string".
* La propiedad llamada "y" podría del tipo "double", u otro contenedor con una propiedad llamada "w" de tipo "string".
* La palabra clave ``indexer`` indica que "z" y "t" son matrices.
* Cada elemento de la matriz "z" es un entero o una cadena.
* "t" es una matriz de cadenas.
* Cada propiedad es implícitamente opcional, y cualquier matriz puede estar vacía.

##### Modelo de esquema

La sintaxis del esquema devuelto es:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Son equivalentes a un subconjunto de las anotaciones de tipo TypeScript, codificados como valor dinámico. En Typescript, el esquema de ejemplo sería:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### count

    count([ Predicate ])

Devuelve un número de filas para las que *Predicate* equivale a `true`. Si no hay ningún elemento *Predicate* especificado, se devuelve el número total de registros del grupo.

**Sugerencias de rendimiento**: Utilice `summarize count(filter)` en lugar de `where filter | summarize count()`.

> [AZURE.NOTE] Evite usar count() para buscar el número de solicitudes, excepciones u otros eventos que se hayan producido. Cuando está en funcionamiento el [muestreo](app-insights-sampling.md), el número de puntos de datos será menor que el número de eventos reales. En su lugar, use `summarize sum(itemCount)...`. La propiedad itemCount refleja el número de eventos originales que cada punto de datos retenido representa.

### countif

    countif(Predicate)

Devuelve un número de filas para las que *Predicate* equivale a `true`.

**Sugerencias de rendimiento**: Utilice `summarize countif(filter)` en lugar de `where filter | summarize count()`.

> [AZURE.NOTE] Evite usar countif() para buscar el número de solicitudes, excepciones u otros eventos que se hayan producido. Cuando está en funcionamiento el [muestreo](app-insights-sampling.md), el número de puntos de datos será menor que el número de eventos reales. En su lugar, use `summarize sum(itemCount)...`. La propiedad itemCount refleja el número de eventos originales que cada punto de datos retenido representa.

### dcount

    dcount( Expression [ ,  Accuracy ])

Devuelve una estimación del número de valores distintivos de *Expr* en el grupo. (Para mostrar los valores distintivos, utilice [`makeset`](#makeset)).

*Accuracy*, si se especifica, controla el equilibrio entre velocidad y precisión.

 * `0` = el cálculo menos preciso y más rápido.
 * `1` = el valor predeterminado, que equilibra la precisión y el tiempo de cálculo; error en torno al 0,8 %.
 * `2` = el cálculo más preciso y más lento; error en torno al 0,4 %.

**Ejemplo**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Devuelve una estimación del número de valores distintivos de *Expr* de las filas del grupo para el que el valor de *Predicate* es True. (Para mostrar los valores distintivos, utilice [`makeset`](#makeset)).

*Accuracy*, si se especifica, controla el equilibrio entre velocidad y precisión.

 * `0` = el cálculo menos preciso y más rápido.
 * `1` = el valor predeterminado, que equilibra la precisión y el tiempo de cálculo; error en torno al 0,8 %.
 * `2` = el cálculo más preciso y más lento; error en torno al 0,4 %.

**Ejemplo**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### makelist

    makelist(Expr [ ,  MaxListSize ] )

Devuelve una matriz `dynamic` (JSON) de todos los valores de *Expr* del grupo.

* *MaxListSize* es un límite de entero opcional para el número máximo de elementos devueltos (el valor predeterminado es *128*).

### makeset

    makeset(Expression [ , MaxSetSize ] )

Devuelve una matriz `dynamic` (JSON) del conjunto de valores distintivos que *Expr* toma en el grupo. (Sugerencia: Para contar solo los valores distintivos, utilice [`dcount`](#dcount)).
  
*  *MaxSetSize* es un límite de entero opcional para el número máximo de elementos devueltos (el valor predeterminado es *128*).

**Ejemplo**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Consulte también el [operador `mvexpand`](#mvexpand-operator) para la función opuesta.


### max, min

    max(Expr)

Calcula el máximo de *Expr*.
    
    min(Expr)

Calcula el mínimo de *Expr*.

**Sugerencia**: Esto ofrece el valor mínimo o máximo por sí mismo; por ejemplo, el precio más alto o más bajo. Sin embargo, si desea tener otras columnas de la fila (por ejemplo, el nombre del proveedor con el precio más bajo), use [argmin o argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
### percentile, percentiles

    percentile(Expression, Percentile)

Devuelve una estimación para *Expression* de los percentiles especificados en el grupo. La precisión depende de la densidad de población en la región del percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Es igual que `percentile()`, pero calcula un número de valores de percentil (más rápido que calcular cada percentil de forma individual).

**Ejemplos**


El valor de `duration` que es mayor que el 95 % del conjunto de la muestra y menor que el 5 % del conjunto de la muestra, calculado para cada nombre de la solicitud:

    request 
    | summarize percentile(duration, 95)
      by name

Omita "by..." para calcular toda la tabla.

Calcular simultáneamente varios percentiles para diferentes nombres de solicitud:

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Los resultados muestran que para la solicitud /Events/Index, el 5 % de las solicitudes se han respondido en menos de 2,44 s, la mitad en 3,52 s, y el 5 % en más de 6,85 s.


Calcular varias estadísticas:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

##### Error de estimación en percentiles

El agregado de percentiles proporciona un valor aproximado mediante [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Algunos puntos importantes:

* Los límites en el error de estimación varían con el valor del percentil solicitado. Los valores más precisos se encuentran en los extremos de la escala [0...100]; los percentiles 0 y 100 son los valores mínimo y máximo exactos de la distribución. La precisión se reduce gradualmente hacia el centro de la escala. Es peor en la media, y está limitada al 1 %. 
* Los límites de los errores se observan en el rango, no en el valor. Supongamos que el percentil (X, 50) devuelve el valor de Xm. La estimación garantiza que al menos el 49 % y a lo sumo el 51% de los valores de X son inferiores a Xm. No hay ningún límite teórico sobre la diferencia entre Xm y el valor medio real de X.

### stdev

     stdev(Expr)

Devuelve la desviación estándar de *Expr* en el grupo.

### variance

    variance(Expr)

Devuelve la varianza de *Expr* en el grupo.

### sum

    sum(Expr)

Devuelve la suma de *Expr* en el grupo.


## Escalares

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Los tipos admitidos son:

| Tipo | Nombres adicionales | Tipo equivalente de .NET |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Conversiones de tipos

Puede convertir un tipo a otro. En general, si la conversión tiene sentido, funcionará:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Compruebe si se puede convertir una cadena a un tipo específico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### Comparaciones escalares

||
---|---
`<` |Menor que
`<=`|Menor que o Igual a
`>` |Mayor que
`>=`|Mayor que o Igual a
`<>`|Not Equals
`!=`|Not Equals 
`in`| El operando derecho es una matriz (dinámica) y el operando izquierdo es igual a uno de sus elementos.
`!in`| El operando derecho es una matriz (dinámica) y el operando izquierdo no es igual a ninguno de sus elementos.




### gettype

**Devoluciones**

Una cadena que representa el tipo de almacenamiento subyacente de su único argumento. Esto es especialmente útil cuando tiene valores de tipo `dynamic`: en este caso, `gettype()` mostrará cómo se codifica un valor.

**Ejemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### hash

**Sintaxis**

    hash(source [, mod])

**Argumentos**

* *source*: el escalar de origen sobre el que se calcula el valor hash.
* *mod*: el valor de módulo que se aplicará al resultado de hash.

**Devoluciones**

El valor de xxhash (long) del escalar dado, aplicando la operación módulo según el valor mod determinado (si se especifica).

**Ejemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

La función `iff()` evalúa el primer argumento (el predicado) y devuelve el valor del argumento segundo o tercero, en función de si el predicado es `true` o `false`. El segundo y tercer argumento deben ser del mismo tipo.

**Sintaxis**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicate*: una expresión que se corresponde con un valor `boolean`.
* *ifTrue*: una expresión que se evalúa y cuyo valor se devuelve desde la función si *predicate* se evalúa como `true`.
* *ifFalse*: una expresión que se evalúa y cuyo valor se devuelve desde la función si *predicate* se evalúa como `false`.

**Devoluciones**

Esta función devuelve el valor de *ifTrue* si *predicate* se evalúa como `true`, o bien el valor de *ifFalse* en caso contrario.

**Ejemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Toma un único argumento e indica si es null.

**Sintaxis**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Devoluciones**

Verdadero o falso dependiendo de si el valor es null o not null.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**Ejemplo**

    T | where isnotnull(PossiblyNull) | count

Tenga en cuenta que hay otras formas de conseguir este efecto:

    T | summarize count(PossiblyNull)

### toscalar

Evalúa una consulta o una expresión y devuelve el resultado como valor único. Esta función es útil para realizar cálculos de ensayo; por ejemplo, para determinar el número total de eventos y, luego, usarlo como línea base.

**Sintaxis**

    toscalar(query)
    toscalar(scalar)

**Devoluciones**

El argumento evaluado. Si el argumento es una tabla, se devuelve la primera columna de la primera fila. (Se recomienda organizar que el argumento solo tenga una columna y una fila).

**Ejemplo**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```



## Booleano 

### Literales booleanos

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Operadores booleanos

	and 
    or 

    

## Números

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Literales numéricos

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Operadores aritméticos

|| |
|---|-------------|
| + | Agregar |
| - | Restar |
| * | Multiplicar |
| / | Dividir |
| % | Aplicar módulo |
||
|`<` |Menor que
|`<=`|Menor que o Igual a
|`>`|Mayor que
|`>=`|Mayor que o Igual a
|`<>`|No igual a
|`!=`|No igual a


### abs

**Sintaxis**

	abs(x)

**Argumentos**

* x: un valor entero, real o de intervalo de tiempo.

**Devoluciones**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### bin, floor

Redondea los valores hacia abajo hasta un entero múltiplo del tamaño de un intervalo determinado. Se utiliza mucho en la consulta [`summarize by`](#summarize-operator). Si tiene un conjunto de valores dispersos, se agruparán en un conjunto más pequeño de valores específicos.

Alias `floor`.

**Sintaxis**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumentos**

* *value*: un número, una fecha o un intervalo de tiempo. 
* *roundTo*: el tamaño del intervalo. Un número, una fecha o un intervalo de tiempo que divide *value*. 

**Devoluciones**

El múltiplo más cercano de *roundTo* por debajo de *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**Ejemplos**

Expresión | Resultado
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


La siguiente expresión calcula un histograma de duraciones, con un tamaño de depósito de 1 segundo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### exp

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v



### floor

Un alias para [`bin()`](#bin).


### log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` debe ser un número real > 0. De lo contrario, se devuelve null.

### rand

Un generador de números aleatorios.

* `rand()`: un número real entre 0,0 y 1,0.
* `rand(n)`: un número entero comprendido entre 0 y n-1.




### sqrt

La función de raíz cuadrada.

**Sintaxis**

    sqrt(x)

**Argumentos**

* *x*: un número real >= 0.

**Devoluciones**

* Un número positivo, como `sqrt(x) * sqrt(x) == x`.
* `null` si el argumento es negativo o no se puede convertir a un valor `real`. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Fecha y hora


[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### Literales de fecha y hora

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Las horas se expresan siempre en formato UTC. La omisión de la fecha genera una hora del día actual.
`now()`|La hora actual.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 días
`1.5h`|1,5 horas 
`30m`|30 minutos
`10s`|10 segundos
`0.1s`|0,1 segundos
`100ms`| 100 milisegundos
`10microsecond`|
`1tick`|100 ns
`time("15 seconds")`|
`time("2")`| 2 días
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Expresiones de fecha y hora

Expresión |Resultado
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Menor que
`<=`|Menor que o Igual a
`>` |Mayor que
`>=`|Mayor que o Igual a
`<>`|Not Equals
`!=`|Not Equals 




### ago

Resta un intervalo de tiempo especificado a la hora UTC actual. Al igual que `now()`, esta función se puede utilizar varias veces en una instrucción, y la hora UTC a la que se hace referencia será la misma para todas las instancias.

**Sintaxis**

    ago(a_timespan)

**Argumentos**

* *a\_timespan*: intervalo para restar a la hora UTC actual (`now()`).

**Devoluciones**

    now() - a_timespan

**Ejemplo**

Todas las filas con una marca de tiempo de la última hora:

```AIQL

    T | where timestamp > ago(1h)
```

### datepart

    datepart("Day", datetime(2015-12-14)) == 14

Extrae un elemento especificado de una fecha como un entero.

**Sintaxis**

    datepart(part, datetime)

**Argumentos**

* `part:String` - {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**Devoluciones**

Valor Long que representa el elemento especificado.


### dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

El número ordinal del día del mes.

**Sintaxis**

    dayofmonth(a_date)

**Argumentos**

* `a_date`: un valor `datetime`.


### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Número entero de días transcurridos desde el domingo anterior, como `timespan`.

**Sintaxis**

    dayofweek(a_date)

**Argumentos**

* `a_date`: un valor `datetime`.

**Devoluciones**

El valor `timespan` desde medianoche al principio del domingo anterior, redondeado a un número entero de días.

**Ejemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### dayofyear

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

El número ordinal del día del año.

**Sintaxis**

    dayofyear(a_date)

**Argumentos**

* `a_date`: un valor `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### getmonth

Obtenga el número del mes (1-12) de un valor de fecha y hora.

**Ejemplo**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Obtenga el año de un valor de fecha y hora.

**Ejemplo**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

La hora UTC actual, con una diferencia opcional determinada por un intervalo de tiempo especificado. Esta función puede utilizarse varias veces en una instrucción, y la hora UTC a la que se hace referencia será la misma para todas las instancias.

**Sintaxis**

    now([offset])

**Argumentos**

* *offset:* un valor `timespan`, agregado a la hora UTC actual. Valor predeterminado: 0.

**Devoluciones**

La hora UTC actual como `datetime`.

    now() + offset

**Ejemplo**

Determina el intervalo desde el evento identificado por el predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Comprueba si una cadena es una fecha válida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### WeekOfYear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

El resultado entero representa el número de semana mediante el estándar ISO 8601. El primer día de la semana es el domingo y la primera semana del año es la semana que contiene el primer jueves de ese año (los últimos días de un año, por tanto, pueden contener algunos de los días de la semana 1 del año siguiente, o bien los días de la primera parte de la semana 52 o 53 del año anterior).


## Cadena

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Literales de cadena

Las reglas son las mismas que en JavaScript.

Las cadenas pueden incluirse entre comillas sencillas o dobles.

La barra diagonal inversa (``) se utiliza para los caracteres de escape, como `\t` (tabulación), `\n` (nueva línea) y las instancias del carácter de comillas.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Literales de cadena ofuscados

Los literales de cadena ofuscados son cadenas que Analytics ocultará al generar la cadena (por ejemplo, al realizar el seguimiento). El proceso de ofuscación reemplaza todos los caracteres ofuscados por un carácter inicial (`*`).

Para formar un literal de cadena ofuscado, anteponga `h` o "H". Por ejemplo:

```
h'hello'
h@'world' 
h"hello"
```

### Comparaciones de cadenas

Operador|Descripción|Distingue mayúsculas de minúsculas|Ejemplo real
---|---|---|---
`==`|Equals |Sí| `"aBc" == "aBc"`
`<>`|No es igual a|Sí| `"abc" <> "ABC"`
`=~`|Equals |No| `"abc" =~ "ABC"`
`!~`|No es igual a |No| `"aBc" !~ "xyz"`
`has`|El lado derecho (RHS) es un término completo en el lado izquierdo (LHS)|No| `"North America" has "america"`
`!has`|RHS no es un término completo en LHS|No|`"North America" !has "amer"` 
`contains` | RHS ocurre como una subsecuencia de LHS|No| `"FabriKam" contains "BRik"`
`!contains`| RHS no ocurre en LHS|No| `"Fabrikam" !contains "xyz"`
`containscs` | RHS ocurre como una subsecuencia de LHS|Sí| `"FabriKam" contains "Kam"`
`!containscs`| RHS no ocurre en LHS|Sí| `"Fabrikam" !contains "Kam"`
`startswith`|RHS es una subsecuencia inicial de LHS|No|`"Fabrikam" startswith "fab"`
`matches regex`|LHS contiene una coincidencia para RHS|Sí| `"Fabrikam" matches regex "b.*k"`


Use `has` o `in` si está probando la presencia de un término léxico completo; es decir, un símbolo o una palabra alfanumérica delimitada por caracteres no alfanuméricos o el inicio o el final del campo. `has` se ejecuta con mayor rapidez que `contains` o `startswith`. La primera de estas consultas se ejecuta más rápido:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Cuenta las apariciones de una subcadena en una cadena. Las coincidencias de cadena sin formato se pueden superponer; las coincidencias de expresión regular no lo hacen.

**Sintaxis**

    countof(text, search [, kind])

**Argumentos**

* *text*: una cadena.
* *search*: la cadena sin formato o la expresión regular que coincide con *text*.
* *kind*: `"normal"|"regex"` el valor predeterminado es `normal`. 

**Devoluciones**

El número de veces que la cadena de búsqueda puede coincidir en el contenedor. Las coincidencias de cadena sin formato se pueden superponer; las coincidencias de expresión regular no lo hacen.

**Ejemplos**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (¡no 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtenga una coincidencia para una [expresión regular](#regular-expressions) desde una cadena de texto. Opcionalmente, convierte a continuación la subcadena extraída al tipo indicado.

**Sintaxis**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex*: una [expresión regular](#regular-expressions).
* *captureGroup*: una constante `int` positiva que indica el grupo de capturas que se va a extraer. 0 significa toda la coincidencia; 1, el valor que coincide con el primer elemento "("paréntesis")" de la expresión regular; 2 o más, los posteriores paréntesis.
* *text*: un valor de `string` que se buscará.
* *typeLiteral*: un literal de tipo opcional (por ejemplo, `typeof(long)`). Si se proporciona, la subcadena extraída se convierte a este tipo. 

**Devoluciones**

Si *regex* encuentra una coincidencia en *text*, la subcadena que coincide con el *captureGroup* del grupo de capturas indicado, opcionalmente convertido a *typeLiteral*.

Si no hay ninguna coincidencia, o se produce un error en la conversión del tipo: `null`.

**Ejemplos**

En la cadena de ejemplo `Trace` se busca una definición para `Duration`. La coincidencia se convierte en `real` y se multiplica por una constante de tiempo (`1s`) para que `Duration` sea de tipo `timespan`. En este ejemplo, es igual a 123,45 segundos:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

En este ejemplo es equivalente a `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True si el argumento es una cadena vacía o es null. Consulte también [isnull](#isnull).


**Sintaxis**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Devoluciones**

Indica si el argumento es una cadena vacía o isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**Ejemplo**


    T | where isempty(fieldName) | count




### replace

Reemplace todas las coincidencias de expresiones regulares por otra cadena.

**Sintaxis**

    replace(regex, rewrite, text)

**Argumentos**

* *regex*: la [expresión regular](https://github.com/google/re2/wiki/Syntax) para buscar *text*. Puede contener grupos de captura entre "("paréntesis")". 
* *rewrite*: la expresión regular de reemplazo para cualquier coincidencia que encuentre *matchingRegex*. Use `\0` para hacer referencia a toda la coincidencia, `\1` para el primer grupo de capturas, `\2` y valores sucesivos para los grupos de capturas posteriores.
* *text*: una cadena.

**Devoluciones**

*text* después de reemplazar todas las coincidencias de *regex* con evaluaciones de *rewrite*. Las coincidencias no se superponen.

**Ejemplo**

Esta instrucción:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Tiene los resultados siguientes:

| x | str | reemplazado|
|---|---|---|
| 1 | El número es 1.000000 | El número era 1.000000|
| 2 | El número es 2.000000 | El número era 2.000000|
| 3 | El número es 3.000000 | El número era 3.000000|
| 4 | El número es 4.000000 | El número era 4.000000|
| 5 | El número es 5.000000 | El número era 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide una cadena determinada según un delimitador especificado y devuelve una matriz de cadenas con las subcadenas contenidas. Opcionalmente, se puede devolver una subcadena específica si existe.

**Sintaxis**

    split(source, delimiter [, requestedIndex])

**Argumentos**

* *source*: la cadena de origen que se va a dividir según el delimitador especificado.
* *delimiter*: el delimitador que se utilizará para dividir la cadena de origen.
* *requestedIndex*: un índice de base cero opcional `int`. Si se proporciona, la matriz de cadenas devuelta contendrá la subcadena solicitada, si existe. 

**Devoluciones**

Una matriz de cadenas que contiene las subcadenas de la cadena de origen determinada delimitadas por el delimitador especificado.

**Ejemplos**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

Concatena entre 1 y 16 argumentos, que deben ser cadenas.

### strlen

    strlen("hello") == 5

Longitud de una cadena.

### substring

    substring("abcdefg", 1, 2) == "bc"

Extraiga una subcadena de una cadena de origen determinada a partir de un índice determinado. Opcionalmente, se puede especificar la longitud de la subcadena solicitada.

**Sintaxis**

    substring(source, startingIndex [, length])

**Argumentos**

* *source*: la cadena de origen de la que se tomará la subcadena.
* *startingIndex*: la posición del carácter inicial basado en cero de la subcadena solicitada.
* *length*: un parámetro opcional que puede utilizarse para especificar el número de caracteres solicitado de la subcadena. 

**Devoluciones**

Una subcadena de la cadena especificada. La subcadena comienza en la posición del carácter startingIndex (basado en cero) y continúa hasta el final de los caracteres de cadena o longitud, si se especifican.

**Ejemplos**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Convierte una cadena a minúsculas.

### toupper

    toupper("hello") == "HELLO"

Convierte una cadena a mayúsculas.



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## Matrices, objetos y dinámica

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Este es el resultado de una consulta en una excepción de Application Insights. El valor de `details` es una matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indexing**: matrices y objetos de índice, al igual que en JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Sin embargo, use `arraylength` y otras funciones de Analytics (no ".length").

**Casting**: en algunos casos es necesario convertir un elemento que se extrae de un objeto, ya que puede variar su tipo. Por ejemplo, `summarize...to` necesita un tipo específico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literals**: para crear un objeto explícito de matriz o contenedor de propiedades, escríbalo como una cadena JSON y conviértalo:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand**: para separar las propiedades de un objeto en filas independientes, use mvexpand:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath**: para buscar todas las rutas en un objeto complejo:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema**: para buscar el esquema mínimo que admite todos los valores de la expresión en la tabla:

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Observe que `indexer` se utiliza para marcar el punto en el que debe utilizar un índice numérico. En el caso de este esquema, algunas rutas válidas podrían las siguientes (suponiendo que estos índices de ejemplo se encuentren en el intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### Literales de matriz y objeto

Para crear un literal dinámico, utilice `parsejson` (alias `todynamic`) con un argumento de cadena de JSON:

* `parsejson('[43, 21, 65]')`: una matriz de números.
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`: un valor único de tipo dinámico que contiene un número.
* `parsejson('"21"')`: un valor único de tipo dinámico que contiene una cadena.

Tenga en cuenta que, a diferencia de JavaScript, JSON exige el uso de comillas dobles (`"`) en torno a las cadenas. Por lo tanto, generalmente es más fácil citar literales de cadena codificados en JSON con comillas simples (`'`).

En este ejemplo se crea un valor dinámico y, a continuación, se utilizan sus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


## Funciones del objeto dinámico

|||
|---|---|
| *value* `in` *array*| True si hay un elemento de *array* == *value*<br/>`where City in ('London', 'Paris', 'Rome')`.
| *value* `!in` *array*| True si no hay ningún elemento de *array* == *value*.
|[`arraylength(`array`)`](#arraylength)| Es null si no es una matriz
|[`extractjson(`path,object`)`](#extractjson)|Usa una ruta para navegar al objeto.
|[`parsejson(`source`)`](#parsejson)| Convierte una cadena JSON en un objeto dinámico.
|[`range(`from,to,step`)`](#range)| Una matriz de valores
|[`mvexpand` listColumn](#mvexpand-operator) | Replica una fila para cada valor de una lista en una celda especificada.
|[`summarize buildschema(`column`)`](#buildschema) |Deduce el esquema de tipo del contenido de la columna
|[`summarize makelist(`column`)` ](#makelist)| Acopla grupos de filas y coloca los valores de la columna en una matriz.
|[`summarize makeset(`column`)`](#makeset) | Acopla grupos de filas y coloca los valores de la columna en una matriz, sin duplicación.

### Objetos dinámicos en cláusulas let


Las [cláusulas let](#let-clause) almacenan valores dinámicos como cadenas, por lo que estas dos cláusulas son equivalentes, y ambas requieren `parsejson` (o `todynamic`) para poder usarse:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

El número de elementos de una matriz dinámica.

**Sintaxis**

    arraylength(array)

**Argumentos**

* *array:* un valor de `dynamic`.

**Devoluciones**

El número de elementos en *array*, o `null` si *array* no es una matriz.

**Ejemplos**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtenga un elemento especificado fuera de un texto JSON mediante una expresión de ruta. Opcionalmente, convierta la cadena extraída en un tipo específico.


**Sintaxis**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Devoluciones**

Esta función realiza una consulta de JsonPath en dataSource que contiene una cadena JSON válida, opcionalmente convirtiendo ese valor en otro tipo de acuerdo con el tercer argumento.



**Ejemplo**

La notación [entre corchetes] y la notación de puntos son equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Consejos de rendimiento**

* Aplique cláusulas where antes de usar `extractjson()`.
* Considere el uso de una coincidencia de expresión regular con [extract](#extract) en su lugar. Esto puede ejecutarse mucho más rápido, y es efectivo si JSON se genera a partir de una plantilla.
* Use `parsejson()` si necesita extraer más de un valor de JSON.
* Considere la posibilidad de analizar el JSON en ingesta declarando que el tipo de columna sea dinámico.

### Expresiones de ruta de JSON

|||
|---|---|
|`$`|Objeto raíz|
|`@`|Objeto actual|
|`[0]`|Subíndice de matriz|
|`.` o `[0]` | Elemento secundario|

*(Actualmente no implementamos comodines, recurrencia, unión o segmentos).*




### parsejson

Interpreta `string` como un [valor JSON](http://json.org/) y devuelve el valor como `dynamic`. Es superior al uso de `extractjson()` cuando necesita extraer más de un elemento de un objeto compuesto de JSON.

**Sintaxis**

    parsejson(json)

**Argumentos**

* *json*: un documento JSON.

**Devoluciones**

Un objeto de tipo `dynamic` especificado por *json*.

**Ejemplo**

En el ejemplo siguiente, cuando `context_custom_metrics` es un elemento `string` similar a este:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Después, el siguiente fragmento recupera el valor de la ranura `duration` del objeto, y desde ahí recupera dos ranuras, `duration.value` y `duration.min` (`118.0` y `110.0`, respectivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

La función `range()` (que no se debe confundir con el operador `range`) genera una matriz dinámica que contiene una serie de valores espaciados igualmente.

**Sintaxis**

    range(start, stop, step)

**Argumentos**

* *start*: el valor del primer elemento de la matriz resultante. 
* *stop*: el valor del último elemento de la matriz resultante, o el valor mínimo que sea mayor que el último elemento de la matriz resultante y dentro de un entero múltiplo de *step* desde *start*.
* *step*: la diferencia entre dos elementos consecutivos de la matriz.

**Ejemplos**

El siguiente ejemplo devuelve `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

El ejemplo siguiente devuelve una matriz que contiene todos los días del año 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convierte una cadena en un valor dinámico.

### treepath

    treepath(dynamic_object)

Enumera todas las expresiones de ruta que identifican hojas en un objeto dinámico.

**Devoluciones**

Una matriz de expresiones de ruta.

**Ejemplos**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Observe que "[0]" indica la presencia de una matriz, pero no especifica el índice utilizado por una ruta específica.

## Nombres

Los nombres pueden contener hasta 1024 caracteres. Se distinguen mayúsculas de minúsculas y pueden contener letras, dígitos y caracteres de subrayado (`_`).

Entrecomille un nombre con ['... '] o [" ... "] para incluir otros caracteres o usar una palabra clave como nombre. Por ejemplo:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|['ruta\\archivo\\n' x''] | Se utiliza \\ como carácter de escape.|
|["d-e.=/f#\\n"] | |
|[@'ruta\\archivo'] | No se utilizan caracteres de escape: \\ es literal.|
|[@"\\de vez en cuando"] | |
|[where] | Se utiliza una palabra clave del lenguaje como nombre.|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0601_2016-->