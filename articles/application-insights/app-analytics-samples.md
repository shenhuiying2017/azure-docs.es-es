<properties 
	pageTitle="Ejemplos de consultas en Application Insights Analytics" 
	description="Ejemplos de consultas en Application Insights Analytics, la potente herramienta de búsqueda de Application Insights." 
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


# Muestras para Application Insights Analytics

[Application Insights Analytics](app-analytics.md) es un potente motor de búsqueda para la telemetría de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Application Insights Analytics, AIQL. También se ofrece un [recorrido por el lenguaje](app-analytics-tour.md), opción recomendada para empezar.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Procedimientos recomendados sobre las consultas 

Es necesario conocer qué debe hacer y qué no debe hacer para que las consultas se ejecuten con mayor rapidez.

SÍ

-	Usar primero filtros de tiempo. Application Insights Analytics está muy optimizado para el uso de filtros de tiempo.
-	Ponga los filtros que espera que descarten la mayor cantidad de datos al principio de la consulta (justo después de los filtros de tiempo)
-	Compruebe que la mayoría de los filtros aparecen al principio de la consulta (antes de empezar a utilizar "extend"). 
-	Use preferentemente la palabra clave "has" en lugar de "contains" al buscar tokens completos. "has" ofrece mejores rendimientos, ya que no tiene que buscar subcadenas.
-	Elija la búsqueda en columnas específicas en vez de usar "*" (búsqueda de texto completo por todas las columnas)
-	Al usar la combinación, el proyecto solo requiere las columnas de ambos lados de la combinación (esto reducirá la carga de transferida de un equipo al otro)

EVITAR:

-	Probar las nuevas consultas sin "limit" (número pequeño) o "count" al final. La ejecución de consultas independientes sobre un conjunto de datos desconocido puede provocar que se devuelvan GB de resultados al cliente, lo cual tiene como consecuencia una respuesta lenta y la ocupación del clúster.
-	Si observa que está aplicando conversiones (JSON, cadena, etc.) a más de mil millones de registros, remodele la consulta para reducir la cantidad de datos introducidos en la conversión.





<a name="activities"></a>
## Obtención de sesiones a partir de eventos de inicio y detención

Supongamos que tenemos un registro de eventos en el que algunos eventos marcan el inicio o el final de una actividad o sesión ampliada.

|Nombre|City|SessionId|Timestamp|
|---|---|---|---|
|Iniciar|Londres|2817330|2015-12-09T10:12:02.32|
|Juego|Londres|2817330|2015-12-09T10:12:52.45|
|Iniciar|Manchester|4267667|2015-12-09T10:14:02.23|
|Detención|Londres|2817330|2015-12-09T10:23:43.18|
|Cancelar|Manchester|4267667|2015-12-09T10:27:26.29|
|Detención|Manchester|4267667|2015-12-09T10:28:31.72|

