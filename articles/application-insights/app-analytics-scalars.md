<properties 
	pageTitle="Expresiones escalares de la herramienta Analytics de Application Insights" 
	description="Números, cadenas, expresiones dinámicas y tipos de Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>


 
# Expresiones escalares de Analytics


[Analytics](app-analytics.md) es una eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) 
<br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) 
<br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt) 
<br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) 
| [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



"Escalar" hace referencia a valores como números o cadenas que pueden ocupar una sola celda en una tabla. Las expresiones escalares se crean a partir de operadores y funciones escalares y se corresponden con valores escalares. `sqrt(score)/100 > target+2` es una expresión escalar.

"Escalar" también incluye matrices y objetos compuestos, que también se pueden almacenar en una sola celda de la base de datos.

Las expresiones escalares son distintas de las [consultas](app-analytics-queries.md), cuyos resultados son tablas.

## Escalares

[casts](#casts) | [comparisons](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

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

Una cadena que representa el tipo de almacenamiento subyacente de su único argumento. Esto es especialmente útil cuando tienen valores de tipo `dynamic`: en este caso, `gettype()` mostrará cómo se codifica un valor.

**Ejemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**Sintaxis**

    hash(source [, mod])

**Argumentos**

* *source*: el escalar de origen sobre el que se calcula el valor hash.
* *mod*: el valor de módulo que se aplicará sobre el resultado de hash.

**Devoluciones**

El valor de xxhash (long) del escalar dado, aplicando la operación módulo según el valor mod determinado (si se especifica).

**Ejemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

La función `iff()` evalúa el primer argumento (el predicado) y devuelve el valor del argumento segundo o tercero, dependiendo de si el predicado es `true` o `false`. El segundo y tercer argumento deben ser del mismo tipo.

**Sintaxis**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicate:* una expresión que se corresponde con un valor `boolean`.
* *ifTrue:* una expresión que se calcula y su valor se devuelve de la función si *predicate* equivale a `true`.
* *ifFalse:* una expresión que se calcula y su valor se devuelve de la función si *predicate* equivale a `false`.

**Devoluciones**

Esta función devuelve el valor de *ifTrue* si *predicate* equivale a `true`, o el valor de *ifFalse* en caso contrario.

**Ejemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> 
<a name="isnotnull"/></a> 
<a name="notnull"/></a>
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
|parsejson("[]")|false
|parsejson("{}")|false

**Ejemplo**

    T | where isnotnull(PossiblyNull) | count

Tenga en cuenta que hay otras formas de conseguir este efecto:

    T | summarize count(PossiblyNull)




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

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

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
|`>` |Mayor que 
|`>=`|Mayor que o Igual a 
|`<>`|No igual a 
|`!=`|No igual a




### bin

Redondea los valores hacia abajo hasta un entero múltiplo del tamaño de un intervalo determinado. Se utiliza mucho en la consulta [`summarize by`](app-analytics-queries.md#summarize-operator). Si tiene un conjunto de valores dispersos, se agruparán en un conjunto más pequeño de valores específicos.

Alias `floor`.

**Sintaxis**

     bin(value, roundTo)

**Argumentos**

* *value:* un número, una fecha o un intervalo de tiempo. 
* *roundTo:* el "tamaño del intervalo". Un número, una fecha o un intervalo de tiempo que divide *value*. 

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

### floor

Un alias para [`bin()`](#bin).


### rand

Un generador de números aleatorios.

* `rand()`: un número real entre 0,0 y 1,0
* `rand(n)`: un número entero comprendido entre 0 y n-1




### sqrt

La función de raíz cuadrada.

**Sintaxis**

    sqrt(x)

**Argumentos**

* *x:* un número real > = 0.

**Devoluciones**

* Un número positivo como `sqrt(x) * sqrt(x) == x`
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


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

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

Resta un intervalo de tiempo especificado a la hora UTC actual. Al igual que `now()`, esta función puede utilizarse varias veces en una instrucción, y la hora UTC a la que se hace referencia será la misma para todas las instancias.

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



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Número total de días transcurridos desde el domingo anterior, como `timespan`.

**Sintaxis**

    dayofweek(a_date)

**Argumentos**

* `a_date`: A `datetime`.

**Devoluciones**

El `timespan` desde medianoche al principio del domingo anterior, redondeado a un número entero de días.

**Ejemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

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

* *offset:* Un `timespan`, agregado a la hora UTC actual. Valor predeterminado: 0.

**Devoluciones**

La hora UTC actual como `datetime`.

    now() + offset

**Ejemplo**

Determina el intervalo desde el evento identificado por el predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

El inicio del mes que contiene la fecha.

### startofyear

    startofyear(date)

El inicio del año que contiene la fecha.


### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Literales de cadena

Las reglas son las mismas que en JavaScript.

Las cadenas pueden incluirse entre comillas sencillas o dobles.

La barra diagonal inversa (``) se utiliza para marcar los caracteres de escape como `\t` (tabulación), `\n` (nueva línea) y las instancias del carácter de comillas.

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

* *text:* una cadena.
* *search:* la cadena sin formato o la [expresión regular](app-analytics-reference.md#regular-expressions) que coincide en *text*.
* *kind:* `"normal"|"regex"` Predeterminado `normal`. 

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

Obtenga una coincidencia para una [expresión regular](app-analytics-reference.md#regular-expressions) desde una cadena de texto. Opcionalmente, convierte a continuación la subcadena extraída al tipo indicado.

**Sintaxis**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* una [expresión regular](app-analytics-reference.md#regular-expressions).
* *captureGroup:* una constante `int` positiva que indica el grupo de captura que se va a extraer. 0 significa toda la coincidencia; 1, el valor que coincide con el primer elemento "("paréntesis")" de la expresión regular; 2 o más, los posteriores paréntesis.
* *text:* una `string` para buscar.
* *typeLiteral:* un literal de tipo opcional (por ejemplo, `typeof(long)`). Si se proporciona, la subcadena extraída se convierte a este tipo. 

**Devoluciones**

Si *regex* encuentra una coincidencia en *text*: la subcadena que coincide con el *captureGroup* del grupo de captura indicado, opcionalmente convertido en *typeLiteral*.

Si no hay ninguna coincidencia, o se produce un error en la conversión de tipos: `null`.

**Ejemplos**

En el ejemplo de cadena `Trace` se busca una definición para `Duration`. La coincidencia se convierte luego en `real` y se multiplica por una constante de tiempo (`1s`) para que `Duration` sea de tipo `timespan`. En este ejemplo, es igual a 123,45 segundos:

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

* *regex:* la [expresión regular](https://github.com/google/re2/wiki/Syntax) para buscar *text*. Puede contener grupos de captura entre "("paréntesis")". 
* *rewrite:* la expresión regular de reemplazo para cualquier coincidencia encontrada por *matchingRegex*. Use `\0` para hacer referencia a toda la coincidencia, `\1` para el primer grupo de captura, `\2` y valores sucesivos para los grupos de captura posteriores.
* *text:* una cadena.

**Devoluciones**

*text* después de reemplazar todas las coincidencias de *regex* con cálculos de *rewrite*. Las coincidencias no se superponen.

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

* *source:* la cadena de origen de la que se tomará la subcadena.
* *startingIndex:* la posición del carácter inicial basado en cero de la subcadena solicitada.
* *length:* un parámetro opcional que puede utilizarse para especificar el número de caracteres solicitado de la subcadena. 

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


## Matrices y objetos: tipos dinámicos

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) 
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Este es el resultado de una consulta en una excepción de Application Insights. El valor de `details` es una matriz.

![](./media/app-analytics-scalars/310.png)

**Indexing:** matrices y objeto de índice, al igual que en JavaScript:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Sin embargo, use `arraylength` y otras funciones de Analytics (no ".length").

**Casting**: en algunos casos es necesario convertir un elemento que se extrae de un objeto, ya que puede variar en su tipo. Por ejemplo, `summarize...to` necesita un tipo específico:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literals**: para crear un objeto explícito de matriz o contenedor de propiedades, escríbalo como una cadena JSON y convierta:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** para separar las propiedades de un objeto en filas independientes, use mvexpand:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


**treepath:** para buscar todas las rutas en un objeto complejo:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema:** para buscar el esquema mínimo que admite todos los valores de la expresión en la tabla:

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

* `parsejson('[43, 21, 65]')`: una matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`: un valor único de tipo dinámico que contiene un número
* `parsejson('"21"')`: un valor único de tipo dinámico que contiene una cadena

Tenga en cuenta que, a diferencia de JavaScript, JSON exige el uso de comillas dobles (`"`) en torno a las cadenas. Por lo tanto, generalmente es más fácil citar literales de cadena codificados en JSON con comillas simples (`'`).

En este ejemplo se crea un valor dinámico y, a continuación, se utilizan sus campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Operadores y funciones en tipos dinámicos

|||
|---|---|
| *value* `in` *array*| True si hay un elemento de *array* == *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True si no hay ningún elemento de *array* == *value*
|[`arraylength(`array`)`](#arraylength)| Es null si no es una matriz
|[`extractjson(`path,object`)`](#extractjson)|Usa una ruta para navegar al objeto.
|[`parsejson(`source`)`](#parsejson)| Convierte una cadena JSON en un objeto dinámico.
|[`range(`from,to,step`)`](#range)| Una matriz de valores
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | Replica una fila para cada valor de una lista en una celda especificada.
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |Deduce el esquema de tipo del contenido de la columna
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| Acopla grupos de filas y coloca los valores de la columna en una matriz.
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | Acopla grupos de filas y coloca los valores de la columna en una matriz, sin duplicación.

### Objetos dinámicos en cláusulas let


[Las cláusulas let](app-analytics-queries.md#let-clause) almacenan valores dinámicos como cadenas, por lo que estas dos cláusulas son equivalentes, y ambas requieren `parsejson` (o `todynamic`) antes de que se utilicen:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

El número de elementos de una matriz dinámica.

**Sintaxis**

    arraylength(array)

**Argumentos**

* *array:* un valor `dynamic`.

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

La notación [entre corchetes] y la expresión punto notación son equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### Expresiones de ruta de JSON

|||
|---|---|
|`$`|Objeto raíz|
|`@`|Objeto actual|
|`.` o `[ ]` | Elemento secundario|
|`[ ]`|Subíndice de matriz|

*(Actualmente no implementamos comodines, recurrencia, unión o segmentos).*


**Consejos de rendimiento**

* Aplique cláusulas where antes de usar `extractjson()`
* Considere el uso de una coincidencia de expresión regular con [extract](#extract) en su lugar. Esto puede ejecutarse mucho más rápido, y es efectivo si JSON se genera a partir de una plantilla.
* Use `parsejson()` si necesita extraer más de un valor de JSON.
* Considere la posibilidad de analizar el JSON en ingesta declarando que el tipo de columna sea dinámico.



### parsejson

Interpreta `string` como un [valor JSON](http://json.org/)) y devuelve el valor como `dynamic`. Es superior al uso de `extractjson()` cuando necesita extraer más de un elemento de un objeto compuesto de JSON.

**Sintaxis**

    parsejson(json)

**Argumentos**

* *json:* un documento JSON.

**Devoluciones**

Un objeto de tipo `dynamic` especificado por *json*.

**Ejemplo**

En el ejemplo siguiente, cuando `context_custom_metrics` es un `string` con esta apariencia:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Después, el siguiente fragmento recupera el valor de la ranura `duration` del objeto, y desde ahí recupera dos ranuras, `duration.value` y `duration.min` (`118.0` y `110.0` respectivamente).

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

* *start:* el valor del primer elemento de la matriz resultante. 
* *stop:* el valor del último elemento de la matriz resultante, o el valor mínimo que sea mayor que el último elemento de la matriz resultante y dentro de un entero múltiplo de *step* desde *start*.
* *step:* la diferencia entre dos elementos consecutivos de la matriz.

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



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->