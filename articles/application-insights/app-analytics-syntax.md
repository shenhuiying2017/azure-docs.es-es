<properties 
	pageTitle="Instrucciones de Application Analytics" 
	description="Consultas, expresiones e instrucciones let en Application Analytics, la eficaz herramienta de búsqueda de Application Insights." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Instrucciones de Application Analytics

[Application Analytics](app-analytics.md) es un motor de búsqueda muy eficaz para la telemetría de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Application Analytics, CSL.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Modelo de datos

En CSL:

* Una *base de datos* contiene cero o varios elementos denominados *tablas*.
* Una *tabla* contiene:
 * Uno o varios elementos denominados *columnas*. Cada columna tiene un *tipo*.
 * Una o varias *filas*.
* Cada fila tiene una o varias *celdas*, una para cada columna de la tabla.
* Una celda puede contener un valor del tipo de la columna o `null`.


## Nombres de entidad

Cada columna, tabla o base de datos tiene un nombre único dentro de su contenedor.

Se debe hace referencia a una entidad por su nombre (si el contexto es inequívoco) o por el nombre completo de su contenedor. Por ejemplo, también se puede hacer referencia a `MyColumn` como `MyTable.MyColumn` o `MyDatabase.MyTable.MyColumn`.

Los nombres pueden contener hasta 1024 caracteres. Se distinguen mayúsculas de minúsculas y pueden contener letras, dígitos y caracteres de subrayado (`_`).

Además, los nombres pueden establecerse para que puedan contener otros caracteres. Por ejemplo:

|||
|---|---|
|`['path\\file\n\'x\'']` | Se utiliza `` como carácter de escape.|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| No se utilizan caracteres de escape: `` es literal.|
|`[@"\now & then"]` | |
|`[where]` | Se utiliza una palabra clave del lenguaje como nombre.|

Los nombres también se pueden completar con el nombre de la base de datos (consulte la sección sobre [consultas cruzadas de la base de datos](#cross-database-queries)).

```
database("MyDb").Table
```


## Instrucciones

En CSL, hay cuatro tipos de instrucciones:

### Consultas de datos
  
Solicitudes de solo lectura sobre los datos almacenados en Application Analytics. Por ejemplo:

* `event`: devuelve todos los registros de la tabla denominada "event".
* `event | count`: devuelve el número de registros de "event".

    
## Instrucciones let

#### Información general
Es posible prefijar una instrucción de consulta de datos con una o varias instrucciones let. Estas permiten enlazar un nombre (un nombre de entidad válido) con una expresión. A continuación, los nombres definidos por una instrucción let se pueden utilizar una o varias veces en la siguiente instrucción de consulta de datos.

Una instrucción let puede enlazar un nombre a las expresiones del tipo siguiente:
1. Escalar
2. Datos tabulares 

Al enlazar a datos tabulares, se puede promover la instrucción let a una "vista", lo que permitirá que la instrucción participe en operaciones "union *".

#### Sintaxis

`let <name> = <expression>`

<expression> puede ser una declaración lambda con cero, uno o más argumentos:

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Ejemplos: en el ejemplo siguiente se enlaza el nombre "x" con el literal escalar "1":

```
let x=1;
range y from x to x step x
```

En el ejemplo siguiente se enlazan registros correspondientes a una sola hora con el nombre de la ventana y, a continuación, se realiza una autocombinación en la ventana:


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

Los dos ejemplos siguientes muestran el uso de una instrucción let con una expresión lambda:


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

Se utiliza la palabra clave "view" para promover las instrucciones let a una vista que participa en operaciones union *.


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Instrucción restrict

#### Información general
Es posible restringir el acceso a las consultas dentro del ámbito de la lista de instrucciones mediante la siguiente instrucción:


```
restrict access to (<entity1, entity2, ...>);
```
 
La instrucción permitirá el acceso solamente a las entidades (instrucciones let o entidades tabulares) que estén permitidas por la instrucción restrict.
 
Ejemplos:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Formato de una consulta

Una *consulta* sigue el patrón siguiente:

- *origen* | *filtro* | *filtro*...

Por ejemplo:


```
Logs | where Timestamp > ago(1d) | count
```

* *Origen* es el nombre de una tabla de base de datos o una tabla de resultados definida anteriormente.
* Cada *filtro* invoca un operador de consulta, como `where` o `count`, con los parámetros adecuados. Los parámetros pueden ser *expresiones escalares*, *consultas de datos* anidadas o nombres de columna.

Por ejemplo:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Instrucciones let

Una instrucción let se puede utilizar para definir funciones (expresiones con nombre con cero o más argumentos y que devuelvan valores). A continuación, otras instrucciones pueden "invocar" esas funciones.

### Valores con nombre

Defina los nombres para representar los valores escalares:


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Defina los nombres para representar los resultados de la consulta:


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

Esto es especialmente útil para una autocombinación:


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Funciones con nombre

Defina las funciones que devuelven resultados escalares:


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Defina las funciones que devuelven resultados de las consultas:


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

Los parámetros de las funciones con nombre deben ser valores escalares.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->