Cada evento tiene un identificador de sesión; así pues, el problema radica en hacer coincidir los eventos de inicio y detención que tengan el mismo identificador.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Utilice [`let`](app-analytics-syntax.md#let-statements) para designar un nombre para la proyección de la tabla que se reduce en lo posible antes de comenzar la combinación. [`Project`](app-analytics-queries.md#project-operator) se utiliza para cambiar los nombres de las marcas de tiempo para que los tiempos de inicio y detención puedan aparecer en el resultado. También selecciona las otras columnas que se quieren ver en el resultado. [`join`](app-analytics-queries.md#join-operator) hace coincidir las entradas de inicio y detención de la misma actividad, creando una fila para cada actividad. Por último, `project` agrega de nuevo una columna para mostrar la duración de la actividad.


|City|SessionId|StartTime|StopTime|Duración|
|---|---|---|---|---|
|Londres|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Obtener las sesiones, sin los identificadores de sesión

Ahora vamos a suponer que los eventos de inicio y detención no cuentan con un práctico identificador de sesión que podamos hacer coincidir. Sin embargo, tenemos una dirección IP del cliente donde tuvo lugar la sesión. Suponiendo que cada en dirección de cliente se realizó únicamente una sesión a la vez, podemos hacer coincidir cada evento de inicio con el siguiente evento de detención desde la misma dirección IP.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

La combinación unirá cada hora de inicio con todas las horas de detención desde la misma dirección IP de cliente. Por tanto, en primer lugar quitamos las coincidencias con horas de detención anteriores.

A continuación, agrupamos por hora de inicio e IP para obtener un grupo para cada sesión. Debemos suministrar una función `bin` para el parámetro StartTime: si no lo hacemos, AI Analytics usará automáticamente intervalos de 1 hora, lo cual hará coincidir algunas horas de inicio con horas de detención incorrectas.

`argmin` recoge la fila con la menor duración de cada grupo, y el parámetro `*` se pasa a través de todas las demás columnas, aunque agrega el prefijo "min\_" a cada nombre de columna.


![](./media/app-analytics-samples/040.png)

A continuación, podemos agregar código para contar las duraciones en intervalos con un tamaño más manejable. Preferimos el gráfico de barras, por lo que dividimos por `1s` para convertir los intervalos de tiempo en números.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Ejemplo real

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Representación de sesiones simultáneas a lo largo del tiempo

Supongamos que tenemos una tabla de actividades con sus horas de inicio y finalización. Nos gustaría ver un gráfico en el que se refleje el paso del tiempo que muestre cuántas se ejecutan simultáneamente en cualquier momento.

Esta es una entrada de ejemplo, a la que llamaremos `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Queremos un gráfico en intervalos de 1 minuto, así que deseamos crear algo que, en cada uno de esos intervalos, podamos contar para cada actividad en ejecución.

Este es un resultado intermedio:

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` genera una matriz de valores en los intervalos especificados:

|SessionId | StartTime | StopTime | ejemplos|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Pero en lugar de mantener esas matrices, vamos a [expandirlas](app-analytics-queries.md#mvexpand-operator):

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | ejemplos|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Ahora podemos agruparlas por cada una de las horas de ejemplo, contando las veces que aparece cada actividad:

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Necesitamos todatetime() porque `mvexpand` produce una columna de tipo dinámico.
* Necesitamos bin() porque, en el caso de los valores numéricos y de fecha, la función de resumir aplica siempre una función de intervalo con un valor predeterminado si no especifica ninguno. 


| count\_SessionId | ejemplos|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Esto se puede representar como un gráfico de barras o un gráfico de tiempo.


## Tipos de combinación

El tipo exacto del operador de unión se especifica con la palabra clave de variante. En este momento, AI Analytics admite seis tipos de operadores de combinación: combinación interna con desduplicación del lado izquierdo (valor predeterminado), combinación interna estándar, externa izquierda, externa derecha y anticombinación izquierda.
 
Tipo de combinación predeterminado (variante no especificada) Usemos dos tablas de ejemplo para explicar el funcionamiento de la combinación:
 
Tabla X


|Clave |Valor1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Tabla Y

|Clave |Valor2 
|---|---
|b |10 
|c |20 | 
|c |30 
|d |40 

 
La combinación predeterminada realiza una combinación interna después de la desduplicación en el lado izquierdo de la clave de combinación (la desduplicación conserva el primer registro). Con esta instrucción:

    X | join Y on Key 

el lado izquierdo efectivo de la combinación (la tabla X después de la desduplicación) sería:

|Clave |Valor1 
|---|---
|a |1 
|b |2 
|c |4 

y el resultado de la combinación sería:

|Clave |Valor1 |Clave |Valor2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 | 
|c |4 |c |30 

Tenga en cuenta que las claves "a" y "d" no aparecen en la salida, ya que no había ninguna clave coincidente en los lados izquierdo y derecho.
 
Históricamente, esta fue la primera implementación de la combinación compatible con la versión inicial de AI Analytics; resulta útil en los escenarios habituales de análisis de registro/seguimiento donde se desea poner en correlación dos eventos (donde cada uno de ellos coincide con determinados criterios de filtrado) bajo el mismo identificador de correlación, y obtener todas las repeticiones del fenómeno que estamos buscando, omitiendo varias apariciones de los registros de seguimiento contribuyentes.
 
### Combinación interna (variante=inner) 

Se trata de la combinación interna estándar tal y como se conoce en el mundo SQL. Se genera un registro de salida cada vez que un registro del lado izquierdo tenga la misma clave de combinación que el registro del lado derecho.
 
    X | join kind=inner Y on Key 

genera:

|Clave |Valor1 |Clave |Valor2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 | 
|c |4 |c |30 

Observe que (b,10), procedente del lado derecho, se ha combinado dos veces: con (b,2) y (b, 3) a la izquierda; además, (c,4), procedente del lado izquierdo, se ha combinado dos veces: con (c,20) y (c,30) en el lado derecho.

### Combinación externa izquierda (variante=leftouter) 

El resultado de una combinación externa izquierda para las tablas X e Y contiene siempre todos los registros de la tabla izquierda (X), incluso si la condición de combinación no encuentra ningún registro coincidente en la tabla derecha (Y).
 
Con estas premisas:

    X | join kind=leftouter Y on Key 

Resultado:

|Clave |Valor1 |Clave |Valor2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 | 
|c |4 |c |30 
 
### Combinación externa derecha (variante=rightouter) 

Es similar a la combinación externa izquierda, pero se invierte el tratamiento de las tablas.
 
Con estas premisas:

    X | join kind=rightouter Y on Key 

Resultado:


|Clave |Valor1 |Clave |Valor2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 | 
|c |4 |c |30 
|null |null |d |40 
 
### Combinación externa completa (variante=fullouter) 

Conceptualmente, una combinación externa completa combina el efecto de aplicar combinaciones derecha e izquierda externas. Cuando los registros de las tablas combinadas no coinciden, el conjunto de resultados tendrá valores NULL para cada columna de la tabla que no tenga una fila coincidente. Para aquellos registros que coincidan, se producirá una única fila del conjunto de resultados (que contiene campos completados con valores de ambas tablas).
 
Con estas premisas:

    X | join kind=fullouter Y on Key 

Resultado:

|Clave |Valor1 |Clave |Valor2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 | 
|c |4 |c |30 
|null |null |d |40 
 
### Anticombinación izquierda (variante=leftanti) 

La anticombinación izquierda devuelve todos los registros del lado izquierdo que no coincidan con ningún registro del lado derecho.
 
    X | join kind=leftanti Y on Key 
 
Resultado:

|Clave |Valor1 
|---|---
|a |1 
 
La anticombinación modela la consulta "NOT IN".



## Combinación con asignación de diccionario en línea

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->