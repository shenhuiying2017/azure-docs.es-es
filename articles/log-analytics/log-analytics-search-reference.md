<properties
	pageTitle="Referencia sobre búsqueda de registros de Log Analytics | Microsoft Azure"
	description="En la referencia sobre búsqueda de registros de Log Analytics, se describe el lenguaje de consulta y se proporcionan las opciones generales de sintaxis de consulta que puede usar al buscar datos y filtrar expresiones para ayudar a limitar la búsqueda."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# Referencia sobre búsqueda de registros de Log Analytics

En la siguiente sección de referencia sobre el lenguaje de búsqueda se describen las opciones generales de sintaxis de consulta que puede usar al buscar datos y filtrar expresiones para ayudar a limitar la búsqueda. También se describen los comandos que puede usar para realizar acciones en los datos recuperados.

Puede leer sobre los campos devueltos en las búsquedas y las facetas que lo ayudarán a explorar categorías similares de datos en la [sección Campo de búsqueda y referencia de faceta](#search-field-and-facet-reference).

## Sintaxis de consulta general

Sintaxis:

```
filterExpression | command1 | command2 …
```

La expresión de filtro (`filterExpression`) define la condición "where" para la consulta. Los comandos se aplican a los resultados devueltos por la consulta. Varios comandos deben separarse con el carácter de barra vertical ( | ).

### Ejemplos de sintaxis general

Ejemplos:

```
system
```

Esta consulta devuelve resultados que contienen la palabra "system" en cualquier campo que se ha indexado para la búsqueda de términos o texto completo.

>[AZURE.NOTE] No todos los campos se indexan de este modo, pero los campos de texto más comunes (como nombres y descripciones) normalmente sí.

```
system error
```

Esta consulta devuelve resultados que contienen las palabras *system* y *error*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Esta consulta devuelve resultados que contienen las palabras *system* y *error*. Luego, ordena los resultados por el campo *ManagementGroupName* (en orden ascendente) y luego por *TimeGenerated* (en orden descendente). Solo toma los 10 primeros resultados.

>[AZURE.IMPORTANT] Todos los nombres de campo y los valores de los campos de cadena y texto distinguen mayúsculas de minúsculas.

## Expresión de filtro

En las subsecciones siguientes se explican las expresiones de filtro.

### Literales de cadena

Un literal de cadena es cualquier cadena que no reconoce el analizador como una palabra clave o un tipo de dato predefinido (por ejemplo, un número o una fecha).

Ejemplos:

```
These all are string literals
```

Esta consulta busca los resultados que contienen las apariciones de las cinco palabras. Para realizar una búsqueda de cadena compleja, incluya el literal de cadena entre comillas, por ejemplo:

```
" Windows Server"
```

Esto solo devuelve resultados con coincidencias exactas de "Windows Server".

### Números

El analizador admite la sintaxis de entero decimal y punto flotante para campos numéricos.

Ejemplos:

```
Type:Perf 0.5
```

```
HTTP 500
```

### Fecha y hora

Cada parte de los datos del sistema tiene una propiedad *TimeGenerated*, que representa la fecha y la hora originales del registro. Algunos tipos de datos pueden tener además más campos de fecha y hora (por ejemplo, *LastModified*).

El selector de gráfico y hora de escala de tiempo de Log Analytics muestra una distribución de los resultados en el tiempo (según la consulta actual que se ejecuta), basada en el campo *TimeGenerated*. Los campos de fecha y hora tienen un formato de cadena específico que puede usarse en las consultas para restringirlas a un período de tiempo concreto. También puede usar la sintaxis para hacer referencia a intervalos de tiempo relativos (por ejemplo, "entre hace tres días y hace dos horas").

Sintaxis:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Ejemplo:

```
TimeGenerated:2013-10-01T12:20
```

El comando anterior solo devuelve los registros con un valor *TimeGenerated* de exactamente 12:20 el 1 de octubre de 2013. No es probable que proporcione ningún resultado, pero puede hacerse una idea.

El analizador admite también el valor de fecha y hora mnemotécnico, NOW.


De nuevo, es poco probable que esto produzca ningún resultado porque los datos no pasan por el sistema tan rápido.

Estos ejemplos son bloques de creación que se usan para fechas relativas y absolutas. En las siguientes tres subsecciones, explicaremos cómo usarlos en filtros más avanzados con ejemplos que emplean intervalos de fechas relativas.

### Operadores matemáticos Date/Time

Use los operadores matemáticos Date/Time para desplazar o redondear el valor de fecha y hora mediante el uso de cálculos de fecha y hora sencillos.

Sintaxis:

```
datetime/unit
```

```
datetime[+|-]count unit
```

|Operador|Descripción|
|---|---|
|/|Redondea la fecha y hora a la unidad especificada. Ejemplo: NOW/DAY redondea la fecha y hora actuales a la medianoche del día actual.|
|+ o -|Desplaza la fecha y hora el número especificado de unidades. Ejemplos: NOW+1HOUR desplaza la fecha y hora actuales una hora hacia delante. 2013-10-01T12:00-10DAYS desplaza el valor de fecha 10 días hacia atrás.|



Puede encadenar los operadores matemáticos Date/Time juntos, por ejemplo:

```
NOW+1HOUR-10MONTHS/MINUTE
```

En la tabla siguiente se enumeran las unidades de fecha y hora compatibles.

Unidad Date/Time|Descripción
---|---
AÑO, AÑOS|Redondea al año actual, o desplaza el número especificado de años.
MES, MESES|Redondea al mes actual, o desplaza el número especificado de meses.
DÍA, DÍAS, FECHA|Redondea al día actual del mes, o desplaza el número especificado de días.
HORA, HORAS|Redondea a la hora actual, o desplaza el número especificado de horas.
MINUTO, MINUTOS|Redondea al minuto actual, o desplaza el número especificado de minutos.
SEGUNDO, SEGUNDOS|Redondea al segundo actual, o desplaza el número especificado de segundos.
MILISEGUNDO, MILISEGUNDOS, MILI, MILIS|Redondea al milisegundo actual, o desplaza el número especificado de milisegundos.


### Facetas de campo

Mediante el uso de facetas de campo, puede especificar la condición de búsqueda para campos específicos y sus valores exactos, en lugar de escribir consultas de "texto libre" para varios términos en todo el índice. Ya hemos usado esta sintaxis en varios ejemplos en los párrafos anteriores. En este caso, proporcionamos ejemplos más complejos.

**Sintaxis**

```
field:value
```

```
field=value
```

**Descripción**

Busca en el campo el valor específico. El valor puede ser un literal de cadena, un número o una fecha u hora.

Ejemplo:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Sintaxis**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**Descripción**

Proporciona comparaciones.

Ejemplo:

```
TimeGenerated>NOW+2HOURS
```

**Sintaxis**

```
field:[from..to]
```

**Descripción**

Proporciona facetas de intervalo.

Ejemplo:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### Operadores lógicos

Los lenguajes de consulta admiten los operadores lógicos (*AND*, *OR* y *NOT*) y sus alias de estilo C (*&&*, *||* y *!*) respectivamente. Puede usar paréntesis para agrupar estos operadores.

Ejemplos:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Puede omitir el operador lógico para los argumentos de filtro de nivel superior. En este caso, se supone el operador AND.


Expresión de filtro|Equivalente a
---|---
error del sistema|sistema Y error
sistema "Windows Server" O gravedad:1|sistema Y "Windows Server" O gravedad:1

### Caracteres comodín

El lenguaje de consulta admite que se use el carácter (**) para representar uno o más caracteres para un valor en una consulta.

Ejemplos:

 Buscar todos los equipos con "SQL" en el nombre, como "Redmond SQL".

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] Actualmente no se pueden usar caracteres comodín entrecomillados. En Message=`"*This text*"`, se considerará que el asterisco (*) se usa como carácter literal (*).

