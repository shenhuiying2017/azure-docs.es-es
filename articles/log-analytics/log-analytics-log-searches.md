---
title: "Búsqueda de datos con búsquedas de registros en Azure Log Analytics | Microsoft Docs"
description: "Las búsquedas de registros permiten combinar y correlacionar datos de equipo procedentes de varios orígenes dentro de su entorno."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 0d7b6712-1722-423b-a60f-05389cde3625
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: bwren
ms.openlocfilehash: bf237a837297cb8f1ab3a3340139133adcd2b244
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="find-data-using-log-searches-in-log-analytics"></a>Descripción de las búsquedas de registros en Log Analytics

>[!NOTE]
> En este artículo se describen las búsquedas de registros de Log Analytics mediante el lenguaje de consulta actual.  Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), debe consultar [Understanding log searches in Log Analytics (new)](log-analytics-log-search-new.md) (Descripción de las búsquedas de registros en Log Analytics [nuevo]).


La base de Log Analytics es la característica de búsqueda de registros que permite combinar y correlacionar datos de equipo procedentes de varios orígenes en el entorno. Las soluciones también se basan en la búsqueda de registros con el objeto de proporcionarle métricas dinamizadas en torno a una área de problema determinada.

En la página Buscar, puede crear una consulta y luego, al buscar, puede filtrar los resultados mediante controles de faceta. También puede crear consultas avanzadas para transformar, filtrar y informar sobre sus resultados.

En la mayoría de las páginas de soluciones aparecen consultas de búsqueda comunes. En toda la consola de OMS, puede hacer clic en los iconos o explorar otros elementos para ver sus detalles mediante la búsqueda de registros.

En este tutorial, usaremos ejemplos para cubrir todos los aspectos básicos del uso de la búsqueda de registros.

Comenzaremos con ejemplos sencillos y prácticos y luego construiremos sobre ellos para que pueda comprender los casos de uso prácticos sobre cómo usar la sintaxis para extraer la información deseada de los datos.

