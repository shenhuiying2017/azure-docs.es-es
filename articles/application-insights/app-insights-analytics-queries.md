<properties 
	pageTitle="Operadores y consultas de la herramienta Analytics de Application Insights" 
	description="Referencia para los operadores que se usan con el fin de realizar consultas en Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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

# Funciones de consultas de Analytics


[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.


[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

Una consulta sobre datos de telemetría se compone de una referencia a una secuencia de origen, seguida de una canalización de filtros. Por ejemplo:


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
Cada filtro precedido por el carácter de barra vertical `|` es una instancia de un *operador*, con algunos parámetros. La entrada del operador es la tabla que resulta de la canalización anterior. En la mayoría de los casos, los parámetros son [expresiones escalares](app-insights-analytics-scalars.md) sobre las columnas de entrada. En algunos casos, los parámetros son los nombres de las columnas de entrada y, en otros casos, el parámetro es una segunda tabla. El resultado de una consulta siempre es una tabla, incluso si solo tiene una columna y una fila.

Una consulta puede ir precedida de una o más [cláusulas let](#let-clause) que definen valores escalares, tablas o funciones que pueden utilizarse dentro de la consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` se utiliza en los siguientes ejemplos de consultas para indicar la tabla de origen o canalización anterior.

## Operador count

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



## Operador extend

     T | extend duration = stopTime - startTime

Anexa una o más columnas calculadas a una tabla.


**Sintaxis**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* la tabla de entrada.
* *ColumnName:* el nombre de una columna a agregar. 
* *Expression:* un cálculo sobre las columnas existentes.

**Devoluciones**

Una copia de la tabla de entrada con las columnas adicionales especificadas.

**Sugerencias**

* Utilice como alternativa [`project`](#project-operator), si también desea quitar o cambiar el nombre de algunas columnas.
* No use `extend` simplemente para obtener un nombre más corto para usar en una expresión larga. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Las columnas originales de la tabla se han indexado; el nuevo nombre define una columna adicional que no está indexada, por lo que posiblemente la consulta se ejecute con lentitud

**Ejemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## Operador join

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

* `Kind` sin especificar

    Solo una fila del lado izquierdo se hace corresponder para cada valor de la clave `on`. La salida contiene una fila por cada coincidencia de esta fila con filas de la derecha.

* `Kind=inner`
 
     Hay una fila en la salida por cada combinación de filas coincidentes de la izquierda y la derecha.

* `kind=leftouter` (o `kind=rightouter` o `kind=fullouter`)

     Además de las coincidencias internas, hay una fila por cada fila de la izquierda (o derecha), incluso si no tiene ninguna coincidencia. En este caso, las celdas de salida sin coincidencias contienen valores NULL.

* `kind=leftanti`

     Devuelve todos los registros del lado izquierdo que no tienen coincidencias en el derecho. La tabla resultante solo tiene las columnas del lado izquierdo.
 
Si hay varias filas con los mismos valores para esos campos, obtendrá filas para todas las combinaciones.

**Sugerencias**

Para obtener el mejor rendimiento:

* Use `where` y `project` para reducir el número de filas y columnas en las tablas de entrada antes del `join`. 
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


## Cláusula let

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

Una cláusula let enlaza un nombre a un resultado tabular, un valor escalar o una función. La cláusula es un prefijo a una consulta y el ámbito del enlace es esa consulta. (La cláusula let no proporciona una forma para dar nombre a elementos que se utilizan más adelante en la sesión.)

**Sintaxis**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-insights-analytics-scalars.md#dynamic-type)
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

## Operador limit

     T | limit 5

Vuelve al número especificado de filas de la tabla de entrada. No hay ninguna garantía de cuáles son los registros que se devuelven. (Para devolver registros específicos, use [`top`](#top-operator).)

**Alias** `take`

**Sintaxis**

    T | limit NumberOfRows


**Sugerencias**

`Take` es una manera sencilla y eficaz de ver una muestra de los resultados cuando se está trabajando de forma interactiva. Tenga en cuenta que no hay garantía de que se generen ningunas filas específicas o de que lo hagan en un orden determinado.

Hay un límite implícito en el número de filas devueltas al cliente, incluso si no utiliza `take`. Para eliminar este límite, use la opción de solicitud de cliente `notruncation`.



## Operador mvexpand

    T | mvexpand listColumn 

Expande una lista de una celda de tipo dinámico (JSON) para que cada entrada tenga una fila independiente. Todas las demás celdas de una fila expandida se duplican.

(Vea también [`summarize makelist`](#summarize-operator) que realiza la función opuesta).

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
* *Nombre:* un nombre para la nueva columna.
* *Typename:* convierte la expresión expandida en un tipo determinado.
* *RowLimit:* el número máximo de filas generadas a partir de cada fila original. El valor predeterminado es 128.

**Devoluciones**

Varias filas para cada uno de los valores de una matriz en la columna indicada o en la expresión de matriz.

La columna expandida siempre tiene el tipo dinámico. Use una conversión como `todatetime()` o `toint()` si desea calcular o agregar valores.

Se admiten dos modos de expansiones de contenedor de propiedades:

* `bagexpansion=bag`: los contenedores de propiedades se expanden en contenedores de propiedades de entrada única. Esta es la expansión predeterminada.
* `bagexpansion=array`: los contenedores de propiedades se expanden en estructuras de matriz de dos elementos `[`* key*`,`*value*`]`, lo que permite el acceso uniforme a claves y valores (así como, por ejemplo, ejecutar una agregación de recuento distintiva sobre los nombres de propiedad). 

**Ejemplos**


    exceptions | take 1 
    | mvexpand details[0]

Divide un registro de excepciones en filas para cada elemento en el campo de detalles.



## Operador parse

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

Extrae los valores de una cadena. Puede usar la coincidencia de expresión regular o simple.

Los elementos de la cláusula `with` se hacen coincidir a su vez con la cadena de origen. Cada elemento arranca un fragmento del texto de origen. Si es una cadena sin formato, el cursor coincidente se mueve hasta la coincidencia. Si es una columna con un nombre de tipo, el cursor se mueve lo suficiente para analizar el tipo especificado. (Las coincidencias de cadena se mueven hasta que se encuentra una coincidencia con el siguiente elemento.) Si es un regex, se hace corresponder la expresión regular (y la columna resultante siempre tiene el tipo de cadena).

**Sintaxis**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**Argumentos**

* *T:* la tabla de entrada.
* *kind:* simple o regex. El valor predeterminado es simple.
* *StringExpression:* una expresión que se evalúa como una cadena o que se puede convertir en una cadena.
* *SimpleMatch:* una cadena que coincide con la siguiente parte del texto.
* *Column:* especifica la nueva columna para asignarle una coincidencia.
* *Type:* especifica cómo analizar la siguiente parte de la cadena de origen.
* *Regex:* una expresión regular para que coincida con la siguiente parte de la cadena. 

**Devoluciones**

La tabla de entrada ampliada según la lista de columnas.


**Ejemplos**

El operador `parse` ofrece una manera simplificada de `extend` una tabla usando varias aplicaciones `extract` en la misma expresión `string`. Esto resulta especialmente útil cuando la tabla tiene una columna `string` que contiene varios valores que quiere dividir en columnas individuales, como una columna que se ha generado con una instrucción ("`printf`"/"`Console.WriteLine`") de seguimiento de desarrollador.

En el ejemplo siguiente, supongamos que la columna `EventNarrative` de la tabla `StormEvents` contiene cadenas del tipo `{0} at {1} crested at {2} feet around {3} on {4} {5}`. La siguiente operación ampliará la tabla con dos columnas: `SwathSize` y `FellLocation`.


|EventNarrative|
|---|
|The Green River at Brownsville crested at 18.8 feet around 0930EST on December 12. Flood stage at Brownsville is 18 feet. Minor flooding occurs at this level. The river overflows lock walls and some of the lower banks, along with some agricultural bottom land.|
|The Rolling Fork River at Boston crested at 39.3 feet around 1700EST on December 12. Flood stage at Boston is 35 feet. Minor flooding occurs at this level, with some agricultural bottom land covered.|
|The Green River at Woodbury crested at 36.7 feet around 0600EST on December 16. Flood stage at Woodbury is 33 feet. Minor flooding occurs at this level, with some lowlands around the town of Woodbury covered with water.|
|The Ohio River at Tell City crested at 39.0 feet around 7 AM EST on December 18. Flood stage at Tell City is 38 feet. At this level, the river begins to overflow its banks above the gage. Indiana Highway 66 floods between Rome and Derby.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|Ubicación|Alto|Hora|Mes|Día|
|---|---|---|---|---|---|
|The Green River | Woodbury |36\.7| 0600EST | December|16|
|The Rolling Fork River | Boston |39\.3| 1700EST | December|12|
|The Green River | Brownsville |18\.8| 0930EST | December|12|
|The Ohio River | Tell City |39| 7 AM EST | December|18|

También es posible realizar coincidencias usando expresiones regulares. Esto produce el mismo resultado, pero todas las columnas de resultados tienen el tipo de cadena:

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## Operador project

    T | project cost=price*quantity, price

Seleccione las columnas que desea incluir, cambie el nombre o quite e inserte nuevas columnas calculadas. El orden de las columnas en el resultado se especifica con el orden de los argumentos. Solamente las columnas especificadas en los argumentos se incluyen en el resultado: cualquier otra de la entrada se quita. (Vea también `extend`).


**Sintaxis**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* la tabla de entrada.
* *ColumnName:* el nombre de una columna que va a aparecer en la salida. Si no hay ningún valor *Expression*, tiene que aparecer en la entrada una columna con ese nombre. 
* *Expression:* expresión escalar opcional que hace referencia a las columnas de entrada. 

    Es posible devolver una nueva columna calculada con el mismo nombre que una columna existente en la entrada.

**Devoluciones**

Una tabla que tiene las columnas con nombres de argumentos y tantas filas como la tabla de entrada.

**Ejemplo**

El ejemplo siguiente muestra varios tipos de manipulaciones que se pueden hacer utilizando el operador `project`. La tabla de entrada `T` tiene tres columnas de tipo `int`: `A`, `B` y `C`.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```



## Operador range

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

* *ColumnName:* el nombre de la única columna de la tabla de salida.
* *Start:* el menor valor de la salida.
* *Stop:* el valor más alto que se genera en la salida (o un límite en el valor más alto si el valor *step* sobrepasa este valor).
* *Step:* la diferencia entre dos valores consecutivos. 

Los argumentos tienen que ser valores numéricos, de fecha o intervalo de tiempo. No pueden tener como referencia las columnas de una tabla. (Si desea calcular el intervalo basándose en una tabla de entrada, use la función [range ](app-insights-analytics-scalars.md#range), posiblemente con el [operador mvexpand](#mvexpand-operator)).

**Devoluciones**

Una tabla con una única columna denominada *ColumnName*, cuyos valores son *Start*, *Start* + *Step*, ... hasta *Stop* que también se incluye.

**Ejemplo**

```AIQL
range Steps from 1 to 8 step 3
```

Una tabla con una única columna denominada `Steps` cuyo tipo sea `long` y cuyos valores sean `1`, `4`, y `7`.

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

Muestra cómo el operador `range` puede utilizarse para crear una pequeña tabla de dimensiones ad hoc que después se usa para introducir ceros donde los datos de origen no tienen ningún valor.

## Operador reduce

    exceptions | reduce by outerMessage

Intenta agrupar registros similares. Para cada grupo, el operador envía el `Pattern` que cree que mejor describe a ese grupo y el `Count` de registros de ese grupo.


![](./media/app-insights-analytics-queries/reduce.png)

**Sintaxis**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName:* la columna que se va a examinar. Tiene que ser de tipo cadena.
* *Threshold:* un valor en el intervalo {0..1}. El valor predeterminado es 0.001. Para las entradas grandes, el valor threshold debe ser pequeño. 

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


## Directiva render

    T | render [ table | timechart  | barchart | piechart ]

Render indica cómo mostrar la tabla de la capa de presentación. Debe ser el último elemento de la canalización. Es una alternativa cómoda a usar los controles de la pantalla, lo que le permite guardar una consulta con un método de presentación determinado.


## Operador sort 

    T | sort by country asc, price desc

Ordena las filas de la tabla de entrada en una o más columnas.

**Alias** `order`

**Sintaxis**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* la entrada de tabla que se va a ordenar.
* *Column:* columna de *T* por la que se ordena. El tipo de los valores tiene que ser numérico, fecha, hora o cadena.
* `asc` Ordenar en orden ascendente, del valor más bajo al más alto. El valor predeterminado es `desc`, descendente del valor más alto al más bajo.

**Ejemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas las filas de la tabla Traces que tienen un determinado valor `ActivityId`, ordenadas por su marca de tiempo.

## Operador summarize

Crea una tabla que agrega el contenido de la tabla de entrada.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Una tabla que muestra el número, la duración media de las solicitudes y el conjunto de ciudades en cada país. Hay una fila en la salida de cada país específico. Las columnas de salida muestran el recuento, promedio de duración, ciudades y país. El resto de columnas de entrada se ignoran.


    T | summarize count() by price_range=bin(price, 10.0)

Una tabla que muestra cuántos elementos tienen precios en cada intervalo [0,10.0], [10.0,20.0], y así sucesivamente. Este ejemplo tiene una columna para el recuento y otra para el intervalo de precios. El resto de columnas de entrada se ignoran.

[Más ejemplos](app-insights-analytics-aggregations.md).



**Sintaxis**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Column:* nombre opcional para una columna de resultados. El valor predeterminado es un nombre derivado de la expresión.
* *Aggregation:* una llamada a una [función de agregación](app-insights-analytics-aggregations.md) como `count()` o `avg()`, con nombres de columna como argumentos. Consulte la [lista de funciones de agregación](app-insights-analytics-aggregations.md).
* *GroupExpression:* una expresión sobre las columnas que ofrece un conjunto de valores específicos. Normalmente se trata de un nombre de columna que ya ofrece un conjunto restringido de valores, o `bin()` con una columna numérica o temporal como argumento. 

Si incluye una expresión numérica o temporal sin utilizar `bin()`, Analytics la aplica automáticamente con un intervalo de `1h` para horas o `1.0` para números.

Si no ofrece un valor *GroupExpression*, toda la tabla se resume en una única fila de salida.



**Devoluciones**

Las filas de entrada están organizadas en grupos que tienen los mismos valores que las expresiones `by`. A continuación, las funciones de agregación especificadas se calculan sobre cada grupo, generando una fila para cada grupo. El resultado contiene las columnas `by` y también al menos una columna para cada agregación calculada. (Algunas funciones de agregación devuelven varias columnas.)

El resultado tiene tantas filas como distintas combinaciones de valores `by`. Si desea resumir intervalos de valores numéricos, utilice `bin()` para reducir los intervalos a valores discretos.

**Nota:**

Aunque puede ofrecer expresiones arbitrarias tanto para las expresiones de agregación como para las de agrupación, resulta más eficaz usar nombres de columna sencillos, o aplicar `bin()` a una columna numérica.



## Operador take

Alias de [límite](#limit-operator)


## Operador top

    T | top 5 by Name desc

Devuelve los primeros registros *N* ordenados por las columnas especificadas.


**Sintaxis**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argumentos**

* *NumberOfRows:* el número de filas de *T* para devolver.
* *Sort\_expression:* una expresión por la que ordenar las filas. Suele ser un nombre de columna. Puede especificar más de una sort\_expression.
* `asc` o `desc` (valor predeterminado) puede aparecer para controlar si la selección se hace desde "abajo" o desde "arriba" del intervalo.


**Sugerencias**

`top 5 by name` es superficialmente equivalente a `sort by name | take 5`. Sin embargo, se ejecuta más rápido y siempre devuelve resultados ordenados, mientras que `take` no ofrece ninguna garantía en este sentido.


## Operador union

     Table1 | union Table2, Table3

Toma dos o más tablas y devuelve las filas de todas ellas.

**Sintaxis**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Tabla1*, *Tabla2*...
 *  El nombre de una tabla, como `events`; o
 *  Una expresión de consulta como `(events | where id==42)`
 *  Un conjunto de tablas especificado con un carácter comodín. Por ejemplo, `E*` podría formar la unión de todas las tablas de la base de datos cuyos nombres comenzaran por `E`.
* `kind`: 
 * `inner` - El resultado tiene el subconjunto de columnas que son comunes a todas las tablas de entrada.
 * `outer` - El resultado tiene todas las columnas que se producen en cualquiera de las entradas. Las celdas que no se han definido mediante una fila de entrada se establecen en `null`.
* `withsource=`* ColumnName: * si se especifica, la salida incluirá una columna denominada *ColumnName* cuyo valor indicará qué tabla de origen ha contribuido a cada fila.

**Devoluciones**

Una tabla con todas las filas que hay en las tablas de entrada.

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
El número de usuarios diferentes que han creado ya sea un evento `exceptions` o un evento `traces` a lo largo del día anterior. En el resultado, la columna "SourceTable" indicará "Query" o "Command".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versión más eficaz, produce el mismo resultado. Filtra cada tabla antes de crear la unión.

## Operador where

     T | where fruit=="apple"

Filtra una tabla para el subconjunto de filas que cumplen un predicado.

**Alias** `filter`

**Sintaxis**

    T | where Predicate

**Argumentos**

* *T*: la entrada tabular cuyos registros se van a filtrar.
* *Predicate:* una [expresión](app-insights-analytics-scalars.md#boolean) `boolean` sobre las columnas de *T*. Se evalúa para cada fila de *T*.

**Devoluciones**

Filas de *T* para las que *Predicate* es `true`.

**Sugerencias**

Para obtener el rendimiento más rápido:

* **Use comparaciones simples** entre los nombres de columna y las constantes. ("Constante" significa constante sobre la tabla, por lo que `now()` y `ago()` son correctos, y también lo son los valores escalares asignados mediante una [instrucción `let`](#let-clause)).

    Por ejemplo, se prefiere `where Timestamp >= ago(1d)` a `where floor(Timestamp, 1d) == ago(1d)`.

* **Coloque primero los términos más simples**: si tiene varias cláusulas unidas con `and`, coloque primero las cláusulas que impliquen una sola columna. Así pues, es mejor usar `Timestamp > ago(1d) and OpId == EventId` que ordenarlo al revés.


**Ejemplo**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registros con antigüedad no superior a 1 hora y que proceden desde el origen denominado "Kuskus" y tienen dos columnas del mismo valor.

Observe que colocamos la comparación entre dos columnas al final, ya que no puede utilizar el índice y exige un examen.





[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->