## Comandos:

Los comandos se aplican a los resultados devueltos por la consulta. Use el carácter de barra vertical ( | ) para aplicar un comando a los resultados recuperados. Varios comandos deben separarse con el carácter de barra vertical ( | ).

>[AZURE.NOTE] Los nombres de los comandos pueden escribirse en mayúsculas o minúsculas, a diferencia de los nombres de campo y los datos.

### Sort

Sintaxis:

	sort field1 asc|desc, field2 asc|desc, …

Ordena los resultados por campos concretos. El prefijo asc o desc es opcional. Si se omiten, se supone que el criterio de ordenación es *asc*. Si una consulta no usa el comando *Sort* explícitamente, Sort **TimeGenerated** desc es el comportamiento predeterminado y siempre devolverá los resultados más recientes en primer lugar.

### Top/Limit

Sintaxis:

	top number


	limit number
Limita la respuesta a los N resultados principales.

Ejemplo:

	Type:Alert errors detected | top 10

Devuelve los 10 resultados coincidentes principales.

### Skip

Sintaxis:

	skip number

Omite el número de resultados que se enumeran.

Ejemplo:

	Type:Alert errors detected | top 10 | skip 200

Devuelve los 10 resultados principales coincidentes empezando por el resultado 200.

### Seleccionar

Sintaxis:

	select field1, field2, ...

Limita los resultados a los campos elegidos.

Ejemplo:

	Type:Alert errors detected | select Name, Severity

Limita los campos de resultados devueltos a *Name** y *Severity*.

### Measure

El comando *measure* se usa para aplicar funciones estadísticas a los resultados de búsqueda sin procesar. Esto resulta muy útil para obtener vistas *group-by* sobre los datos. Cuando se usa el comando *measure*, la búsqueda de Log Analytics muestra una tabla con resultados agregados.

Sintaxis:

	 measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]

	 measure aggregateFunction1([aggregatedField]) [as fieldAlias1] , aggregateFunction2([aggregatedField]) [as fieldAlias2] by groupField [interval interval]

	 measure aggregateFunction([aggregatedField])  interval interval

	 measure aggregateFunction1([aggregatedField]), aggregateFunction2([aggregatedField]), ...  interval interval


Agrega los resultados por *groupField* y calcula los valores de medida agregados mediante el uso de *aggregatedField*.