Cuando se haya familiarizado con las técnicas de búsqueda, puede repasar la [Referencia sobre búsqueda de registros de Log Analytics](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Uso de filtros básicos
Lo primero que debe saber es que la primera parte de una consulta de búsqueda, antes de cualquier carácter de barra vertical "|", es siempre un *filtro*. Se puede considerar como una cláusula WHERE de TSQL en el sentido de que determina *qué* subconjunto de datos se va a extraer del almacén de datos de OMS. La búsqueda en el almacén de datos consiste en gran medida en especificar las características de los datos que quiere extraer, por lo que es natural que una consulta comience con la cláusula WHERE.

Los filtros más básicos que puede usar son *palabras clave*, como 'error' o 'tiempo de espera', o un nombre de equipo. Estos tipos de consultas sencillas generalmente devuelven diversas formas de datos en el mismo conjunto de resultados. Este es el motivo de que Log Analytics tenga distintos *tipos* de datos en el sistema.

### <a name="to-conduct-a-simple-search"></a>Para llevar a cabo una búsqueda simple
1. En el portal de OMS, haga clic en **Log Search**(Búsqueda de registros).  
    ![icono de búsqueda](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. En el campo de consulta, escriba `error` y luego haga clic en **Buscar**.  
    ![error de búsqueda](./media/log-analytics-log-searches/oms-search-error.png)  
    Por ejemplo, la consulta de `error` en la siguiente imagen devolvió 100 000 registros de **Event** (recopilados por Administración del registro), 18 registros de **ConfigurationAlert** (generados por la evaluación de la configuración) y 12 registros de **ConfigurationChange** (capturados por el seguimiento de cambios).   
    ![resultados de la búsqueda](./media/log-analytics-log-searches/oms-search-results01.png)  

Estos filtros no son en realidad clases o tipos de objeto. *Type* es una etiqueta, una propiedad o una cadena/nombre/categoría, que se adjunta a una parte de los datos. Algunos documentos del sistema están etiquetados como **Type:ConfigurationAlert** y otros como **Type:Perf** o **Type:Event**, y así sucesivamente. Cada resultado de búsqueda, documento, registro o entrada muestra todas las propiedades sin procesar y sus valores para cada uno de estas partes de los datos, y puede usar esos nombres de campo para especificar en el filtro cuándo quiere recuperar únicamente los registros donde el campo tiene ese valor dado.

*Type* es simplemente un campo que todos los registros tienen, no es diferente de ningún otro campo. Esto se estableció en función del valor del campo Type. Ese registro tendrá una forma diferente. Por cierto, **Type=Perf** o **Type=Event** es también la sintaxis que debe conocer para consultar datos o eventos de rendimiento.

Puede usar un signo de dos puntos (:) o un signo igual (=) después del nombre del campo y antes del valor. **Type:Event** and **Type=Event** tienen un significado equivalente, así que puede elegir el estilo que prefiera.

Por lo tanto, si los registros Type=Perf tienen un campo llamado 'CounterName', puede escribir entonces una consulta parecida a `Type=Perf CounterName="% Processor Time"`.

Esta consulta solo le proporcionará los datos de rendimiento donde el nombre del contador de rendimiento sea "% de tiempo de procesador".

### <a name="to-search-for-processor-time-performance-data"></a>Para buscar datos de rendimiento de tiempo de procesador
* En el campo de consulta de búsqueda, escriba `Type=Perf CounterName="% Processor Time"`

También puede ser más específico y usar **InstanceName=_'Total'** en la consulta, que es un contador de rendimiento de Windows. También puede seleccionar una faceta y otro **field:value**. El filtro se agrega automáticamente al filtro en la barra de consulta. Puede ver esto en la siguiente imagen. Se muestra dónde hacer clic para agregar **InstanceName:’_Total’** a la consulta sin escribir nada.

![search facet](./media/log-analytics-log-searches/oms-search-facet.png)

La consulta ahora se convierte en `Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

En este ejemplo, no tiene que especificar **Type=Perf** para llegar a este resultado. Dado que los campos CounterName e InstanceName solo existen para los registros de Type=Perf, la consulta es lo suficientemente específica como para devolver los mismos resultados que la anterior, más larga:

```
CounterName="% Processor Time" InstanceName="_Total"
```

Esto se debe a que todos los filtros de la consulta se evalúan como que están en *AND* con respecto al otro. Efectivamente, cuantos más campos agregue a los criterios, obtendrá resultados más o menos específicos y refinados.

Por ejemplo, la consulta `Type=Event EventLog="Windows PowerShell"` es idéntica a `Type=Event AND EventLog="Windows PowerShell"`. Devuelve todos los eventos que se registraron y se recopilaron del registro de eventos de Windows PowerShell. Si agrega un filtro varias veces seleccionando de manera repetida la misma faceta, entonces el problema es meramente cosmético; podría desordenar la barra de búsqueda, pero seguiría devolviendo los mismos resultados porque el operador AND implícito siempre está ahí.

Puede invertir fácilmente el operador AND implícito usando un operador NOT explícitamente. Por ejemplo:

`Type:Event NOT(EventLog:"Windows PowerShell")` o su equivalente `Type=Event EventLog!="Windows PowerShell"` devuelven todos los eventos de todos los registros, EXCEPTO el registro de Windows PowerShell.

O bien, puede usar otro operador booleano como 'OR'. La siguiente consulta devuelve registros en los que EventLog es Application OR System.

```
EventLog=Application OR EventLog=System
```

Con la consulta anterior, obtendrá entradas para ambos registros en el mismo conjunto de resultados.

Sin embargo, si quita el operador OR dejando en su lugar el operador AND implícito, entonces la siguiente consulta no producirá ningún resultado porque no hay una entrada de registro de eventos que pertenezca a AMBOS registros. Cada entrada de registro de eventos se escribió para solo uno de los dos registros.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Uso de filtros adicionales
La siguiente consulta devuelve entradas de dos registros de eventos para todos los equipos que han enviado datos.

```
EventLog=Application OR EventLog=System
```

![search results](./media/log-analytics-log-searches/oms-search-results03.png)

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
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```

### <a name="field-types"></a>Tipos de campo
Cuando cree filtros, debe comprender las diferencias que existen al trabajar con los distintos tipos de campos que aparecen en las búsquedas de registros.

Los **campos de búsqueda** se muestran en azul en los resultados de la búsqueda.  Puede usar campos de búsqueda en condiciones de búsqueda específicas para el campo, como el siguiente:

```
Type: Event EventLevelName: "Error"
Type: SecurityEvent Computer:Contains("contoso.com")
Type: Event EventLevelName IN {"Error","Warning"}
```

Los **campos de búsqueda de texto libre** se muestran en gris en los resultados de la búsqueda.  No se pueden usar con condiciones de búsqueda específicas para el campo, como los campos de búsqueda.  Se buscan solo al realizar una consulta en todos los campos, como la siguiente.

```
"Error"
Type: Event "Exception"
```


### <a name="boolean-operators"></a>Operadores booleanos
Con los campos de fecha y hora, y numéricos, puede buscar valores mediante *mayor que*, *menor que* y *menor o igual que*. Puede usar operadores simples como >, < , >=, <= , !=, en la barra de búsqueda de consulta.

Puede consultar un registro de eventos específico para un período de tiempo específico. Por ejemplo, las últimas 24 horas se expresan con la siguiente expresión mnemotécnica.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Para realizar búsquedas mediante un operador booleano
* En el campo de consulta de búsqueda, escriba `EventLog=System TimeGenerated>NOW-24HOURS`  
    ![búsqueda con booleano](./media/log-analytics-log-searches/oms-search-boolean.png)

Aunque puede controlar el intervalo de tiempo gráficamente, y casi siempre es recomendable hacerlo, resulta beneficioso incluir un filtro de tiempo directamente en la consulta. Por ejemplo, esto funciona bien con paneles donde puede invalidar el tiempo de cada icono, con independencia del selector de tiempo *global* de la página del panel. Para obtener más información, consulte [Cuestiones sobre el tiempo en el panel](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Si desea filtrar por tiempo, tenga en cuenta que se obtienen resultados para la *intersección* de los dos períodos: el especificado en el portal de OMS (S1) y el especificado en la consulta (S2).

![intersección](./media/log-analytics-log-searches/oms-search-intersection.png)

Esto significa que, si los períodos no se cruzan, por ejemplo en el portal de OMS donde elige **esta semana** y en la consulta donde define **última semana**, entonces no hay ninguna intersección y no recibirá ningún resultado.

Los operadores de comparación usados con el campo TimeGenerated también son útiles en otras situaciones. Por ejemplo, con los campos numéricos.

Por ejemplo, dado que las alertas de evaluación de la configuración tienen los siguientes valores de gravedad:

* 0 = Información
* 1= Advertencia
* 2 = Crítico

Puede consultar las alertas de advertencia y críticas y también excluir las informativas con la siguiente consulta:

```
Type=ConfigurationAlert  Severity>=1
```


También puede usar consultas por rango. Esto significa que puede proporcionar el intervalo inicial y final de los valores de una secuencia. Por ejemplo, si desea los eventos del registro de eventos de Operations Manager en los que EventID sea mayor o igual que 2100 pero no superior a 2199, la siguiente consulta devolvería estos valores.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


> [!NOTE]
> La sintaxis de intervalo que debe usar es el separador dos puntos (:) field:value y *no* el signo igual (=). Incluya el extremo inferior y superior del intervalo entre corchetes y sepárelos con dos puntos (..).
>
>

## <a name="manipulate-search-results"></a>Manipulación de los resultados de búsqueda
Al buscar datos, querrá refinar la consulta de búsqueda y tener un buen nivel de control sobre los resultados. Cuando se recuperen los resultados, puede aplicar comandos para transformarlos.

Los comandos de las búsquedas de Log Analytics *deben* seguir al carácter de barra vertical (|). Un filtro debe ser siempre la primera parte de una cadena de consulta. Define el conjunto de datos con el que trabaja y luego "canaliza" esos resultados en un comando. Luego puede usar la canalización para agregar comandos adicionales. Es parecido a grandes rasgos a la canalización de Windows PowerShell.

En general, el lenguaje de búsqueda de Log Analytics intenta seguir el estilo y los criterios de PowerShell a fin de que sea familiar para los profesionales de TI y para facilitar la curva de aprendizaje.

Los comandos tienen nombres de verbos para que pueda saber fácilmente lo que hacen.  

### <a name="sort"></a>Sort
El comando sort permite definir el criterio de ordenación por uno o varios campos. Aunque no lo use, se aplica de forma predeterminada un tiempo en orden descendente. Los resultados más recientes siempre están en la parte superior de los resultados de búsqueda. Esto significa que cuando ejecuta una búsqueda con `Type=Event EventID=1234` , lo que realmente se ejecuta es:

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

### <a name="limit-and-top"></a>Limit y Top
Otro comando menos conocido es LIMIT. Limit es un verbo al estilo de PowerShell. Es funcionalmente idéntico al comando TOP. Las consultas siguientes devuelven los mismos resultados.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Para realizar búsquedas con TOP
* En el campo de consulta de búsqueda, escriba `Type=Event EventID=600 | Top 1` .  
    ![búsqueda con Top](./media/log-analytics-log-searches/oms-search-top.png)

En la imagen anterior, hay 358 000 registros con EventID=600. Los campos, las facetas y los filtros de la izquierda siempre muestran información sobre los resultados devueltos *por la parte de filtro* de la consulta, que es la situada delante de cualquier carácter de barra vertical. El panel **Resultados** solo devuelve el resultado más reciente (1), porque el comando de ejemplo ha dado forma a los resultados y los ha transformado.

### <a name="select"></a>Seleccionar
El comando SELECT se comporta como Select-Object en PowerShell. Devuelve los resultados filtrados que no tienen todas sus propiedades originales. En su lugar, selecciona solo las propiedades que especifique.

#### <a name="to-run-a-search-using-the-select-command"></a>Para ejecutar una búsqueda con el comando Select
1. En el campo de búsqueda, escriba `Type=Event` y luego haga clic en **Buscar**.
2. Haga clic en **+ mostrar más** en uno de los resultados para ver todas las propiedades que tienen los resultados.
3. Seleccione algunas de ellas explícitamente y la consulta cambiará a `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![Búsqueda con Select](./media/log-analytics-log-searches/oms-search-select.png)

Se trata de un comando especialmente útil cuando desea controlar los resultados de búsqueda y elegir solo las partes de los datos que realmente importan para la exploración, que casi siempre no son el registro completo. También es útil cuando registros de diferentes tipos tienen *algunas* propiedades en común, pero no *todas*. Luego, puede generar resultados con la apariencia natural de una tabla, o que funcionan bien cuando se exportan a un archivo CSV y luego se transforman en Excel.

## <a name="use-the-measure-command"></a>Uso del comando measure
MEASURE es uno de los comandos más versátiles en las búsquedas de Log Analytics. Permite aplicar *funciones* estadísticas a los datos y agregar los resultados agrupados por un campo dado. Son muchas las funciones estadísticas que admite este comando.

### <a name="measure-count"></a>Measure count()
La primera función estadística con la que se trabaja y una de las más fáciles de entender es la función *count()* .

Los resultados de cualquier consulta de búsqueda como `Type=Event`, muestran filtros también llamados facetas en el lado izquierdo. Los filtros muestran una distribución de valores por un campo dado de los resultados de la búsqueda realizada.

![search measure count](./media/log-analytics-log-searches/oms-search-measure-count01.png)

Por ejemplo, en la imagen anterior verá el campo **Computer**, y muestra que dentro de los casi 739 000 eventos de los resultados, hay 68 valores únicos y distintos para ese **Computer** campo en esos registros. El icono solo muestra los cinco principales, que son los cinco valores más comunes que están escritos en el campo **Computer** , ordenados por el número de documentos que contienen ese valor específico en ese campo. En la imagen se ve que, entre esos eventos casi 369 000 eventos, 90 000 proceden del equipo OpsInsights04.contoso.com, 83 000 del equipo DB03.contoso.com y así sucesivamente.

¿Y si quisiera ver todos los valores, ya que el icono solo muestra los cinco primeros?

Eso es lo que puede hacer el comando measure con la función count(). Esta función no usa ningún parámetro. Usted solo especifica el campo por el que desea agrupar, en este caso, el campo **Computer** :

`Type=Event | Measure count() by Computer`

![search measure count](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

Sin embargo, **Computer** es simplemente un campo usado *en* cada dato; no hay ninguna base de datos relacional implicada y no hay ningún objeto **Computer** independiente en ninguna parte. Los valores *en* los datos bastan para describir la entidad que los ha generado, así como otras características y aspectos de los datos; de ahí el término *faceta*. Sin embargo, también puede agrupar por otros campos. Dado que los resultados originales de casi 739 000 eventos que se canalizan al comando measure también tienen un campo denominado **EventID**, puede aplicar la misma técnica para agrupar por ese campo y obtener un recuento de eventos por EventID:

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

#### <a name="to-search-using-measure-count"></a>Para buscar mediante measure count
* En el campo de consulta de búsqueda, escriba `Type=Event | Measure count() by EventID`
* Anexe `| Select EventID` al final de la consulta.
* Finalmente, anexe `| Sort EventID asc` al final de la consulta.

Hay un par de cuestiones importantes que es necesario observar y resaltar:

En primer lugar, los resultados que ve ya no son los resultados sin formato originales. Son los resultados de agregado, básicamente grupos de resultados. Esto no supone un problema, pero debe comprender que está interactuando con una forma muy diferente de los datos que difiere de la forma sin formato original que se crea sobre la marcha como resultado de la función estadística o de agregación.

En segundo lugar, **Measure count** solo devuelve actualmente los 100 primeros resultados diferentes. Este límite no se aplica a las otras funciones estadísticas. Por lo tanto, normalmente deberá usar primero un filtro más preciso para buscar elementos específicos antes de aplicar measure count().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Uso de las funciones max y min con el comando measure
Son varias las situaciones en las que **Measure Max()** y **Measure Min()** son útiles. Sin embargo, puesto que cada función es opuesta entre sí, ilustraremos Max() y usted puede experimentar con Min() por su cuenta.

Si busca eventos de seguridad, tienen una propiedad **Level** que puede variar. Por ejemplo:

```
Type=SecurityEvent
```

![search measure count start](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Si quiere ver el valor más alto de todos los eventos de seguridad dado un equipo común, puede usar agrupar por campo.

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![search measure max computer](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Mostrará que, para los equipos con registros **Level**, la mayoría tiene al menos el nivel 8 y muchos, el nivel 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![search measure max time generated computer](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Esta función es muy adecuada para números, pero también para campos de fecha y hora. Resulta útil para comprobar la última marca de tiempo o la más reciente en cualquier parte de los datos indexados para cada equipo. Por ejemplo: ¿cuándo se notificó el evento de seguridad más reciente para cada máquina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Uso de la función avg con el comando measure
La función estadística Avg() usada con measure le permite calcular el valor medio de algún campo y agrupar los resultados por el mismo campo u otro diferente. Esto resulta útil en muchos casos, por ejemplo, con los datos de rendimiento.

Comenzaremos con los datos de rendimiento. Tenga en cuenta que OMS actualmente recopila contadores de rendimiento tanto para equipos Windows como Linux.

Para buscar *todos* los datos de rendimiento, la consulta más básica es:

```
Type=Perf
```

![search avg start](./media/log-analytics-log-searches/oms-search-avg01.png)

Lo primero que notará es que Log Analytics muestra tres perspectivas: Lista, que muestra los registros en los que se basan los gráficos; Tabla, que muestra una vista tabular de los datos de los contadores de rendimiento; y Métricas, que muestra gráficos para los contadores de rendimiento.

En la imagen anterior, hay dos conjuntos de campos marcados que indican lo siguiente:

* El primer conjunto identifica el nombre del contador de rendimiento de Windows, el nombre del objeto y el nombre de instancia en el filtro de consulta. Estos son los campos que usará probablemente como facetas o filtros:
* **CounterValue** es el valor real del contador. En este ejemplo, el valor es *75*.
* **TimeGenerated** es 12:51, en formato de 24 horas.

Esta es una vista de las métricas en un gráfico.

![search avg start](./media/log-analytics-log-searches/oms-search-avg02.png)

Después de leer sobre la forma de los registros Perf y de haber leído sobre otras técnicas de búsqueda, puede usar measure Avg() para agregar este tipo de datos numéricos.

A continuación se muestra un sencillo ejemplo:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![search avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

En este ejemplo, selecciona el contador de rendimiento de tiempo total de la CPU y el promedio por equipo. Si desea restringir los resultados a solo las últimas 6 horas, puede usar el control de filtro de tiempo o especificarlo en la consulta de la forma siguiente:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Para buscar con la función avg con el comando measure
* En el campo de consulta de búsqueda, escriba `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.

Puede agregar y correlacionar los datos *entre* equipos. Por ejemplo, imagine que tiene un conjunto de hosts en algún tipo de granja de servidores donde cada nodo es igual a otro y todos hacen el mismo tipo de trabajo, y la carga se debe equilibrar de manera aproximada. Podría obtener sus contadores en un solo paso con la siguiente consulta y conseguir los promedios para toda la granja. Puede comenzar eligiendo los equipos con el ejemplo siguiente:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Ahora que tiene los equipos, solo quiere seleccionar dos indicadores clave de rendimiento (KPI): % de uso de la CPU y % de espacio libre en disco. Por lo tanto, esa parte de la consulta se convierte en:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Ahora puede agregar equipos y contadores con el ejemplo siguiente:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Como tiene una selección muy específica, el comando **measure Avg()** puede devolver el promedio no por equipo, sino en la granja de servidores, con solo agrupar por CounterName. Por ejemplo:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Esto proporciona una vista útil compacta de un par de KPI de su entorno.

![search avg grouping](./media/log-analytics-log-searches/oms-search-avg04.png)

Puede usar fácilmente la consulta de búsqueda en un panel. Por ejemplo, puede guardar la consulta de búsqueda y crear un panel a partir de ella llamado *KPI de granja de servidores web*. Para aprender más acerca del uso de paneles, consulte [Create a custom dashboard in Log Analytics](log-analytics-dashboards.md)(Creación de un panel personalizado en Log Analytics).

![search avg dashboard](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Uso de la función sum con el comando measure
La función sum es similar a otras funciones del comando measure. Puede ver un ejemplo sobre cómo usar la función sum en [Búsqueda de registros de W3C IIS en Visión operativa de Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Puede usar Max() y Min() con cadenas de texto, números, fechas y horas. Con cadenas de texto, se ordenan alfabéticamente y obtiene la primera y la última.

Sin embargo, no puede usar Sum() con campos que no sean numéricos. Esto también se aplica a Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Uso de la función percentile con el comando measure
La función percentile se parece a Avg() y Sum() en que solo se puede usar para campos numéricos. Puede utilizar cualquier percentil entre 1 y 99 en un campo numérico. También puede usar ambos comandos, **percentile** y **pct**. Estos son algunos ejemplos:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Uso del comando where
El comando where funciona como un filtro, pero se puede aplicar en la canalización para filtrar aún más los resultados agregados producidos por un comando measure, por oposición a los resultados sin formato que se filtran al principio de una consulta.

Por ejemplo:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Puede agregar otro carácter de barra vertical "|" y el comando where para obtener solo los equipos cuyo promedio de CPU sea superior al 80 %, con el siguiente ejemplo:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Si está familiarizado con Microsoft System Center Operations Manager, puede considerar el comando where en términos de módulo de administración. Si el ejemplo fuera una regla, la primera parte de la consulta sería el origen de datos y el comando where sería la detección de condición.

Puede usar la consulta como icono en **Mi panel**, como monitor de ordenaciones, para ver cuándo las CPU de equipo se sobreutilizan. Para aprender más acerca de los paneles, consulte [Create a custom dashboard in Log Analytics](log-analytics-dashboards.md)(Creación de un panel personalizado en Log Analytics). También puede crear y usar paneles mediante la aplicación móvil. Para más información, consulte la [aplicación móvil de OMS](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). En los dos iconos inferiores de la siguiente imagen, puede ver que el monitor se muestra como una lista y como un número. Básicamente, desea siempre que el número sea cero y la lista esté vacía. De lo contrario, indica una condición de alerta. Si es necesario, puede usarlo para ver qué máquinas están bajo presión.

![mobile dashboard](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Uso del operador in
El operador *IN*, junto con *NOT IN*, permite usar subbúsquedas, que son búsquedas que incluyen otra búsqueda como argumento. Se encierran entre llaves {} dentro de otra búsqueda *principal* o *exterior*. El resultado de una subbúsqueda, con frecuencia una lista de resultados distintos, se usa a continuación como argumento en su búsqueda principal.

Puede usar subbúsquedas para hacer coincidir subconjuntos de los datos que no se puedan describir directamente en una expresión de búsqueda, pero que se puedan generar a partir de una búsqueda. Por ejemplo, si está interesado en usar una búsqueda para encontrar todos los eventos de *equipos donde faltan actualizaciones de seguridad*, debe diseñar una subbúsqueda que identifique esos *equipos donde faltan actualizaciones de seguridad* antes de buscar los eventos que pertenezcan a dichos hosts.

Por lo tanto, puede expresar *equipos donde actualmente faltan actualizaciones de seguridad* con la siguiente consulta:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![Ejemplo de búsqueda con IN](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Una vez que tenga la lista, puede usar la búsqueda como búsqueda interna para proporcionar la lista de equipos a una búsqueda externa (principal) que buscará eventos para esos equipos. Para ello, encierre la búsqueda interna entre llaves y proporcione sus resultados como valores posibles para un filtro o campo en la búsqueda externa mediante el operador IN. La consulta se parecería a:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![Ejemplo de búsqueda con IN](./media/log-analytics-log-searches/oms-search-in02-revised.png)

Observe también el filtro de tiempo que se usa en la búsqueda interna, porque la evaluación de la actualización del sistema toma una instantánea de todos los equipos cada 24 horas. También puede hacer la consulta interna más ligera y precisa si busca para un solo día. En su lugar, la búsqueda externa usa la selección de tiempo en la interfaz de usuario y recupera eventos de los siete últimos días. Consulte [Operadores booleanos](#boolean-operators) para más información acerca de los operadores de tiempo.

Como realmente solo usa los resultados de la búsqueda interna como valor de filtro para la externa, todavía puede aplicar comandos en la búsqueda externa. Por ejemplo, puede agrupar los eventos anteriores con otro comando measure:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![Ejemplo de búsqueda con IN](./media/log-analytics-log-searches/oms-search-in03-revised.png)

Por lo general, conviene que la consulta interna se ejecute rápidamente porque Log Analytics tiene tiempos de espera del servicio para ella y también para devolver una cantidad pequeña de resultados. Si la consulta interna devuelve más resultados, se trunca la lista, lo que podría causar que la búsqueda externa devuelva resultados incorrectos.

Otra regla es que la búsqueda interna actualmente necesita proporcionar resultados *agregados* . Es decir, debe contener un comando *measure* ; actualmente no se pueden proporcionar resultados sin procesar a una búsqueda externa.

Además, solo puede haber un operador IN y debe ser el último filtro de la consulta. No se pueden combinar varios operadores IN mediante OR; básicamente, esto impide que se ejecuten varias subbúsquedas. Lo importante es que solo es posible una subbúsqueda o búsqueda interna por cada búsqueda externa.

Incluso con estos límites, IN permite muchos tipos de búsquedas correlacionadas y con él puede definir algo similar a los grupos, como equipos, usuarios o archivos, según los campos que contengan sus datos. Estos son otros ejemplos:

**Todas las actualizaciones que faltan en equipos donde se deshabilitó la configuración Actualización automática**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Todos los eventos de error de equipos que ejecutan SQL Server (=donde se ejecutó la evaluación de SQL)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Todos los eventos de seguridad de equipos que son controladores de dominio (=donde se ejecutó la evaluación de AD)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**¿Qué otras cuentas han iniciado sesión en los mismos equipos que la cuenta BACONLAND\jochan?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Uso del comando distinct
Como su nombre indica, este comando proporciona una lista de valores distintos para un campo. Es sorprendentemente sencillo pero muy útil. Se podría lograr lo mismo con el comando measure count(), como se muestra a continuación.

```
Type=Event | Measure count() by Computer
```

![Ejemplo del comando de búsqueda DISTINCT](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Sin embargo, si todo lo que le interesa es una simple lista de valores distintos y no el número de documentos que tengan esos valores, DISTINCT puede proporcionar una salida más limpia y fácil de leer, con una sintaxis más corta, como se muestra a continuación.

```
Type=Event | Distinct Computer
```
![Ejemplo del comando de búsqueda DISTINCT](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Uso de la función countdistinct con el comando measure
La función countdistinct cuenta el número de valores distintos dentro de cada grupo. Por ejemplo, podría utilizarse para contar el número de equipos únicos que elaboran informes para cada tipo:

```
* | measure countdistinct(Computer) by Type
```

![Countdistinct de OMS](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Uso del comando measure interval
Con una recopilación de datos de rendimiento prácticamente en tiempo real, puede recopilar y visualizar cualquier contador de rendimiento en Log Analytics. Basta con escribir la consulta **Type:Perf** para que se devuelvan miles de gráficos de métricas en función del número de contadores y los servidores en el entorno de Log Analytics. Con la agregación de métricas a petición, puede consultar las métricas generales de alto nivel de su entorno y profundizar en los datos más granulares cuando sea necesario.

Supongamos que desea saber cuál es el promedio de CPU entre todos los equipos. Es posible que ver el promedio de CPU para cada equipo no resulte útil porque los resultados podrían uniformizarse. Para examinarlo con más detalle, puede agregar el resultado en intervalos de tiempo más reducidos y después observar una serie temporal en distintas dimensiones. Por ejemplo, puede obtener el promedio de uso de CPU por hora en todos los equipos de la forma siguiente:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![Medida de promedios en un intervalo](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

De forma predeterminada, estos resultados se mostrarán en un gráfico de líneas interactivo con varias series.  Este gráfico admite alternar entre series (con el cambio de escala del eje Y), usar el zoom y mantener el mouse sobre elementos.  La opción de visualización de la tabla sigue disponible para ver los datos sin procesar si es necesario.

También puede agrupar por otros campos. En este ejemplo, estoy mirando todos los contadores de porcentaje de un equipo específico y quiero saber cuál es el percentil 70 por hora de cada contador:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Hay que destacar que estas consultas no se limitan a los contadores de rendimiento. Se pueden aplicar a cualquier métrica. En este ejemplo, estoy consultando los registros de IIS para W3C. Quiero saber cuál es el tiempo máximo que se tarda en procesar cada solicitud a lo largo de un intervalo de 5 minutos:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Uso de varios agregados en una consulta
Puede especificar varias cláusulas de agregado en un comando measure.  A cada una se le puede asignar un alias de forma independiente.  Si no se le proporciona un alias, el nombre de campo resultante será la función de agregado que se usó (es decir, "avg(CounterValue)" para avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![Varios agregados en OMS 1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Este es otro ejemplo:

 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de las búsquedas de registros, consulte:

* Use [Custom fields in Log Analytics](log-analytics-custom-fields.md) (Campos personalizados en Log Analytics) para ampliar las búsquedas de registros.
* Revise la [Referencia sobre búsqueda de registros de Log Analytics](log-analytics-search-reference.md) para ver todos los campos de búsqueda y las facetas disponibles en Log Analytics.
