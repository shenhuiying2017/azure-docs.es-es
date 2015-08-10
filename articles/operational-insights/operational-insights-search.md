<properties
   pageTitle="Búsqueda de datos en Visión operativa"
   description="Puede usar la búsqueda de registros en Visión operativa de Azure para encontrar los datos que busca."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />

# Búsqueda de datos en Visión operativa

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

La base de Visión operativa de Microsoft Azure es la característica de búsqueda de registros que permite combinar y correlacionar datos de equipo procedentes de varios orígenes en el entorno. Las soluciones también se basan en la búsqueda de registros con el objeto de proporcionarle métricas dinamizadas en torno a una área de problema determinada.

En la página Buscar, puede crear una consulta y luego, al buscar, puede filtrar los resultados mediante controles de faceta. También puede crear consultas avanzadas para transformar, filtrar y informar sobre sus resultados.

En la mayoría de las páginas de soluciones aparecen consultas de búsqueda comunes. Por toda la consola de Visión operativa, puede hacer clic en los iconos o explorar otros elementos para ver sus detalles mediante la búsqueda de registros.

En este tutorial, usaremos ejemplos para cubrir todos los aspectos básicos del uso de la búsqueda de registros.

Comenzaremos con ejemplos sencillos y prácticos y luego construiremos sobre ellos para que pueda comprender los casos de uso prácticos sobre cómo usar la sintaxis para extraer la información deseada de los datos.

Cuando realice búsquedas de registros en Visión operativa, empleará las siguientes técnicas:

- Uso de filtros básicos
- Uso de filtros adicionales
- Manipulación de los resultados de búsqueda
- Uso del comando measure
- Uso de las funciones max y min con el comando measure
- Uso de la función avg con el comando measure
- Uso del comando where