|Función estadística de medida|Descripción|
|---|---|
|*aggregateFunction*|Nombre de la función de agregado (no distingue mayúsculas de minúsculas). Se admiten las siguientes funciones de agregado: COUNT, MAX, MIN, SUM, AVG, STDDEV, COUNTDISTINCT, PERCENTILE## o PCT## (## es cualquier número entre 1 y 99).|
|*aggregatedField*|Campo que se agrega. Este campo es opcional para la función de agregado COUNT, pero debe ser un campo numérico existente para SUM, MAX, MIN, AVG, STDDEV, PERCENTILE## o PCT## (## es cualquier número entre 1 y 99).|
|*fieldAlias*|Alias (opcional) para el valor agregado calculado. Si no se especifica, el nombre del campo será AggregatedValue.|
|*groupField*|Nombre del campo por el que se agrupa el conjunto de resultados.|
|*Intervalo*|El intervalo de tiempo en el formato:**nnnNAME**, donde nnn es el número entero positivo. **NAME** es el nombre del intervalo. Entre los nombres de intervalo admitidos, se incluyen (con distinción de mayúsculas y minúsculas): MILLISECOND[S], SECOND[S], MINUTE[S], HOUR[S], DAY[S], MONTH[S], YEAR[S].|


La opción de intervalo solo se puede usar en los campos de grupo de fecha y hora (como *TimeGenerated* y *TimeCreated*). Actualmente, el servicio no aplica esta directriz, pero un campo sin fecha y hora que se pasa al back-end producirá un error en tiempo de ejecución. Cuando se implementa la validación de esquema, la API del servicio rechaza las consultas que usan campos sin fecha y hora para la agregación de intervalo. La implementación actual de *measure* admite la agrupación de intervalos para cualquier función de agregado.

Si se omite la cláusula BY, pero se especifica un intervalo (como una segunda sintaxis), el campo *TimeGenerated* se asume de forma predeterminada.

Ejemplos:

**Ejemplo 1**

	Type:Alert | measure count() as Count by ObjectId

*Explicación*

Agrupa las alertas por *ObjectID* y calcula el número de alertas para cada grupo. El valor agregado se devuelve como el campo *Count* (alias).

**Ejemplo 2**

	Type:Alert | measure count() interval 1HOUR

*Explicación*

Agrupa las alertas por intervalos de una hora mediante el uso del campo *TimeGenerated* y devuelve el número de alertas en cada intervalo.

**Ejemplo 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explicación*

Igual que el ejemplo anterior, pero con un alias de campo agregado (*AlertsPerHour*).

**Ejemplo 4**

	* | measure count() by TimeCreated interval 5DAYS

*Explicación*

Agrupa los resultados por intervalos de cinco días mediante el uso del campo *TimeCreated* y devuelve el número de resultados en cada intervalo.

**Ejemplo 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Explicación*

Agrupa las alertas por nombre de carga de trabajo y devuelve el valor máximo de gravedad de alerta para cada flujo de trabajo.

**Ejemplo 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Explicación*

Igual que el ejemplo anterior, pero con la función agregada *Min*.

**Ejemplo 7**

	Type:Perf | measure avg(CounterValue) by Computer

*Explicación*

Agrupa Perf por equipo y calcula el promedio (avg).

**Ejemplo 8**

	Type:Perf | measure sum(CounterValue) by Computer

*Explicación*

Igual que el ejemplo anterior, pero usa *Sum*.

**Ejemplo 9**

	Type:Perf | measure stddev(CounterValue) by Computer

*Explicación*

Igual que el ejemplo anterior, pero usa *STDDEV*.

**Ejemplo 10**

	Type:Perf | measure percentile70(CounterValue) by Computer

*Explicación*

Igual que el ejemplo anterior, pero usa *PERCENTILE70*.

**Ejemplo 11**

	Type:Perf | measure pct70(CounterValue) by Computer

*Explicación*

Igual que el ejemplo anterior, pero usa *PCT70*. Tenga en cuenta que *PCT##* es solo un alias de la función *PERCENTILE##*.

**Ejemplo 12**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explicación*

Obtiene los cinco principales flujos de trabajo con el número máximo de alertas.

**Ejemplo 13**

	* | measure countdistinct(Computer) by Type

*Explicación*

Cuenta el número de equipos únicos que elaboran informes para cada tipo.

**Ejemplo 14**

	* | measure countdistinct(Computer) Interval 1HOUR

*Explicación*

Cuenta el número de equipos únicos que elaboran informes para cada hora.

**Ejemplo 15**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*Explicación*

Agrupa % de tiempo de procesador por equipo y devuelve el promedio de cada hora.

**Ejemplo 16**

	Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*Explicación*

Agrupa W3CIISLog por método y devuelve el valor máximo para cada 5 minutos.

**Ejemplo 17**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*Explicación*

Agrupa % de tiempo de procesador por equipo y devuelve el mínimo, el promedio, el percentil 75 y el máximo para cada hora.

### Where

Sintaxis:

```
**where** AggregatedValue>20
```

Solo puede usarse después de un comando *measure* para seguir filtrando los resultados agregados que ha producido la función de agregación *measure*.

Ejemplos:

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

	Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### IN

Sintaxis:

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Descripción: esta sintaxis permite crear una agregación y proporcionar la lista de valores resultante a otra búsqueda externa (principal) que buscará eventos con esos valores. Para ello, encierre la búsqueda interna entre llaves y proporcione sus resultados como valores posibles para un campo en la búsqueda externa mediante el operador IN.

