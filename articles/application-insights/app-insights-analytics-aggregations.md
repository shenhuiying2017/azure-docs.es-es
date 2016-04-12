<properties 
	pageTitle="Instrucción de resumen y funciones de agregación de la herramienta Analytics de Application Insights" 
	description="Referencia para las funciones de agregación y la instrucción de resumen en Analytics, la eficaz herramienta de búsqueda para Application Insights." 
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

# Funciones de agregación de Analytics

[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]


## Operador summarize

Crea una tabla que agrega el contenido de la tabla de entrada.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-insights-analytics-aggregations/01.png)

* Los registros de entrada se recopilan en grupos; así pues, cada grupo tiene una combinación específica de valores en los campos `by`.
* Las expresiones de agregación se evalúan sobre los miembros de cada grupo.
* Hay una fila de salida para cada combinación de valores distinta de las expresiones "by". 
* Hay una columna de salida para cada expresión de agregado y cada expresión "by". El resto de columnas de entrada se retiran.

### Sintaxis

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**Argumentos**

* *Column:* nombre opcional para una columna de resultados. El valor predeterminado es un nombre derivado de la expresión.
* *Aggregation:* una llamada a una función de agregación como `count()` o `avg()`, con nombres de columna como argumentos. Consulte la lista de funciones de agregación a continuación.
* *GroupExpression:* una expresión sobre las columnas que proporciona un conjunto de valores específicos. Normalmente se trata de un nombre de columna que ya proporciona un conjunto limitado de valores, o `bin()` con una columna numérica o temporal como argumento. 

Si proporciona una expresión numérica o temporal sin utilizar `bin()`, Analytics la aplica automáticamente con un intervalo de `1h` para horas o `1.0` para números.

Si no proporciona un valor *GroupExpression*, toda la tabla se resume en una única fila de salida.

Debe utilizar un tipo simple, no un tipo dinámico, en la cláusula `by`. Por ejemplo, la conversión `tostring` es esencial aquí:

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### Resumen por columnas con valores discretos

Una consulta para mostrar los promedios de los tiempos de respuesta a diferentes solicitudes HTTP, separando las solicitudes que tengan códigos de respuesta diferentes:

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-insights-analytics-aggregations/03.png)


* Las expresiones de agregado (como count y avg) tienen que formarse a partir de las funciones de agregado que se documentan en este artículo. Sus argumentos pueden ser cualquier función escalar.
* Las expresiones de agrupación (después de "by") pueden ser cualquier expresión escalar, pero ofrecen un mejor rendimiento si son solo nombres de campo.

### Resumen por columnas numéricas

Si desea agrupar por un valor escalar continuado como un número o una hora, se debe utilizar la función `bin` (también conocida como `floor`) para distribuir el rango continuo en intervalos.

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-insights-analytics-aggregations/04.png)

(El campo de duración de la solicitud es un número en milisegundos).
 
## Sugerencias

* Utilice `where` para quitar todas las filas que sabe que no desea antes de `summarize`.
 * Quite los valores nulos. Si hay un valor nulo en un grupo, el resultado del agregado será nulo. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* Aunque puede proporcionar expresiones arbitrarias para las expresiones de agregación y agrupación, resulta más eficaz usar nombres de campo sencillos o aplicar `bin()` a un campo numérico.





## Ejemplos

#### Solicitudes de recuento

Busca el número de solicitudes de cada nombre durante la última hora:

    requests | where timestamp > ago(1h)
    | summarize req_count = sum(itemCount) by name

> [AZURE.NOTE] En lugar de simplemente contar los puntos de datos de la solicitud, use la propiedad itemCount. Esto compensa cualquier [muestreo](app-insights-sampling.md) que esté en funcionamiento. Por ejemplo, si el muestreo está establecido en un 33 %, itemCount será 3 en cada punto de datos.

#### Min y Max

Busca la marca de tiempo mínima y máxima de todos los registros de la secuencia de solicitudes. No hay ninguna cláusula de agrupar por, así que hay una sola fila en la salida:

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`2015-12-09 09:21:45` | `2015-12-24 23:45:00`



#### Duración de la sesión

Las sesiones de un registro de eventos tienen varios eventos. Busque el inicio y el fin de cada sesión fijándose en los eventos más recientes o más antiguos de cada sesión:

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

La operación `extend` agrega la columna de duración, con `bin` (también conocido como `floor`) para redondear al segundo más cercano.

![](./media/app-insights-analytics-aggregations/minmax.png)

#### Ejemplo: duración media

Ahora vamos a buscar las duraciones medias de la sesión para los clientes en diferentes ciudades:

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

Hemos agregado la columna `client_City` a la cláusula `by` para que pase por la primera operación de resumen. Al suponer que todos los eventos de sesión de un cliente se producen en la misma ciudad, no se agregará al número de salidas del resumen.


![](./media/app-insights-analytics-aggregations/durationcity.png)


#### Ejemplo: 

Busque la hora del día más ocupada en la ciudad de cada cliente. En este caso, con "más ocupada" nos referimos a la hora del día en que empieza la mayor cantidad de sesiones en un día promedio.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## AGREGACIONES