Cuando se haya familiarizado con las técnicas de búsqueda, puede revisar las secciones [Referencia de sintaxis de búsqueda](#search-syntax-reference) y [Campo de búsqueda y referencia de faceta](#search-field-and-facet-reference).

## Uso de filtros básicos

Lo primero que debe saber es que la primera parte de una consulta de búsqueda, antes de cualquier carácter de barra vertical "|", es siempre un *filtro*. Se puede considerar como una cláusula WHERE de TSQL en el sentido de que determina *qué* subconjunto de datos se va a extraer del almacén de datos de Visión operativa. La búsqueda desde un almacén de datos trata en gran medida sobre la especificación de las características de los datos que quiere extraer, por lo que es natural que una consulta comience con la cláusula WHERE.

Los filtros más básicos que puede usar son *palabras clave*, como 'error' o 'tiempo de espera', o un nombre de equipo. Estos tipos de consultas sencillas generalmente devuelven diversas formas de datos en el mismo conjunto de resultados. Este es el motivo de que Visión operativa tenga distintos *tipos* de datos en el sistema.


### Para llevar a cabo una búsqueda simple
1. En el portal de Visión operativa, haga clic en **Explorador de datos de búsqueda**. ![search tile](./media/operational-insights-search/overview-search.png)
2. En el campo de consulta, escriba `error` y luego haga clic en **Buscar**. ![search error](./media/operational-insights-search/search-error.png) Por ejemplo, la consulta de `error` en la siguiente imagen devolvió 100 000 registros de **Event** (recopilados por la administración de registros), 18 registros de **ConfigurationAlert** (generados por la evaluación de la configuración) y 12 registros de **ConfigurationChange** (capturados por el seguimiento de cambios). ![search results](./media/operational-insights-search/results01.png)

Estos filtros no son en realidad clases o tipos de objeto. *Type* es una etiqueta, una propiedad o una cadena/nombre/categoría, que se adjunta a una parte de los datos. Algunos documentos del sistema están etiquetados como **Type:ConfigurationAlert** y otros como **Type:PerfHourly** o **Type:Event**, y así sucesivamente. Cada resultado de búsqueda, documento, registro o entrada muestra todas las propiedades sin procesar y sus valores para cada uno de estas partes de los datos, y puede usar esos nombres de campo para especificar en el filtro cuándo quiere recuperar únicamente los registros donde el campo tiene ese valor dado.

*Type* es simplemente un campo que todos los registros tienen, no es diferente de ningún otro campo. Esto se estableció en función del valor del campo Type. Ese registro tendrá una forma diferente. Por cierto, **Type=PerfHourly** o **Type=Event** es también la sintaxis que debe conocer para consultar los agregados de datos o eventos de rendimiento por hora.

Puede usar un signo de dos puntos (:) o un signo igual (=) después del nombre de campo y antes del valor. **Type:Event** y **Type=Event** tienen un significado equivalente, así que puede elegir el estilo que prefiera.

Por lo tanto, si los registros Type=PerfHourly tienen un campo llamado 'CounterName', puede escribir entonces una consulta parecida a `Type=PerfHourly CounterName="% Processor Time"`.

Esta consulta solo le proporcionará los datos de rendimiento donde el nombre del contador de rendimiento sea "% de tiempo de procesador".

### Para buscar datos de rendimiento de tiempo de procesador
- En el campo de consulta de búsqueda, escriba `Type=PerfHourly CounterName="% Processor Time"`

También puede ser más específico y usar **InstanceName =\_'Total'** en la consulta, que es un contador de rendimiento de Windows. También puede seleccionar una faceta y otro **field:value**. El filtro se agrega automáticamente al filtro en la barra de consulta. Puede ver esto en la siguiente imagen. Se muestra dónde hacer clic para agregar **InstanceName:’\_Total’** a la consulta sin escribir nada.

![search facet](./media/operational-insights-search/search-facet.png)

La consulta ahora se convierte en `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`

En este ejemplo, no tiene que especificar **Type=PerfHourly** para llegar a este resultado. Dado que los campos CounterName y InstanceName solo existen para los registros de Type=PerfHourly, la consulta es lo suficientemente específica como para devolver los mismos resultados que la anterior, más larga: ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

Esto se debe a que todos los filtros de la consulta se evalúan como que están en *AND* con respecto al otro. Efectivamente, cuantos más campos agregue a los criterios, obtendrá resultados más o menos específicos y refinados.

Por ejemplo, la consulta `Type=Event EventLog="Windows PowerShell"` es idéntica a `Type=Event AND EventLog="Windows PowerShell"`. Devuelve todos los eventos que se registraron y se recopilaron del registro de eventos de Windows PowerShell. Si agrega un filtro varias veces seleccionando de manera repetida la misma faceta, entonces el problema es meramente cosmético; podría desordenar la barra de búsqueda, pero seguiría devolviendo los mismos resultados porque el operador AND implícito siempre está ahí.

Puede invertir fácilmente el operador AND implícito usando un operador NOT explícitamente. Por ejemplo:

`Type:Event NOT(EventLog:"Windows PowerShell")` o su equivalente `Type=Event EventLog!="Windows PowerShell"` devuelven todos los eventos de todos los demás registros que NO son el registro de Windows PowerShell.

O bien, puede usar otro operador booleano como 'OR'. La siguiente consulta devuelve registros en los que EventLog es Application OR System.

```
EventLog=Application OR EventLog=System
```

Con la consulta anterior, obtendrá entradas para ambos registros en el mismo conjunto de resultados.

Sin embargo, si quita el operador OR dejando en su lugar el operador AND implícito, entonces la siguiente consulta no producirá ningún resultado porque no hay una entrada de registro de eventos que pertenezca a AMBOS registros. Cada entrada de registro de eventos se escribió para solo uno de los dos registros.

```
EventLog=Application EventLog=System
```


## Uso de filtros adicionales

La siguiente consulta devuelve entradas de dos registros de eventos para todos los equipos que han enviado datos.

```
EventLog=Application OR EventLog=System
```

![search results](./media/operational-insights-search/search-results03.png)

Al seleccionar uno de los campos o filtros se limitará la consulta a un equipo específico, sin incluir todos los demás. La consulta resultante podría parecerse a la siguiente.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

La cual es equivalente a la siguiente debido al AND implícito.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Cada consulta se evalúa en el siguiente orden explícito. Observe los paréntesis.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Al igual que el campo de registro de eventos, solo puede recuperar datos de un conjunto de equipos específicos agregando OR. Por ejemplo:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Igualmente, la siguiente consulta devuelve el **% tiempo de CPU** solo para los dos equipos seleccionados.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Operadores booleanos
Con los campos de fecha y hora y numéricos, puede buscar valores mediante *mayor que*, *menor que* y *menor o igual que*. Puede usar operadores simples como >, < , >=, <= , !=, en la barra de búsqueda de consulta.


Puede consultar un registro de eventos específico para un período de tiempo específico. Por ejemplo, las últimas 24 horas se expresan con la siguiente expresión mnemotécnica.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Para realizar búsquedas mediante un operador booleano
- En el campo de consulta de búsqueda, escriba `EventLog=System TimeGenerated>NOW-24HOURS"` ![search with boolean](./media/operational-insights-search/search-boolean.png)

Aunque puede controlar el intervalo de tiempo gráficamente, y casi siempre es recomendable hacerlo, resulta beneficioso incluir un filtro de tiempo directamente en la consulta. Por ejemplo, esto funciona bien con paneles donde puede invalidar el tiempo de cada icono, con independencia del selector de tiempo *global* de la página del panel. Para obtener más información, consulte [Cuestiones sobre el tiempo en el panel](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Si desea filtrar por tiempo, tenga en cuenta que se obtienen resultados para la *intersección* de los dos períodos de tiempo: el especificado en el portal de Visión operativa (S1) y el especificado en la consulta (S2).

![intersection](./media/operational-insights-search/intersection.png)

Esto significa que, si los períodos de tiempo no se cruzan, por ejemplo en el portal de Visión operativa donde elige **esta semana** y en la consulta donde define **última semana**, entonces no hay ninguna intersección y no recibirá ningún resultado.

Los operadores de comparación usados con el campo TimeGenerated también son útiles en otras situaciones. Por ejemplo, con los campos numéricos.

Por ejemplo, dado que las alertas de evaluación de la configuración tienen los siguientes valores de gravedad:

- 0 = Información
- 1= Advertencia
- 2 = Crítico

Puede consultar las alertas de advertencia y críticas y también excluir las informativas con la siguiente consulta:

```
Type=ConfigurationAlert  Severity>=1
```


También puede usar consultas por rango. Esto significa que puede proporcionar el intervalo inicial y final de los valores de una secuencia. Por ejemplo, si desea los eventos del registro de eventos de Operations Manager en los que EventID sea mayor o igual que 2100 pero no superior a 2199, la siguiente consulta devolvería estos valores.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]La sintaxis de intervalo que debe usar es el separador dos puntos (:) field:value y *no* el signo igual (=). Incluya el extremo inferior y superior del intervalo entre corchetes y sepárelos con dos puntos (..).

## Manipulación de los resultados de búsqueda

Al buscar datos, querrá refinar la consulta de búsqueda y tener un buen nivel de control sobre los resultados. Cuando se recuperen los resultados, puede aplicar comandos para transformarlos.

Los comandos de las búsquedas de Visión operativa *deben* seguir al carácter de barra vertical (|). Un filtro debe ser siempre la primera parte de una cadena de consulta. Define el conjunto de datos con el que trabaja y luego "canaliza" esos resultados en un comando. Luego puede usar la canalización para agregar comandos adicionales. Es parecido a grandes rasgos a la canalización de Windows PowerShell.

En general, el lenguaje de búsqueda de Visión operativa intenta seguir el estilo y los criterios de PowerShell a fin de que sea familiar para los profesionales de TI y para facilitar la curva de aprendizaje.

Los comandos tienen nombres de verbos para que pueda saber fácilmente lo que hacen.

### Sort

El comando sort permite definir el criterio de ordenación por uno o varios campos. Aunque no lo use, se aplica de forma predeterminada un tiempo en orden descendente. Los resultados más recientes siempre están en la parte superior de los resultados de búsqueda. Esto significa que cuando ejecuta una búsqueda con `Type=Event EventID=1234`, lo que realmente se ejecuta es:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Esto se debe a que es el tipo de experiencia con el que está familiarizado en los registros. Por ejemplo, en el Visor de eventos de Windows.

Puede usar el comando Sort para cambiar la forma en que se devuelven los resultados. En los ejemplos siguientes se muestra cómo funciona este comando.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Los sencillos ejemplos anteriores muestran cómo funcionan los comandos; estos cambian la forma de los resultados que devuelve el filtro.

### Limit y Top
Otro comando menos conocido es LIMIT. Limit es un verbo al estilo de PowerShell. Es funcionalmente idéntico al comando TOP. Las consultas siguientes devuelven los mismos resultados.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Para realizar búsquedas con TOP
- En el campo de consulta de búsqueda, escriba `Type=Event EventID=2110 | Top 1` ![search top](./media/operational-insights-search/search-top.png)

En la imagen anterior, hay 988 registros con EventID=2110. Los campos, las facetas y los filtros de la izquierda siempre muestran información sobre los resultados devueltos *por la parte de filtro* de la consulta, que es la parte situada delante de cualquier carácter de barra vertical. El panel **Resultados** solo devuelve el resultado más reciente (1), porque el comando de ejemplo ha dado forma a los resultados y los ha transformado.

### Seleccionar

El comando SELECT se comporta como Select-Object en PowerShell. Devuelve los resultados filtrados que no tienen todas sus propiedades originales. En su lugar, selecciona solo las propiedades que especifique.

#### Para ejecutar una búsqueda con el comando Select

1. En el campo de búsqueda, escriba `Type=Event` y luego haga clic en **Buscar**.
2. Haga clic en **+ mostrar más** en uno de los resultados para ver todas las propiedades que tienen los resultados.
3. Seleccione algunas de ellas explícitamente y la consulta cambiará a `Type=Event | Select Computer,EventID,RenderedDescription`. ![search select](./media/operational-insights-search/search-select.png)

Se trata de un comando especialmente útil cuando desea controlar los resultados de búsqueda y elegir solo las partes de los datos que realmente importan para la exploración, que casi siempre no son el registro completo. También es útil cuando registros de diferentes tipos tienen *algunas* propiedades en común, pero no *todas*. Luego, puede generar resultados con la apariencia natural de una tabla, o que funcionan bien cuando se exportan a un archivo CSV y luego se transforman en Excel.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## Uso del comando measure

MEASURE es uno de los comandos más versátiles en las búsquedas de Visión operativa. Permite aplicar *funciones* estadísticas a los datos y agregar los resultados agrupados por un campo dado. Son muchas las funciones estadísticas que admite este comando.

### Measure count()

La primera función estadística con la que se trabaja y una de las más fáciles de entender es la función *count()*.

Los resultados de cualquier consulta de búsqueda como `Type=Event`, muestran filtros también llamados facetas en el lado izquierdo. Los filtros muestran una distribución de valores por un campo dado de los resultados de la búsqueda realizada.

![search measure count](./media/operational-insights-search/search-measure-count01.png)

Por ejemplo, en la imagen anterior verá el campo **Computer**, y muestra que dentro de los casi tres millones de eventos de los resultados, hay 20 valores únicos y distintos para ese campo en esos registros. El icono solo muestra los cinco principales, que son los cinco valores más comunes que están escritos en el campo **Computer**, ordenados por el número de documentos que contienen ese valor específico en ese campo. En la imagen puede ver que, entre esos casi tres millones de eventos, 880 mil proceden del equipo DM, 602 mil del equipo DE, etc.


¿Y si quisiera ver todos los valores, ya que el icono solo muestra los cinco primeros?

Eso es lo que puede hacer el comando measure con la función count(). Esta función no usa ningún parámetro. Usted solo especifica el campo por el que desea agrupar, en este caso, el campo **Computer**:

`Type=Event | Measure count() by Computer`

![search measure count](./media/operational-insights-search/search-measure-count-computer.png)

Sin embargo, **Computer** es simplemente un campo usado *en* cada parte de los datos; no hay ninguna base de datos relacional implicada y no hay ningún objeto **Computer** independiente en ninguna parte. Solo los valores *de* los datos pueden describir la entidad que los ha generado y otras características y aspectos de los datos, de ahí el término *faceta*. Sin embargo, también puede agrupar por otros campos. Dado que los resultados originales de casi tres millones de eventos que se canalizan al comando measure también tienen un campo denominado **EventID**, puede aplicar la misma técnica para agrupar por ese campo y obtener un recuento de eventos por EventID:

```
Type=Event | Measure count() by EventID
```

Si no está interesado en el número de registros real que contiene un valor específico, sino que solo quiere una lista de valores propiamente dichos, puede agregar un comando *Select* al final de la misma y seleccionar la primera columna:

```
Type=Event | Measure count() by EventID | Select EventID
```

Así puede obtener resultados más intrincados y ordenarlos previamente en la consulta, o también puede hacer simplemente clic en las columnas de la cuadrícula.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Para buscar mediante measure count

- En el campo de consulta de búsqueda, escriba `Type=Event | Measure count() by EventID`
- Anexe `| Select EventID` al final de la consulta.
- Finalmente, anexe `| Sort EventID asc` al final de la consulta.


Hay un par de cuestiones importantes que es necesario observar y resaltar:

En primer lugar, los resultados que ve ya no son los resultados sin formato originales. Son los resultados de agregado, básicamente grupos de resultados. Esto no supone un problema, pero debe comprender que está interactuando con una forma muy diferente de los datos que difiere de la forma sin formato original que se crea sobre la marcha como resultado de la función estadística o de agregación.

En segundo lugar, **Measure count** solo devuelve actualmente los 100 primeros resultados diferentes. Este límite no se aplica a las otras funciones estadísticas. Por lo tanto, normalmente deberá usar primero un filtro más preciso para buscar elementos específicos antes de aplicar measure count().

## Uso de las funciones max y min con el comando measure

Son varias las situaciones en las que **Measure Max()** y **Measure Min()** son útiles. Sin embargo, puesto que cada función es opuesta entre sí, ilustraremos Max() y usted puede experimentar con Min() por su cuenta.

Si consulta las alertas de evaluación de la configuración, tienen una propiedad **Severity** que puede ser 0, 1 o 2 según la alerta sea informativa, un mensaje de advertencia o crítica. Por ejemplo:

```
Type=ConfigurationAlert
```

![search measure count start](./media/operational-insights-search/search-measure-max01.png)

Si quiere ver el valor más alto de todas las alertas dado un equipo común, puede usar agrupar por campo.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![search measure max computer](./media/operational-insights-search/search-measure-max02.png)

Se mostrará que para los equipos que tenían registros **Alert**, la mayoría de ellos tienen al menos una alerta crítica, y el equipo Bacc tiene una advertencia como su peor gravedad.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![search measure max time generated computer](./media/operational-insights-search/search-measure-max03.png)

Esta función es muy adecuada para números, pero también para campos de fecha y hora. Resulta útil para comprobar la última marca de tiempo o la más reciente en cualquier parte de los datos indexados para cada equipo. Por ejemplo, ¿cuándo fue el cambio de configuración más reciente informado por la solución de seguimiento de cambios para cada máquina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Uso de la función avg con el comando measure

La función estadística Avg() usada con measure le permite calcular el valor medio de algún campo y agrupar los resultados por el mismo campo u otro diferente. Esto resulta útil en muchos casos, por ejemplo, con los datos de rendimiento.

Comenzaremos con los datos de rendimiento. Sin embargo, tenga en cuenta que, actualmente, Visión operativa solo recopila algunos contadores de rendimiento relacionados con tejidos para hosts de Hyper-V y Virtual Machine Manager como parte de la solución de administración de la capacidad.

Para buscar *todos* los datos de rendimiento, la consulta más básica es:

```
Type=PerfHourly
```

![search avg start](./media/operational-insights-search/search-avg01.png)

Lo primero que notará es que Visión operativa muestra los gráficos de los contadores de rendimiento. En la parte inferior de los resultados, verá los registros reales detrás de los gráficos.

![search avg start](./media/operational-insights-search/search-avg02.png)

En la imagen anterior, hay dos conjuntos de campos marcados que indican lo siguiente:

- el primer conjunto identifica el nombre del contador de rendimiento de Windows, el nombre del objeto y el nombre de instancia en el filtro de consulta. Estos son los campos que usará probablemente como facetas o filtros:
- **SampleValue** es el valor real del contador
- En la consulta, **Type=PerfHourly** es un agregado por hora
- **TimeGenerated** es 21:00, en formato de 24 horas. Es la agregación de ese período por hora de 20:00 a 21:00.
- **SampleCount** es la agregación, y se calcula con 12 muestras (una cada cinco minutos)
- Los valores **min**, **max** y **Percentile95** para el período por hora eran en este ejemplo para la memoria de una máquina virtual, 6144 (megabytes)
- **SampleValue** es un agregado por hora y se rellena con el *promedio* para el período por hora y es lo que se usa para trazar los gráficos de rendimiento

Después de leer sobre la forma de los registros PerfHourly y de haber leído sobre otras técnicas de búsqueda, puede usar measure Avg() para agregar este tipo de datos numéricos.

A continuación se muestra un sencillo ejemplo:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![search avg samplevalue](./media/operational-insights-search/search-avg03.png)

En este ejemplo, selecciona el contador de rendimiento de tiempo total de la CPU y el promedio por equipo. Dado que **SampleValue** ya es un promedio, en realidad consulta un promedio de un promedio. En este punto, eso es correcto con Type=PerfHourly. Siempre debe usar un filtro en TimeGenerated para restringir la operación a un conjunto de datos pequeño o reciente, como las últimas cuatro horas, no siete días.

Así que la consulta anterior se convierte en:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Para buscar con la función avg con el comando measure
1. En el campo de consulta de búsqueda, escriba `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`.
2. Observe que el promedio reciente suele ser más alto.
3. Calcule el promedio de los valores por hora máximos mediante la revisión de la consulta de búsqueda con `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer`

Puede agregar y correlacionar los datos *entre* equipos. Por ejemplo, imagine que tiene un conjunto de hosts en algún tipo de granja de servidores donde cada nodo es igual a otro y todos hacen el mismo tipo de trabajo, y la carga se debe equilibrar de manera aproximada. Podría obtener sus contadores en un solo paso con la siguiente consulta y conseguir los promedios para toda la granja. Puede comenzar eligiendo los equipos con el ejemplo siguiente:

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Ahora que tiene los equipos, solo quiere seleccionar dos indicadores clave de rendimiento (KPI): % de uso de la CPU y % de espacio libre en disco. Por lo tanto, esa parte de la consulta se convierte en:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Ahora puede agregar equipos y contadores con el ejemplo siguiente:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Como tiene una selección muy específica, el comando **measure Avg()** puede devolver el promedio no por equipo, sino en la granja de servidores, con solo agrupar por CounterName. Por ejemplo:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

Esto proporciona una vista útil compacta de un par de KPI de su entorno.

![search avg grouping](./media/operational-insights-search/search-avg04.png)


Puede usar fácilmente esto en un panel. Para obtener más información sobre el uso de los panales, consulte [Paneles de Visión operativa](operational-insights-use-dashboards).

![search avg dashboard](./media/operational-insights-search/search-avg05.png)

### Uso de la función sum con el comando measure

La función sum es similar a otras funciones del comando measure. Puede ver un ejemplo sobre cómo usar la función sum en [Búsqueda de registros de W3C IIS en Visión operativa de Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Puede usar Max() y Min() con cadenas de texto, números, fechas y horas. Con cadenas de texto, se ordenan alfabéticamente y obtiene la primera y la última.

Sin embargo, no puede usar Sum() con campos que no sean numéricos. Esto también se aplica a Avg().

## Uso del comando where

El comando where funciona como un filtro, pero se puede aplicar en la canalización para filtrar aún más los resultados agregados producidos por un comando measure, por oposición a los resultados sin formato que se filtran al principio de una consulta.

Por ejemplo:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

Puede agregar otro carácter de barra vertical "|" y el comando where para obtener solo los equipos cuyo promedio de CPU sea superior al 80 %, con el siguiente ejemplo:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Si está familiarizado con Microsoft System Center Operations Manager, puede considerar el comando where en términos de módulo de administración. Si el ejemplo fuera una regla, la primera parte de la consulta sería el origen de datos y el comando where sería la detección de condición.

Puede usar la consulta como icono en **Mi panel**, como monitor de ordenaciones, para ver cuándo las CPU de equipo se sobreutilizan. Para obtener más información sobre los panales, consulte [Paneles de Visión operativa](operational-insights-use-dashboards). También puede crear y usar paneles mediante la aplicación móvil. Para obtener más información, consulte [Aplicación móvil de Visión operativa de Azure ](http://www.windowsphone.com/es-es/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). En los dos iconos inferiores de la siguiente imagen, puede ver que el monitor se muestra como una lista y como un número. Básicamente, desea siempre que el número sea cero y la lista esté vacía. De lo contrario, indica una condición de alerta. Si es necesario, puede usarlo para ver qué máquinas están bajo presión.

![mobile dashboard](./media/operational-insights-search/search-mobile.png)

## Referencia de la sintaxis de búsqueda

En la siguiente sección de referencia sobre el lenguaje de búsqueda se describen las opciones generales de sintaxis de consulta que puede usar al buscar datos y filtrar expresiones para ayudar a limitar la búsqueda. También se describen los comandos que puede usar para realizar acciones en los datos recuperados.

Puede leer sobre los campos devueltos en las búsquedas y las facetas que le ayudarán a explorar categorías similares de datos en [Campo de búsqueda y referencia de faceta](#Search-field-and-facet-reference).

### Sintaxis de consulta general

Sintaxis:

filterExpression | command1 | command2 …

La expresión de filtro (**filterExpression**) define la condición "where" para la consulta. Los comandos se aplican a los resultados devueltos por la consulta. Varios comandos deben separarse con el carácter de barra vertical ( | ).

#### Ejemplos de sintaxis general

Ejemplos:

	system

Esta consulta devuelve resultados que contienen la palabra "system" en cualquier campo que se ha indexado para la búsqueda de términos o texto completo.

>[AZURE.NOTE]No todos los campos se indexan de este modo, pero los campos de texto más comunes (como nombres y descripciones) normalmente sí.

	system error

Esta consulta devuelve resultados que contienen las palabras "system" y "error".

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

Esta consulta devuelve resultados que contienen las palabras "system" y "error". Luego, ordena los resultados por el campo **ManagementGroupName** (en orden ascendente) y luego por **TimeGenerated** (en orden descendente). Solo toma los 10 primeros resultados.

>[AZURE.IMPORTANT]Todos los nombres de campo y los valores de los campos de cadena y texto distinguen mayúsculas de minúsculas.

### Expresión de filtro

En las subsecciones siguientes se explican las expresiones de filtro.

#### Literales de cadena

Un literal de cadena es cualquier cadena que no reconoce el analizador como una palabra clave o un tipo de dato predefinido (por ejemplo, un número o una fecha).

Ejemplos:

	These all are string literals


Esta consulta busca los resultados que contienen las apariciones de las cinco palabras. Para realizar una búsqueda de cadena compleja, incluya el literal de cadena entre comillas, por ejemplo:

	" Windows Server"

Esto solo devuelve resultados con coincidencias exactas de "Windows Server".

#### Números

El analizador admite la sintaxis de entero decimal y punto flotante para campos numéricos.

Ejemplos:

	Type:PerfHourly 0.5


	HTTP 500

#### Fecha y hora

Cada parte de los datos del sistema tiene una propiedad **TimeGenerated**, que representa la fecha y la hora originales del registro. Algunos tipos de datos pueden tener además más campos de fecha y hora (por ejemplo, **LastModified**).

El selector de gráfico y hora de escala de tiempo de Visión operativa muestra una distribución de los resultados en el tiempo (según la consulta actual que se ejecuta), basada en el campo **TimeGenerated**. Los campos de fecha y hora tienen un formato de cadena específico que puede usarse en las consultas para restringirlas a un período de tiempo concreto. También puede usar la sintaxis para hacer referencia a intervalos de tiempo relativos (por ejemplo, "entre hace tres días y hace dos horas").

Sintaxis:

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



Ejemplo:

	TimeGenerated:2013-10-01T12:20

El comando anterior solo devuelve los registros con un valor **TimeGenerated** de exactamente 12:20 el 1 de octubre de 2013. No es probable que proporcione ningún resultado, pero puede hacerse una idea.

El analizador admite también el valor de fecha y hora mnemotécnico, NOW.

Ejemplo:


De nuevo, es poco probable que esto produzca ningún resultado porque los datos no pasan por el sistema tan rápido.

Estos ejemplos son bloques de creación que se usan para fechas relativas y absolutas. En las siguientes tres subsecciones, explicaremos cómo usarlos en filtros más avanzados con ejemplos que emplean intervalos de fechas relativas.

#### Operadores matemáticos Date/Time

Use los operadores matemáticos Date/Time para desplazar o redondear el valor de fecha y hora mediante el uso de cálculos de fecha y hora sencillos.

Sintaxis:

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>Operador</th>
		<th>Descripción</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>Redondea la fecha y hora a la unidad especificada. </p>
		<p>Ejemplo: NOW/DAY redondea la fecha y hora actuales a la medianoche del día actual. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ o -</p>
		</td>
		<td>
		<p>Desplaza la fecha y hora el número especificado de unidades</p>
		<p>Ejemplos:&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR desplaza la fecha y hora actuales una hora hacia adelante.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS desplaza el valor de fecha 10 días atrás.</li>
		</ul>
		</td>
	</tr>
</table>





Puede encadenar los operadores matemáticos Date/Time juntos, por ejemplo:

	NOW+1HOUR-10MONTHS/MINUTE

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


#### Facetas de campo

Mediante el uso de facetas de campo, puede especificar la condición de búsqueda para campos específicos y sus valores exactos, en lugar de escribir consultas de "texto libre" para varios términos en todo el índice. Ya hemos usado esta sintaxis en varios ejemplos en los párrafos anteriores. En este caso, proporcionamos ejemplos más complejos.

**Sintaxis**

*field:value*

*field=value*

**Descripción**

Busca en el campo el valor específico. El valor puede ser un literal de cadena, un número o una fecha u hora.

Ejemplo:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**Sintaxis**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**Descripción**

Proporciona comparaciones.

Ejemplo:

	TimeGenerated>NOW+2HOURS


**Sintaxis**

*field:[from..to]*

**Descripción**

Proporciona facetas de intervalo.

Ejemplo:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### Operadores lógicos

Los lenguajes de consulta admiten los operadores lógicos (AND, OR y NOT) y sus alias de estilo C (&&, || y !) respectivamente. Puede usar paréntesis para agrupar estos operadores.

Ejemplos:

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
Puede omitir el operador lógico para los argumentos de filtro de nivel superior. En este caso, se supone el operador AND.


Expresión de filtro|Equivalente a
---|---
error del sistema|sistema Y error
sistema "Windows Server" O gravedad:1|sistema Y "Windows Server" O gravedad:1



### Comandos:

Los comandos se aplican a los resultados devueltos por la consulta. Use el carácter de barra vertical ( | ) para aplicar un comando a los resultados recuperados. Varios comandos deben separarse con el carácter de barra vertical ( | ).

>[AZURE.NOTE]Los nombres de los comandos pueden escribirse en mayúsculas o minúsculas, a diferencia de los nombres de campo y los datos.

#### Sort

Sintaxis:

	sort field1 asc|desc, field2 asc|desc, …

Ordena los resultados por campos concretos. El prefijo asc o desc es opcional. Si se deben omitir, se supone el criterio de ordenación "asc". Si una consulta no usa el comando **Sort** explícitamente, Sort **TimeGenerated** desc es el comportamiento predeterminado y siempre devolverá los resultados más recientes en primer lugar.

#### Top/Limit

Sintaxis:

	top number


	limit number
Limita la respuesta a los N resultados principales.

Ejemplo:

	Type:Alert errors detected | top 10

Devuelve los 10 resultados coincidentes principales.

#### Skip

Sintaxis:

	skip number

Omite el número de resultados que se enumeran.

Ejemplo:

	Type:Alert errors detected | top 10 | skip 200

Devuelve los 10 resultados principales coincidentes empezando por el resultado 200.

#### Seleccionar

Sintaxis:

	select field1, field2, ...

Limita los resultados a los campos elegidos.

Ejemplo:

	Type:Alert errors detected | select Name, Severity

Limita los campos de resultados devueltos a **Name** y **Severity**.

#### Measure

El comando **measure** se usa para aplicar funciones estadísticas a los resultados de búsqueda sin procesar. Esto resulta muy útil para obtener vistas *group-by* sobre los datos. Cuando se usa el comando **measure**, Visión operativa muestra una tabla con resultados agregados.

Sintaxis:

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

Agrega los resultados por **groupField** y calcula los valores de medida agregados mediante el uso de **aggregatedField**.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Función estadística measure </th> <th>Descripción </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>El nombre de la función de agregado (no distingue mayúsculas de minúsculas). Las siguientes funciones de agregado no se admiten :</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>El campo que se agrega. Este campo es opcional para la función de agregado COUNT, pero debe ser un campo numérico existente para SUM, MAX, MIN, AVG o STDDEV.</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>El alias (opcional) para el valor agregado calculado. Si no se especifica, el nombre del campo será <em>AggregatedValue.</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>El nombre del campo por el que se agrupa el conjunto de resultados. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>El intervalo de tiempo en el formato: </p> <p><em>nnnNAME</em> </p> <p></p> <p>Donde: </p> <p>nnn es el número entero positivo</p> <p><em>NAME</em> es el nombre del intervalo</p> <p>Nombres de intervalo admitidos son (distinguen mayúsculas de minúsculas): </p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



La opción de intervalo solo se puede usar en los campos de grupo de fecha y hora (como **TimeGenerated** y **TimeCreated**). Actualmente, el servicio no aplica esta directriz, pero un campo sin fecha y hora que se pasa al back-end producirá un error en tiempo de ejecución. Cuando se implementa la validación de esquema, la API del servicio rechaza las consultas que usan campos sin fecha y hora para la agregación de intervalo. La implementación actual de **measure** admite la agrupación de intervalos solo para la función de agregado **Count**.

Si se omite la cláusula BY, pero se especifica un intervalo (como una segunda sintaxis), el campo **TimeGenerated** se asume de forma predeterminada.

Ejemplos:

**Ejemplo 1**

	Type:Alert | measure count() as Count by ObjectId

*Explicación*

Agrupa las alertas por **ObjectID** y calcula el número de alertas para cada grupo. El valor agregado se devuelve como el campo **Count** (alias).

**Ejemplo 2**

	Type:Alert | measure count() interval 1HOUR

*Explicación*

Agrupa las alertas por intervalos de una hora mediante el uso del campo **TimeGenerated** y devuelve el número de alertas en cada intervalo.

**Ejemplo 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explicación*

Igual que el ejemplo anterior, pero con un alias de campo agregado (**AlertsPerHour**).

**Ejemplo 4**

	* | measure count() by TimeCreated interval 5DAYS

*Explicación*

Agrupa los resultados por intervalos de cinco días mediante el uso del campo **TimeCreated** y devuelve el número de resultados en cada intervalo.

**Ejemplo 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Explicación*

Agrupa las alertas por nombre de carga de trabajo y devuelve el valor máximo de gravedad de alerta para cada flujo de trabajo.

**Ejemplo 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Explicación*

Igual que el ejemplo anterior, pero con la función agregada **Min**.

**Ejemplo 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*Explicación*

Agrupa PerfHourly por ObjectId y calcula el promedio (avg).

**Ejemplo 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*Explicación*

Igual que el ejemplo anterior, pero usa **Sum**.

**Ejemplo 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*Explicación*

Igual que el ejemplo anterior, pero usa **STDDEV**.

**Ejemplo 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explicación*

Obtiene los cinco principales flujos de trabajo con el número máximo de alertas.

#### Where

Sintaxis:

**where** AggregatedValue>20

Solo puede usarse después de un comando **Measure** para seguir filtrando los resultados agregados que ha producido la función de agregación **Measure**.

Ejemplos:

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## Campo de búsqueda y referencia de faceta

Al usar Buscar para buscar datos, los resultados muestran varios campos y facetas. Sin embargo, alguna información que vea podría no aparecer muy descriptiva. Puede usar la siguiente información para ayudarle a entender los resultados.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>Campo</b></th>
		<th><b>Tipo de búsqueda<b></th>
		<th><b>Descripción</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>Todo</p>
		</td>
		<td>
		<p>Se usa para datos de partición</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>Todo</p>
		</td>
		<td>
		<p>Se usa para controlar la escala de tiempo y los selectores de tiempo (en la pantalla de búsqueda y en otras pantallas). Representa cuándo se generó la parte de los datos (normalmente en el agente). El tiempo se expresa en formato ISO y siempre es UTC. En el caso de "tipos" que se basan en la instrumentación existente (es decir, los eventos de un registro), suele ser el tiempo real con el que se ha registrado la entrada de registro, la línea o el registro; para algunos de los otros tipos que se generan a través de módulos de administración o en la nube, es decir, recomendaciones, alertas, agente de actualización etc., es la hora en que se recopiló esta nueva parte de los datos con una instantánea de una configuración de algún tipo o en que se produjo una alerta o recomendación basadas en ella.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventId</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>EventID en el registro de eventos de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Registro de eventos donde Windows registró el evento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Crítico/advertencia/información/éxito</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Valor numérico para crítico/advertencia/información/éxito (use EventLevelName en su lugar para hacer más legibles y fáciles las consultas)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>Todo</p>
		</td>
		<td>
		<p>Procedencia de los datos (en términos del modo "adjuntar" al servicio; es decir, Operations Manager, Visión operativa (= los datos se generan en la nube), Almacenamiento de Azure (datos procedentes de WAD), etc.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nombre del objeto de rendimiento de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nombre de instancia del contador de rendimiento de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nombre del contador de rendimiento de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nombre para mostrar del objeto dirigido por una regla de recopilación del rendimiento en Operations Manager, o el del objeto descubierto por Visión operativa, o con respecto al cual se generó la alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nombre para mostrar del elemento principal del elemento principal (en un relación de doble hospedaje: es decir, SqlDatabase hospedado por SqlInstance hospedado por un Equipo de Windows) del objeto dirigido por una regla de recopilación del rendimiento en Operations Manager, o el del objeto descubierto por Visión operativa, o con respecto al cual se generó la alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Equipo</p>
		</td>
		<td>
		<p>La mayoría de tipos </p>
		</td>
		<td>
		<p>Nombre del equipo al que pertenecen los datos</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nombre del equipo al que pertenecen los datos (igual que 'Equipo')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>La categoría de estado de amenaza es una representación numérica del estado de amenaza y se parece a los códigos de respuesta HTTP; hemos dejado espacios entre los números (razón de que ninguna amenaza sea 150 y no 100 o 0) a fin de tener algo de espacio para agregar nuevos estados. Cuando hacemos un resumen del estado de amenaza y del estado de protección, queremos mostrar el peor estado en que ha estado el equipo durante el período de tiempo seleccionado. Usamos los números para clasificar los diferentes estados de manera que podamos buscar el registro con el número más alto.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Descripción de ThreatStatus, asigna 1:1 con ThreatStatusRank</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Producto antimalware detectado en el equipo: ninguno, herramienta de eliminación de malware de Microsoft, Forefront, etc.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>Identificador de HealthService para el agente de este equipo</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>La mayoría de tipos </p>
		</td>
		<td>
		<p>Identificador de HealthService para el agente de este equipo</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>La mayoría de tipos </p>
		</td>
		<td>
		<p>Nombre del grupo de administración para los agentes conectados a Operations Manager; en caso contrario, será null o en blanco</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>El tipo (como en la clase o el ’tipo’ del módulo de administración de Operations Manager) para este objeto descubierto en la evaluación de la configuración de Visión operativa</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nombre de la actualización que se encontró que no estaba instalada</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>¿Cuándo fue publicada la actualización en Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Server</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nombre del equipo al que pertenecen los datos (igual que 'Equipo')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Producto</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Producto al que se aplica la actualización</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Tipo de actualización (paquete acumulativo de actualizaciones, Service Pack, etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Id. del artículo de KB que describe este procedimiento recomendado o esta actualización</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Nombre de la regla o el monitor que generó la alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Gravedad</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Gravedad de la alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Prioridad</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Prioridad de la alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>¿Esta alerta la ha generado un monitor (true) o una regla (false)?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML con los parámetros de la alerta de Visión operativa</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML con el ’contexto’ de la alerta de Visión operativa</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Carga de trabajo</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Tecnología o 'carga de trabajo' a la que hace referencia la alerta </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>Recomendación</p>
		</td>
		<td>
		<p>Tecnología o 'carga de trabajo' a la que hace referencia la recomendación</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Descripción</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Descripción de la alerta (corta)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>¿Cuántos días hace (en relación con 'TimeGenerated' de este registro) que este agente instaló una actualización de WSUS/Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Según DaysSinceLastUpdate, una categorización en 'ciclos' de cuánto tiempo hace que un equipo instaló por última vez una actualización de WSUS o Microsoft Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>¿Está habilitada o deshabilitada la comprobación automática de actualizaciones en este agente?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>¿Está configurada la actualización automática para descargar e instalar automáticamente, solo para descargar o solo para comprobar?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Número de versión del agente de Microsoft Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>¿Hacia qué servidor WSUS se dirige este agente de actualización?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>La versión del sistema operativo en el que se ejecuta este agente de actualización</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Nombre</p>
		</td>
		<td>
		<p>Recommendation, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Título o nombre de la recomendación, o el nombre de la propiedad de evaluación de la configuración de Visión operativa</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Valor</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Valor de una propiedad de evaluación de la configuración de Visión operativa</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>Recomendación</p>
		</td>
		<td>
		<p>Dirección URL del artículo de KB que describe este procedimiento recomendado o esta actualización</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>Recomendación</p>
		</td>
		<td>
		<p>Las recomendaciones se encuentran entre los pocos tipos cuyos registros se 'actualizan', no solo se agregan al índice de búsqueda. Este estado cambia si la recomendación está activa o abierta o si Visión operativa detecta que se ha resuelto</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Descripción representada (texto reutilizado con parámetros rellenados) de un evento de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML con los parámetros de la sección "datos" de un evento de Windows (como se muestra en el Visor de eventos)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML con la sección entera de "datos" de un evento de Windows (como se muestra en el Visor de eventos)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Origen</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Origen del registro de eventos que generó el evento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Categoría del evento, directamente del registro de eventos de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Nombre de usuario del evento de Windows (normalmente, NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor promedio de la agregación por hora de un contador de rendimiento </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor mínimo en el intervalo por hora de un agregado por hora del contador de rendimiento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor máximo en el intervalo por hora de un agregado por hora del contador de rendimiento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>El valor del percentil 95 en el intervalo por hora de un agregado por hora del contador de rendimiento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>El número de muestras del contador de rendimiento "sin procesar" que se usaron para producir este registro agregado por hora</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Threat</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nombre del malware encontrado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Cuenta de almacenamiento de Azure desde la que se leyó el registro</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Id. de implementación de Azure del servicio en la nube al que pertenece el registro</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Rol</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Rol del servicio en la nube de Azure al que pertenece el registro</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>RoleInstance del rol de Azure al que pertenece el registro</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Sitio web de IIS al que pertenece el registro (notación de metabase); el campo s-sitename en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>El campo s-computername en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Dirección IP de servidor a la que se dirigió la solicitud HTTP. El campo s-ip en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Método HTTP (GET, POST, etc.) usado por el cliente en la solicitud HTTP. El campo cs-method en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Dirección IP de cliente de la que procede la solicitud de HTTP. El campo c-ip en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>El agente de usuario HTTP declarado por el cliente (explorador o de otro modo). El campo cs-user-agent en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de estado HTTP (200/403, 500/etc.) devuelto por el servidor al cliente. El campo cs-status en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>El tiempo que ha tardado en completarse la solicitud, expresado en milisegundos. El campo timetaken en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>El Uri relativo (sin dirección host, es decir, '/buscar') que se solicitó. El campo cs-uristem en el registro original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Consulta de URI. Las consultas URI son necesarias solo para páginas dinámicas, por ejemplo, páginas ASP, por lo que este campo normalmente contiene un guión para páginas estáticas.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Puerto de servidor al que se envió la solicitud HTTP (y en el que escucha IIS, ya que lo ha seleccionado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Nombre de usuario autenticado, si la solicitud está autenticada y no es anónima</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Versión del protocolo HTTP usado en la solicitud (es decir, 'HTTP/1.1')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Información de cookies</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Sitio que el usuario visitó por última vez. Este sitio proporciona un vínculo al sitio actual. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Encabezado de host (es decir, 'www.mysite.com') que se ha solicitado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de error de subestado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de estado de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Bytes enviados en la solicitud del cliente al servidor</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Bytes devueltos en la respuesta del servidor al cliente</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de cambio (WindowsServices/software/, etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Categoría del cambio (modificado/agregado/eliminado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de software (actualización/aplicación)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nombre del software (solo se aplica a los cambios de software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Publicador </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Proveedor que publica el software (solo se aplica a los cambios de software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de cambio que se ha aplicado en un servicio de Windows (State/StartupType/Path/ServiceAccount); solo se aplica a los cambios de servicio de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nombre para mostrar del servicio que se cambió</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nombre del servicio que se cambió</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>El estado nuevo (actual) del servicio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Estado conocido anterior del servicio (solo es aplicable si cambia el estado del servicio)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de inicio del servicio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de inicio del servicio anterior (solo es aplicable si cambia el tipo de inicio del servicio)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Cuenta de servicio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Cuenta de servicio anterior (solo es aplicable si cambia la cuenta de servicio)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Ruta de acceso al archivo ejecutable del servicio de Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Ruta de acceso anterior del archivo ejecutable para el servicio de Windows (solo es aplicable si ha cambiado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Descripción del servicio</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Anterior </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Estado anterior de este software (instalado/no instalado/versión anterior)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Último estado este software (instalado/no instalado/versión anterior)</p>
		</td>
	</tr>
</table>

## Entradas de blog: casos de uso de Buscar
- [Búsqueda de registros de IIS W3C en Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Supervisión de errores de copia de seguridad de SQL con la característica de búsqueda y los paneles de Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [Evento del Módulo de administración de IIS: equivalentes a las búsquedas de OpInsights de las reglas de alertas](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [Recopilación de consultas de búsqueda útiles de Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## Otros recursos:
Stefan Roth creó una práctica hoja de referencia sobre búsqueda. Visite su [blog](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/) para obtener más información y descargar su hoja de referencia.

<!---HONumber=July15_HO5-->