Ejemplo de consulta interna: *equipos donde actualmente faltan actualizaciones de seguridad* con la siguiente consulta de agregación:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

La consulta final que busca *todos los eventos de Windows para equipos donde actualmente faltan actualizaciones de seguridad* parecería:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### Dedup

**Sintaxis**

	Dedup FieldName

**Descripción** Devuelve el primer documento que se encuentra para cada valor único del campo especificado.

**Ejemplo**

	Type=Event | sort TimeGenerated DESC | Dedup EventID

El ejemplo anterior devuelve un evento (el último porque usamos DESC en TimeGenerated) por EventID.


### Extend

**Descripción** Permite crear campos de tiempo de ejecución en consultas.

**Ejemplo 1**

	Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Muestra una puntuación de recomendación ponderada para las recomendaciones de evaluación de SQL.

**Ejemplo 2**

	Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Muestra el valor del contador en kilobytes en lugar de bytes.

**Ejemplo 3**

	Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Escala el valor TotalBytes de WireData de forma que todos los resultados se encuentren entre 0 y 100.

**Ejemplo 4**

	Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
Etiqueta los valores de contadores de rendimiento inferiores a 50 % como LOW (bajo) y el resto como HIGH (alto).

**Funciones admitidas**


|Función |Descripción |Ejemplos de sintaxis|  
|---------|---------|---------|
|abs | Devuelve el valor absoluto de la función o el valor especificados.|<p>abs(x)</p> <p>abs(-5)</p>|
|y | Devuelve un valor True solamente si todos sus operandos se evalúan como True. | and(not(exists(**popularity**)),exists(**price**)):|
|def | def es la forma abreviada de "default" o predeterminado. Devuelve el valor del campo "field" o, si el campo no existe, devuelve el valor predeterminado especificado y proporciona el primer valor donde: exists()==true. |<p>div(1,y)</p><p>div(sum(x,100),max(y,1))</p>|
| div | div(x,y) divide x entre y. | div(1,y),div(sum(x,100),max(y,1)) |
| dist | Devuelve la distancia entre dos vectores (puntos) en un espacio de n dimensiones. Eleva la potencia, más dos o más, de las instancias de ValueSource y calcula las distancias entre los dos vectores. Cada elemento ValueSource debe ser un número. Se debe pasar un número par de instancias de ValueSource y el método supone que la primera mitad representa el primer vector y la segunda mitad, el segundo vector. | <p>dist(2, x, y, 0, 0): calcula la distancia euclidiana entre (0,0) y (x,y) para cada documento.</p><p>dist(1, x, y, 0, 0): calcula la distancia Manhattan (taxicab) entre (0,0) y (x,y) para cada documento.</p><p>dist(2,x,y,0,0,0): calcula la distancia euclidiana entre (0,0,0) y (x,y,z) para cada documento.</p><p>dist(1,x,y,z,e,f,g): distancia Manhattan entre (x,y,z) y (e,f,g), donde cada letra es un nombre de campo.</p> |
| exists | Devuelve TRUE si existe algún miembro del campo. | <p>exists(author) devuelve TRUE para cualquier documento que tenga un valor en el campo "author".</p><p>exists(query(price:5.00)) devuelve TRUE si "price" coincide con "5.00".</p> |
| hsin | La distancia Haversine calcula la distancia entre dos puntos en una esfera cuando recorre la esfera. Los valores deben estar en radianes. hsin también toma un argumento booleano para especificar si la función debe convertir su salida en radianes. | hsin(2, true, x, y, 0, 0) |
| if | Permite realizar consultas de funciones condicionales. En if(test,value1,value2): test es o se refiere a un valor lógico o una expresión que devuelve un valor lógico (TRUE o FALSE). · value1 es el valor devuelto por la función si test devuelve TRUE. · value2 es el valor devuelto por la función si test devuelve FALSE. Una expresión puede ser cualquier función que genera valores booleanos o incluso funciones que devuelven valores numéricos (en cuyo caso el valor 0 se interpretará como false) o que devuelven cadenas (en cuyo caso una cadena vacía se interpretará como false). | if(termfreq(cat,'electronics'),popularity,42): esta función comprueba cada documento para ver si contiene el término "electronics" en el campo cat. En caso afirmativo, se devuelve el valor del campo popularity; en caso contrario, se devuelve el valor 42. |
| linear | Implementa m*x+c, donde m y c son constantes y x es una función arbitraria. Es equivalente a sum(product(m,x),c), pero algo más eficaz ya que se implementa como una única función. | linear(x,m,c) linear(x,2,4) devuelve 2*x+4</font? |
| log | Devuelve el logaritmo en base 10 de la función especificada. | log(x) log(sum(x,100)) |
| map | Asigna al destino especificado los valores de una función de entrada x que se encuentran entre los valores min y max, ambos inclusive. Los argumentos min y max deben ser constantes. Los argumentos target y default pueden ser constantes o funciones. Si el valor de x no se encuentra entre min y max, se devuelve el valor de x, o bien un valor predeterminado si se especifica como un quinto argumento. | <p>map(x,min,max,target) map(x,0,0,1): cambia cualquier valor de 0 a 1. Esto puede ser útil para controlar los valores 0 predeterminados.</p> <p> map(x,min,max,target,default) map(x,0,100,1,-1): cambia cualquier valor entre 0 y 100 a 1, y todos los demás valores a -1.</p> </p> map(x,0,100,sum(x,599),docfreq(text,solr)): cambia los valores entre 0 y 100 a x+599, y todos los demás valores a la frecuencia del término 'solr' en el campo text.</p> |
| max | Devuelve el valor numérico máximo de varias funciones anidadas o constantes, que se especifican como argumentos: max(x,y,…). La función max también puede ser útil para "descender" otra función o campo a alguna constante especificada. (Use la sintaxis field(myfield,max) para seleccionar el valor máximo de un único campo multivalor). | max(myfield,myotherfield,0) |
| min | Devuelve el valor numérico mínimo de varias funciones anidadas o constantes, que se especifican como argumentos: min(x,y,…). La función min también puede ser útil para proporcionar un "límite superior" en una función mediante una constante. (Use la sintaxis field(myfield,min) para seleccionar el valor mínimo de un único campo multivalor). | min(myfield,myotherfield,0) |
| ms | Devuelve los milisegundos de diferencia entre sus argumentos. Las fechas son relativas a la época temporal de Unix o POSIX, media noche, 1 de enero de 1970 UTC. Los argumentos pueden ser el nombre de un campo TrieDateField indexado o matemáticas de fecha en función de una fecha constante o NOW. · ms(): equivalente a ms(NOW), el número de milisegundos desde la época. · ms(a): devuelve el número de milisegundos desde la época que representa el argumento. · ms(a,b): devuelve el número de milisegundos en los que b ocurre antes que a (es decir, a - b). | <p> </p><p> ms(NOW/DAY) </p> <p> ms(2000-01-01T00:00:00Z)</p> <p> ms(mydatefield)</p> <p> ms(NOW,mydatefield)</p> <p> ms(mydatefield,2000-01-01T00:00:00Z)</p> <p> ms(datefield1,datefield2)</p> |
| not | El valor negado lógicamente de la función ajustada. | not(exists(author)): TRUE solo cuando exists(author) es false. |
| o | Una disyunción lógica. | or(value1,value2): TRUE si value1 o value2 son true. |
| pow | Eleva la base especificada a la potencia especificada. pow(x,y)</font eleva x a la potencia de y. | <p> pow(x,y)</p> <p>pow(x,log(y))</p> <p> pow(x,0.5)</p>: igual que sqrt. |
| product | Devuelve el producto de varios valores o funciones, que se especifican en una lista separada por comas. mul(…) también puede utilizarse como alias para esta función. | <p> product(x,y,…)</p> <p>product(x,2)</p> <p> product(x,y)</p> <p>mul(x,y)</p> |
| recip | Realiza una función recíproca con `recip(x,m,a,b)` implementando `<a/(m*x+b)`, donde m,a,b son constantes y x es cualquier función arbitrariamente compleja. Cuando a y b son iguales y x>=0, esta función tiene un valor máximo de 1 que se reduce a medida que x aumenta. Si se aumenta el valor de a y b a la vez, da como resultado un movimiento de toda la función a una parte más plana de la curva. Estas propiedades pueden convertirla en una función idónea para aumentar los documentos más recientes, donde x es "rord(datefield)". | <p>recip(myfield,m,a,b)<p> <p> recip(rord(creationDate),1,1000,1000)</p> |
| scale | Escala los valores de la función x, como los que se encuentran entre los valores especificados de minTarget y maxTarget, ambos inclusive. La implementación actual recorre todos los valores de la función para obtener los valores min y max, para poder elegir la escala correcta. La implementación actual no puede distinguir si los documentos se han eliminado o si no tienen ningún valor. Utiliza valores 0,0 para estos casos. Esto significa que, si normalmente todos los valores son mayores que 0,0, todavía se puede acabar con 0,0 como valor mínimo desde donde asignar. En estos casos, se podría usar una función map() adecuada como solución alternativa para cambiar 0,0 a un valor en el intervalo real, como se muestra aquí: scale(map(x,0,0,5),1,2). | <p>scale(x,minTarget,maxTarget)</p> <p>scale(x,1,2): escala los valores de x, de tal forma que todos los valores estarán entre 1 y 2, ambos inclusive. </p> |
| sqedist | La distancia euclidiana cuadrática calcula la norma 2 (distancia euclidiana), pero no toma la raíz cuadrada, con lo que evita una operación bastante costosa. Suele ocurrir que las aplicaciones que consideran la distancia euclidiana no necesitan la distancia real, pero en cambio pueden utilizar el cuadrado de la distancia. Se debe pasar un número par de instancias de ValueSource y el método supone que la primera mitad representa el primer vector y la segunda mitad, el segundo vector. | sqedist(x\_td, y\_td, 0, 0) |
| sqrt | Devuelve la raíz cuadrada de la función o el valor especificado. | <p>sqrt(x)</p><p>sqrt(100)</p><p>sqrt(sum(x,100))</p> |
| strdist | Calcula la distancia entre dos cadenas. Utiliza la interfaz StringDistance del corrector ortográfico Lucene y admite todas las implementaciones disponibles en ese paquete; además, permite a las aplicaciones utilizar sus propias funcionalidades de carga de recursos mediante Solr. strdist toma (string1, string2, medida de la distancia). Los posibles valores para la medida de la distancia son jw (Jaro-Winkler), edit (distancia Levenstein o de edición) y ngram (distancia NGramDistance). Si se especifica, también se puede pasar opcionalmente el tamaño ngram. El valor predeterminado es 2. FQN: nombre de clase completo para una implementación de la interfaz StringDistance. Debe tener un constructor no-arg. | strdist("SOLR",id,edit) |
| sub | Devuelve x-y de sub(x,y). | <p>sub(myfield,myfield2)</p> <p> sub(100,sqrt(myfield))</p> |
| sum | Devuelve la suma de varios valores o funciones, que se especifican en una lista separada por comas. add(…) también puede utilizarse como alias para esta función. | <p>sum(x,y,…)</p> <p>sum(x,1)</p> <p> sum(x,y)</p> <p> sum(sqrt(x),log(y),z,0.5)</p> <p> add(x,y)</p> |
| xor | otro, pero no ambos. | xor(field1,field2): devuelve TRUE si field1 o field2 es true; FALSE si ambos son true. |