## cualquiera 

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
## argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Busca una fila en el grupo que minimiza o maximiza *ExprToMaximize*, y devuelve el valor de *ExprToReturn* (o `*` para devolver toda la fila).

**Sugerencia**: El nombre de las columnas por las que se pasa se cambia automáticamente. Para asegurarse de que está usando los nombres adecuados, revise los resultados mediante `take 5` antes de canalizarlos a otro operador.

**Ejemplos**

Para cada nombre de la solicitud, mostrar cuando se produjo la solicitud más larga:

    requests | summarize argmax(duration, timestamp) by name

Mostrar todos los detalles de la solicitud más larga, no solo la marca de tiempo:

    requests | summarize argmax(duration, *) by name


Buscar el valor más bajo de cada métrica, junto con su marca de tiempo y otros datos:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

Calcula el promedio de *Expression* en todo el grupo.

## buildschema

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

Observe que `indexer` se usa para marcar el punto en el que debe usar un índice numérico. En el caso de este esquema, algunas rutas válidas podrían las siguientes (suponiendo que estos índices de ejemplo se encuentren en el intervalo):

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

#### Modelo de esquema

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


## count

    count([ Predicate ])

Devuelve un número de filas para el que *Predicate* equivale a `true`. Si no hay ningún valor *Predicate* especificado, se devuelve el número total de registros en el grupo.

**Sugerencias de rendimiento**: Use `summarize count(filter)` en lugar de `where filter | summarize count()`

> [AZURE.NOTE] Evite usar count() para buscar el número de solicitudes, excepciones u otros eventos que se hayan producido. Cuando está en funcionamiento el [muestreo](app-insights-sampling.md), el número de puntos de datos será menor que el número de eventos reales. En su lugar, use `summarize sum(itemCount)...`. La propiedad itemCount refleja el número de eventos originales que cada punto de datos retenido representa.
   

## dcount

    dcount( Expression [ ,  Accuracy ])

Devuelve una estimación del número de valores distintos de *Expr* en el grupo. (Para mostrar los valores distintos, use [`makeset`](#makeset)).

*Accuracy*, si se especifica, controla el equilibrio entre velocidad y precisión.

 * `0` = el cálculo menos preciso y más rápido.
 * `1` = el valor predeterminado, que equilibra la precisión y el tiempo de cálculo; error en torno al 0,8 %.
 * `2` = el cálculo más preciso y más lento; error en torno al 0,4 %.

**Ejemplo**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

Devuelve una matriz `dynamic` (JSON) de todos los valores de *Expr* en el grupo.

* *MaxListSize* es un límite de entero opcional para el número máximo de elementos devueltos (el valor predeterminado es *128*).

## makeset

    makeset(Expression [ , MaxSetSize ] )

Devuelve una matriz `dynamic` (JSON) del conjunto de valores distintos que *Expr* toma en el grupo. (Sugerencia: Para contar solo los valores distintos, use [`dcount`](#dcount)).
  
*  *MaxSetSize* es un límite de entero opcional para el número máximo de elementos devueltos (el valor predeterminado es *128*).

**Ejemplo**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/makeset.png)

Vea también el [operador `mvexpand`](app-insights-analytics-queries.md#mvexpand-operator) para la función opuesta.


## max, min

    max(Expr)

Calcula el máximo de *Expr*.
    
    min(Expr)

Calcula el mínimo de *Expr*.

**Sugerencia**: Esto le ofrece el valor mínimo o máximo por sí mismo; por ejemplo, el precio más alto o más bajo. Sin embargo, si desea otras columnas de la fila (como el nombre del proveedor con el precio más bajo, por ejemplo), use [argmin o argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
## percentile, percentiles

    percentile(Expression, Percentile)

Devuelve una estimación para *Expression* de los percentiles especificados en el grupo. La precisión depende de la densidad de población en la región del percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Es como `percentile()`, pero calcula un número de valores de percentil (lo cual es más rápido que calcular de forma individual cada percentil).

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

![](./media/app-insights-analytics-aggregations/percentiles.png)

Los resultados muestran que para la solicitud /Events/Index, el 5 % de las solicitudes se han respondido en menos de 2,44 s, la mitad en 3,52 s, y el 5 % en más de 6,85 s.


Calcular varias estadísticas:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### Error de estimación en percentiles

El agregado de percentiles proporciona un valor aproximado mediante [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Algunos puntos importantes:

* Los límites en el error de estimación varían con el valor del percentil solicitado. Los valores más precisos se encuentran en los extremos de la escala [0...100]; los percentiles 0 y 100 son los valores mínimo y máximo exactos de la distribución. La precisión se reduce gradualmente hacia el centro de la escala. Es peor en la media, y está limitada al 1 %. 
* Los límites de los errores se observan en el rango, no en el valor. Supongamos que el percentil (X, 50) devuelve el valor de Xm. La estimación garantiza que al menos el 49 % y a lo sumo el 51% de los valores de X son inferiores a Xm. No hay ningún límite teórico sobre la diferencia entre Xm y el valor medio real de X.

## stdev

     stdev(Expr)

Devuelve la desviación estándar de *Expr* en el grupo.

## variance

    variance(Expr)

Devuelve la varianza de *Expr* en el grupo.

## sum

    sum(Expr)

Devuelve la suma de *Expr* en el grupo.




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->