## Campo de búsqueda y referencia de faceta

Al usar la búsqueda de registros para buscar datos, los resultados muestran diversos campos y facetas. Sin embargo, alguna información que vea podría no aparecer muy descriptiva. Puede usar la siguiente información para ayudarle a entender los resultados.



|Campo|Tipo de búsqueda|Descripción|
|---|---|---|
|TenantId|Todo|Se usa para datos de partición|
|TimeGenerated|Todo|Se usa para controlar la escala de tiempo y los selectores de tiempo (en la pantalla de búsqueda y en otras pantallas). Representa cuándo se generó la parte de los datos (normalmente en el agente). El tiempo se expresa en formato ISO y siempre es UTC. En el caso de "tipos" que se basan en la instrumentación existente (es decir, los eventos de un registro), suele ser el tiempo real con el que se ha registrado la entrada de registro, la línea o el registro. Para algunos de los otros tipos que se generan a través de módulos de administración o en la nube (es decir, recomendaciones, alertas, agente de actualización etc.) es la hora en que se recopiló este nuevo dato con una instantánea de una configuración de algún tipo o en que se produjo una alerta o recomendación basadas en ella.|
|EventId|Evento|EventID en el registro de eventos de Windows|
|EventLog|Evento|Registro de eventos donde Windows registró el evento|
|EventLevelName|Evento|Crítico/advertencia/información/éxito|
|EventLevel|Evento|Valor numérico para crítico/advertencia/información/éxito (use EventLevelName en su lugar para hacer más legibles y fáciles las consultas)|
|SourceSystem|Todo|Procedencia de los datos (en términos del modo "adjuntar" al servicio; es decir, Operations Manager, OMS (= los datos se generan en la nube), Almacenamiento de Azure (datos procedentes de WAD), etc.|
|ObjectName|PerfHourly|Nombre del objeto de rendimiento de Windows|
|InstanceName|PerfHourly|Nombre de instancia del contador de rendimiento de Windows|
|CounteName|PerfHourly|Nombre del contador de rendimiento de Windows|
|ObjectDisplayName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Nombre para mostrar del objeto dirigido por una regla de recopilación del rendimiento en Operations Manager, o el del objeto descubierto por Visión operativa, o con respecto al cual se generó la alerta|
|RootObjectName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Nombre para mostrar del elemento principal del elemento principal (en un relación de doble hospedaje: es decir, SqlDatabase hospedado por SqlInstance hospedado por un Equipo de Windows) del objeto dirigido por una regla de recopilación del rendimiento en Operations Manager, o el del objeto descubierto por Visión operativa, o con respecto al cual se generó la alerta|
|Equipo|La mayoría de tipos|Nombre del equipo al que pertenecen los datos|
|DeviceName|ProtectionStatus|Nombre del equipo al que pertenecen los datos (igual que 'Equipo')|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|La categoría de estado de amenaza es una representación numérica del estado de amenaza y se parece a los códigos de respuesta HTTP; hemos dejado espacios entre los números (razón de que ninguna amenaza sea 150 y no 100 o 0) a fin de tener algo de espacio para agregar nuevos estados. Cuando hacemos un resumen del estado de amenaza y del estado de protección, queremos mostrar el peor estado en que ha estado el equipo durante el período de tiempo seleccionado. Usamos los números para clasificar los diferentes estados de manera que podamos buscar el registro con el número más alto.|
|ThreatStatus|ProtectionStatus|Descripción de ThreatStatus, asigna 1:1 con ThreatStatusRank|
|TypeofProtection|ProtectionStatus|Producto antimalware detectado en el equipo: ninguno, herramienta de eliminación de malware de Microsoft, Forefront, etc.|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus, RequiredUpdate|Identificador de HealthService para el agente de este equipo|
|HealthServiceId|La mayoría de tipos|Identificador de HealthService para el agente de este equipo|
|ManagementGroupName|La mayoría de tipos|Nombre del grupo de administración para los agentes conectados a Operations Manager; en caso contrario, será null o en blanco|
|ObjectType|ConfigurationObject|El tipo (como en la clase o el "tipo" del módulo de administración de Operations Manager) para este objeto descubierto en la evaluación de la configuración de Log Analytics|
|UpdateTitle|RequiredUpdate|Nombre de la actualización que se encontró que no estaba instalada|
|PublishDate|RequiredUpdate|¿Cuándo fue publicada la actualización en Microsoft Update?|
|Server|RequiredUpdate|Nombre del equipo al que pertenecen los datos (igual que 'Equipo')|
|Producto|RequiredUpdate|Producto al que se aplica la actualización|
|UpdateClassification|RequiredUpdate|Tipo de actualización (paquete acumulativo de actualizaciones, Service Pack, etc.)|
|KBID|RequiredUpdate|Id. del artículo de KB que describe este procedimiento recomendado o esta actualización|
|WorkflowName|ConfigurationAlert|Nombre de la regla o el monitor que generó la alerta|
|Gravedad|ConfigurationAlert|Gravedad de la alerta|
|Prioridad|ConfigurationAlert|Prioridad de la alerta|
|IsMonitorAlert|ConfigurationAlert|¿Esta alerta la ha generado un monitor (true) o una regla (false)?|
|AlertParameters|ConfigurationAlert|XML con los parámetros de la alerta de Log Analytics|
|Context|ConfigurationAlert|XML con el "contexto" de la alerta de Log Analytics|
|Carga de trabajo|ConfigurationAlert|Tecnología o 'carga de trabajo' a la que hace referencia la alerta|
|AdvisorWorkload|Recomendación|Tecnología o 'carga de trabajo' a la que hace referencia la recomendación|
|Descripción|ConfigurationAlert|Descripción de la alerta (corta)|
|DaysSinceLastUpdate|UpdateAgent|¿Cuántos días hace (en relación con 'TimeGenerated' de este registro) que este agente instaló una actualización de WSUS/Microsoft Update?|
|DaysSinceLastUpdateBucket|UpdateAgent|Según DaysSinceLastUpdate, una categorización en 'ciclos' de cuánto tiempo hace que un equipo instaló por última vez una actualización de WSUS o Microsoft Update|
|AutomaticUpdateEnabled|UpdateAgent|¿Está habilitada o deshabilitada la comprobación automática de actualizaciones en este agente?
|AutomaticUpdateValue|UpdateAgent|¿Está configurada la actualización automática para descargar e instalar automáticamente, solo para descargar o solo para comprobar?|
|WindowsUpdateAgentVersion|UpdateAgent|Número de versión del agente de Microsoft Update|
|WSUSServer|UpdateAgent|¿Hacia qué servidor WSUS se dirige este agente de actualización?|
|OSVersion|UpdateAgent|La versión del sistema operativo en el que se ejecuta este agente de actualización|
|Nombre|Recommendation, ConfigurationObjectProperty|Título o nombre de la recomendación, o el nombre de la propiedad de evaluación de la configuración de Log Analytics|
|Valor|ConfigurationObjectProperty|Valor de una propiedad de evaluación de la configuración de Log Analytics|
|KBLink|Recomendación|Dirección URL del artículo de KB que describe este procedimiento recomendado o esta actualización|
|RecommendationStatus|Recomendación|Las recomendaciones se encuentran entre los pocos tipos cuyos registros se 'actualizan', no solo se agregan al índice de búsqueda. Este estado cambia si la recomendación está activa o abierta o si Log Analytics detecta que se ha resuelto.|
|RenderedDescription|Evento|Descripción representada (texto reutilizado con parámetros rellenados) de un evento de Windows|
|ParameterXml|Evento|XML con los parámetros de la sección "datos" de un evento de Windows (como se muestra en el Visor de eventos)|
|EventData|Evento|XML con la sección entera de "datos" de un evento de Windows (como se muestra en el Visor de eventos)|
|Origen|Evento|Origen del registro de eventos que generó el evento|
|EventCategory|Evento|Categoría del evento, directamente del registro de eventos de Windows|
|UserName|Evento|Nombre de usuario del evento de Windows (normalmente, NT AUTHORITY\\LOCALSYSTEM)|
|SampleValue|PerfHourly|Valor promedio de la agregación por hora de un contador de rendimiento|
|Min|PerfHourly|Valor mínimo en el intervalo por hora de un agregado por hora del contador de rendimiento|
|Max|PerfHourly|Valor máximo en el intervalo por hora de un agregado por hora del contador de rendimiento|
|Percentile95|PerfHourly|El valor del percentil 95 en el intervalo por hora de un agregado por hora del contador de rendimiento|
|SampleCount|PerfHourly|El número de muestras del contador de rendimiento "sin procesar" que se usaron para producir este registro agregado por hora|
|Threat|ProtectionStatus|Nombre del malware encontrado|
|StorageAccount|W3CIISLog|Cuenta de almacenamiento de Azure desde la que se leyó el registro|
|AzureDeploymentID|W3CIISLog|Id. de implementación de Azure del servicio en la nube al que pertenece el registro|
|Rol|W3CIISLog|Rol del servicio en la nube de Azure al que pertenece el registro|
|RoleInstance|W3CIISLog|RoleInstance del rol de Azure al que pertenece el registro|
|sSiteName|W3CIISLog|Sitio web de IIS al que pertenece el registro (notación de metabase); el campo s-sitename en el registro original|
|sComputerName|W3CIISLog|El campo s-computername en el registro original|
|sIP|W3CIISLog|Dirección IP de servidor a la que se dirigió la solicitud HTTP. El campo s-ip en el registro original|
|csMethod|W3CIISLog|Método HTTP (GET, POST, etc.) usado por el cliente en la solicitud HTTP. El campo cs-method en el registro original|
|cIP|W3CIISLog|Dirección IP de cliente de la que procede la solicitud de HTTP. El campo c-ip en el registro original|
|csUserAgent|W3CIISLog|El agente de usuario HTTP declarado por el cliente (explorador o de otro modo). El campo cs-user-agent en el registro original|
|scStatus|W3CIISLog|Código de estado HTTP (200/403, 500/etc.) devuelto por el servidor al cliente. El campo cs-status en el registro original|
|TimeTaken|W3CIISLog|El tiempo que ha tardado en completarse la solicitud, expresado en milisegundos. El campo timetaken en el registro original|
|csUriStem|W3CIISLog|El Uri relativo (sin dirección host, es decir, '/buscar') que se solicitó. El campo cs-uristem en el registro original|
|csUriQuery|W3CIISLog|Consulta de URI. Las consultas URI son necesarias solo para páginas dinámicas, por ejemplo, páginas ASP, por lo que este campo normalmente contiene un guión para páginas estáticas.|
|sPort|W3CIISLog|Puerto de servidor al que se envió la solicitud HTTP (y en el que escucha IIS, ya que lo ha seleccionado)|
|csUserName|W3CIISLog|Nombre de usuario autenticado, si la solicitud está autenticada y no es anónima|
|csVersion|W3CIISLog|Versión del protocolo HTTP usado en la solicitud (es decir, 'HTTP/1.1')|
|csCookie|W3CIISLog|Información de cookies|
|csReferer|W3CIISLog|Sitio que el usuario visitó por última vez. Este sitio proporciona un vínculo al sitio actual.|
|csHost|W3CIISLog|Encabezado de host (es decir, 'www.mysite.com') que se ha solicitado|
|scSubStatus|W3CIISLog|Código de error de subestado|
|scWin32Status|W3CIISLog|Código de estado de Windows|
|csBytes|W3CIISLog|Bytes enviados en la solicitud del cliente al servidor|
|csBytes|W3CIISLog|Bytes devueltos en la respuesta del servidor al cliente|
|ConfigChangeType|ConfigurationChange|Tipo de cambio (WindowsServices/software/, etc.)|
|ChangeCategory|ConfigurationChange|Categoría del cambio (modificado/agregado/eliminado)|
|SoftwareType|ConfigurationChange|Tipo de software (actualización/aplicación)|
|SoftwareName|ConfigurationChange|Nombre del software (solo se aplica a los cambios de software)|
|Publicador|ConfigurationChange|Proveedor que publica el software (solo se aplica a los cambios de software)|
|SvcChangeType|ConfigurationChange|Tipo de cambio que se ha aplicado en un servicio de Windows (State/StartupType/Path/ServiceAccount); solo se aplica a los cambios de servicio de Windows|
|SvcDisplayName|ConfigurationChange|Nombre para mostrar del servicio que se cambió|
|SvcName|ConfigurationChange|Nombre del servicio que se cambió|
|SvcState|ConfigurationChange|El estado nuevo (actual) del servicio|
|SvcPreviousState|ConfigurationChange|Estado conocido anterior del servicio (solo es aplicable si cambia el estado del servicio)|
|SvcStartupType|ConfigurationChange|Tipo de inicio del servicio|
|SvcPreviousStartupType|ConfigurationChange|Tipo de inicio del servicio anterior (solo es aplicable si cambia el tipo de inicio del servicio)|
|SvcAccount|ConfigurationChange|Cuenta de servicio|
|SvcPreviousAccount|ConfigurationChange|Cuenta de servicio anterior (solo es aplicable si cambia la cuenta de servicio)|
|SvcPath|ConfigurationChange|Ruta de acceso al archivo ejecutable del servicio de Windows|
|SvcPreviousPath|ConfigurationChange|Ruta de acceso anterior del archivo ejecutable para el servicio de Windows (solo es aplicable si ha cambiado)|
|SvcDescription|ConfigurationChange|Descripción del servicio|
|Anterior|ConfigurationChange|Estado anterior de este software (instalado/no instalado/versión anterior)|
|Current|ConfigurationChange|Último estado este software (instalado/no instalado/versión anterior)|

## Pasos siguientes
Para más información acerca de las búsquedas de registros:

- Familiarícese con las [búsquedas de registros](log-analytics-log-searches.md) para ver información detallada recopilada por soluciones.
- Use [Custom fields in Log Analytics](log-analytics-custom-fields.md) (Campos personalizados en Log Analytics) para ampliar las búsquedas de registros.

<!---HONumber=AcomDC_0504_2016-->