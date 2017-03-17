---
title: "Escalado automático de nodos de proceso en un grupo de Azure Batch | Microsoft Docs"
description: "Habilite el escalado automático en un grupo en la nube para ajustar de forma dinámica el número de nodos de ejecución del grupo."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: b8cad4541d4e17f98a35289c6c031b9331ab4a8b
ms.lasthandoff: 02/28/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Creación de una fórmula de escala automática para escalar nodos de proceso en un grupo de Batch

Con la escala automática, el servicio Lote de Azure puede agregar o quitar de forma dinámica nodos de ejecución en un grupo en función de los parámetros definidos. Con esto puede ahorrar tiempo y dinero ajustando automáticamente la cantidad de potencia de procesamiento utilizada por la aplicación: agregar nodos a medida que las demandas de la tarea del trabajo aumentan y quitarlos cuando disminuyen.

Habilitar el escalado automático en un grupo de nodos de proceso mediante la asociación con una fórmula de *escalado automático* que defina, como con el método [PoolOperations.EnableAutoScale][net_enableautoscale] en la biblioteca de [.NET para Batch](batch-dotnet-get-started.md). A continuación, el servicio Lote usa esta fórmula para determinar el número de nodos de ejecución que se necesitan para ejecutar la carga de trabajo. Lote responde a las muestras de datos de métricas de servicio que se recopilan periódicamente y ajusta el número de nodos de proceso del grupo a un intervalo configurable según la fórmula asociada.

Puede habilitar el escalado automático al crear un grupo o bien en un grupo existente. También puede cambiar una fórmula existente en un grupo con el "escalado automático" habilitado. Lote proporciona la capacidad de evaluar las fórmulas antes de asignarlas a grupos y de supervisar el estado de las ejecuciones de escalado automático.

## <a name="automatic-scaling-formulas"></a>Fórmulas de escalado automático
Una fórmula de escalado automático es un valor de cadena definido que contiene una o varias instrucciones y que se asignan al elemento [autoScaleFormula][rest_autoscaleformula] (REST de Batch) o a la propiedad [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (.NET para Batch) de un grupo. Cuando se asigna a un grupo, el servicio Lote usa la fórmula para determinar el número de nodos de proceso de un grupo para el siguiente intervalo de procesamiento (más en intervalos posteriores). La cadena de fórmula no puede superar los 8 KB y puede incluir hasta 100 instrucciones separadas por punto y coma, y saltos de línea y comentarios.

Puede imaginarse que las fórmulas de escalado automático son un "idioma" de escalado automático de Lote. Las instrucciones de fórmula son expresiones de forma libre que pueden incluir variables definidas por el servicio (variables definidas por el servicio de Lote) y variables definidas por el usuario (variables que usted define). Pueden realizar diversas operaciones en estos valores mediante funciones, operadores y tipos integrados. Por ejemplo, una instrucción podría tener la forma siguiente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Las fórmulas suelen tener varias instrucciones que realizan operaciones en los valores obtenidos en las instrucciones anteriores. Por ejemplo, en primer lugar, se obtiene un valor para `variable1`, a continuación, se pasa a una función para rellenar `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Con estas instrucciones en la fórmula, el objetivo es llegar a un número de nodos de proceso al que debe escalar el grupo: el número de **destino** de **nodos dedicados**. Este número puede ser mayor, menor o igual que el número actual de nodos del grupo. Lote evalúa la fórmula de escalado automático del grupo según un intervalo específico (a continuación se describen los[intervalos de escalado automático](#automatic-scaling-interval) ). Después, ajustará el número de destino de nodos del grupo al número que la fórmula de escalado automático especifica en el momento de la evaluación.

A modo de ejemplo rápido, esta fórmula de escalado automático de dos líneas especifica que el número de nodos se debe ajustar según el número de tareas activas, hasta un máximo de 10 nodos de ejecución:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Las siguientes secciones de este artículo tratan de las distintas entidades que conformarán las fórmulas de escalado automático, incluidos variables, operadores, operaciones y funciones. En Lote, encontrará información acerca de cómo obtener varias métricas de recursos y tareas de proceso. Puede usar estas métricas para ajustar de forma inteligente el número de nodos del grupo en función del estado de tareas y el uso de recursos. Después, aprenderá a construir una fórmula y a habilitar el escalado automático en un grupo mediante API de .NET y de REST de Lote. Por último, terminaremos con algunas fórmulas de ejemplo.

> [!IMPORTANT]
> Cada cuenta de Lote de Azure se limita a un número máximo de núcleos (y por lo tanto de nodos de ejecución) que pueden utilizarse para su procesamiento. El servicio Lote creará nodos solo hasta ese límite de núcleos. Por lo tanto, podría no alcanzar el número de nodos de ejecución que se especifica mediante una fórmula. Consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md) para más información sobre la visualización y aumento de las cuotas de la cuenta.
> 
> 

## <a name="variables"></a>Variables
En las fórmulas de escalado automático puede usar tanto variables **definidas por el servicio** como variables **definidas por el usuario**. Las variables definidas por el servicio se integran en el servicio de Lote: algunas son de lectura y escritura y otras son de solo lectura. Las variables definidas por el usuario son las que *usted* define. En la fórmula de ejemplo de dos líneas anterior, `$TargetDedicated` es una variable definida por el servicio, mientras que `$averageActiveTaskCount` está definida por el usuario.

Las tablas siguientes muestran las variables de lectura y escritura y de solo lectura definidas por el servicio Lote.

Puede **obtener** y **establecer** los valores de estas variables definidas por el servicio para administrar el número de nodos de ejecución de un grupo:

| Variables definidas por el servicio de solo escritura | Description |
| --- | --- |
| $TargetDedicated |Número **objetivo** de **nodos de ejecución dedicados** para el grupo. Es el número de nodos de ejecución al que se debe escalar el grupo. Es un número "objetivo" porque es posible que un grupo no alcance el número objetivo de nodos. Esto puede ocurrir si el número de nodos de destino se modifica de nuevo mediante una evaluación posterior de escalado automático antes de que el grupo haya alcanzado el objetivo inicial. También puede ocurrir si se alcanza una cuota de nodos o núcleos de la cuenta de Lote antes de llegar al número de nodos de destino. |
| $NodeDeallocationOption |La acción que se produce cuando se quitan los nodos de ejecución de un grupo. Los valores posibles son:<ul><li>**requeue**: finaliza las tareas de inmediato y las vuelve a poner en la cola de trabajos para que se vuelvan a programar.<li>**terminate**: finaliza las tareas de inmediato y las quitar de la cola de trabajos.<li>**taskcompletion**: espera a que finalicen las tareas actualmente en ejecución y, a continuación, quita el nodo del grupo.<li>**retaineddata**: espera a que todos los datos que se conservan en la tarea local en el nodo se limpien antes de quitar el nodo del grupo.</ul> |

Puede **obtener** el valor de estas variables definidas por el servicio para realizar ajustes basados en las métricas del servicio Batch:

| Variables definidas por el servicio de solo lectura | Description |
| --- | --- |
| $CPUPercent |El porcentaje medio de uso de CPU. |
| $WallClockSeconds |El número de segundos consumidos. |
| $MemoryBytes |La media de megabytes usados. |
| $DiskBytes |La media de gigabytes usados en los discos locales. |
| $DiskReadBytes |El número de bytes leídos. |
| $DiskWriteBytes |El número de bytes escritos. |
| $DiskReadOps |El número de operaciones de disco de lectura realizadas. |
| $DiskWriteOps |El número de operaciones de disco de escritura realizadas. |
| $NetworkInBytes |El número de bytes entrantes. |
| $NetworkOutBytes |El número de bytes salientes. |
| $SampleNodeCount |El número de nodos de ejecución. |
| $ActiveTasks |El número de tareas que se encuentran en estado activo. |
| $RunningTasks |El número de tareas en un estado de ejecución. |
| $PendingTasks |La suma de $ActiveTasks y $RunningTasks. |
| $SucceededTasks |El número de tareas que finalizó correctamente. |
| $FailedTasks |El número de tareas erróneas. |
| $CurrentDedicated |El número actual de dedicado de nodos de ejecución dedicados. |

> [!TIP]
> Las variables de solo lectura definidas por el servicio que se muestran anteriormente son *objetos* que proporcionan varios métodos para acceder a los datos asociados a cada uno de ellos. Consulte la sección [Obtención de datos de ejemplo](#getsampledata) más adelante para más información.
> 
> 

## <a name="types"></a>Tipos
Estos son los **tipos** que se admiten en las fórmulas.

* double
* doubleVec
* doubleVecList
* string
* timestamp: timestamp es una estructura compuesta que contiene los siguientes miembros:
  
  * year
  * mes (1-12)
  * día (1-31)
  * weekday (en formato de número; p.ej., 1 para lunes)
  * hora (en formato de número de 24 horas; p.ej., 13 significa 1 p.m.)
  * minuto (00-59)
  * segundo (00-59)
* timeinterval
  
  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operaciones
Estas **operaciones** se permiten en los tipos enumerados arriba.

| Operación | Operadores admitidos | Tipo de resultado |
| --- | --- | --- |
| double *operador* double |+, -, *, / |double |
| double *operador* timeinterval |* |timeinterval |
| doubleVec *operador* double |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operador* double |*, / |timeinterval |
| timeinterval *operador* timeinterval |+, - |timeinterval |
| timeinterval *operador* timestamp |+ |timestamp |
| timestamp *operador* timeinterval |+ |timestamp |
| timestamp *operador* timestamp |- |timeinterval |
| *operador*double |-, ! |double |
| *operador*timeinterval |- |timeinterval |
| double *operador* double |<, <=, ==, >=, >, != |double |
| string *operador* string |<, <=, ==, >=, >, != |double |
| timestamp *operador* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operador* timeinterval |<, <=, ==, >=, >, != |double |
| double *operador* double |&&, &#124;&#124; |double |

Cuando se prueba un valor double con un operador ternario (`double ? statement1 : statement2`), el valor distinto de cero es **true** y cero es **false**.

## <a name="functions"></a>Funciones
Estas **funciones** predefinidas están disponibles para que las use al definir fórmulas de escalado automático.

| Función | Tipo de valor devuelto | Description |
| --- | --- | --- |
| avg(doubleVecList) |double |Devuelve el valor medio de todos los valores de doubleVecList. |
| len(doubleVecList) |double |Devuelve la longitud del vector creado a partir de doubleVecList. |
| lg(double) |double |Devuelve la base logarítmica 2 de double. |
| lg(doubleVecList) |doubleVec |Devuelve la base logarítmica de componentes 2 de doubleVecList. Se debe pasar explícitamente un vec(double) para el parámetro. En caso contrario, se supone la versión double lg(double). |
| ln(double) |double |Devuelve el registro natural de double. |
| ln(doubleVecList) |doubleVec |Devuelve la base logarítmica de componentes 2 de doubleVecList. Se debe pasar explícitamente un vec(double) para el parámetro. En caso contrario, se supone la versión double lg(double). |
| log(double) |double |Devuelve la base logarítmica 10 de double. |
| log(doubleVecList) |doubleVec |Devuelve la base logarítmica de componentes 10 de doubleVecList. Se debe pasar explícitamente un vec(double) para un único parámetro double. En caso contrario, se supone la versión double log(double). |
| max(doubleVecList) |double |Devuelve el valor máximo de doubleVecList. |
| min(doubleVecList) |double |Devuelve el valor mínimo de doubleVecList. |
| norm(doubleVecList) |double |Devuelve la norma de dos del vector creado a partir de doubleVecList. |
| percentile(doubleVec v, double p) |double |Devuelve el elemento percentil del vector v. |
| rand() |double |Devuelve un valor aleatorio entre 0,0 y 1,0. |
| range(doubleVecList) |double |Devuelve la diferencia entre los valores máximo y mínimo en doubleVecList. |
| std(doubleVecList) |double |Devuelve la desviación de muestra estándar de los valores en doubleVecList. |
| stop() | |Detiene la evaluación de la expresión de escalado automático. |
| sum(doubleVecList) |double |Devuelve la suma de todos los componentes de doubleVecList. |
| time(string dateTime="") |timestamp |Devuelve la marca de tiempo de la hora actual si no se pasan los parámetros o la marca de hora de la cadena dateTime si se pasó. Los formatos de dateTime compatibles son W3C-DTF y RFC 1123. |
| val(doubleVec v, double i) |double |Devuelve el valor del elemento que está en la ubicación i en el vector v con el índice inicial de cero. |

Algunas de las funciones descritas en la tabla anterior pueden aceptar una lista como argumento. La lista separada por comas es cualquier combinación de *double* y *doubleVec*. Por ejemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

El valor *doubleVecList* se convierte en un *doubleVec* individual antes de la evaluación. Por ejemplo, si `v = [1,2,3]`, entonces, llamar a `avg(v)` es equivalente a llamar a `avg(1,2,3)`. Llamar a `avg(v, 7)` es equivalente a llamar a `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtención de datos de ejemplo
Las fórmulas de escalado automático actúan en datos de métricas (muestras) proporcionados por el servicio Lote. Una fórmula aumenta o reduce el tamaño del grupo según los valores que obtiene del servicio. Las variables definidas por el servicio descritas anteriormente son objetos que proporcionan varios métodos para acceder a los datos que están asociados a cada objeto. Por ejemplo, la siguiente expresión muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Description |
| --- | --- |
| GetSample() |El método `GetSample()` devuelve un vector de muestras de datos.<br/><br/>Un ejemplo tiene un valor de 30 segundos de datos de métrica. En otras palabras, las muestras se obtienen cada 30 segundos. No obstante, tal como se mencionó anteriormente, hay un retraso entre cuándo se recopila una muestra y cuándo está disponible para una fórmula. Por lo tanto, puede que no todos los ejemplos durante un período de tiempo determinado estén disponibles para la evaluación a través de una fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica el número de muestras que se obtienen a partir de las muestras recogidas más recientes.<br/><br/>`GetSample(1)` devuelve la última muestra disponible. Para métricas como `$CPUPercent`, sin embargo, esto no debe usarse porque es imposible saber *cuándo* se recopiló la muestra. Puede ser reciente o, debido a problemas del sistema, puede ser mucho más antiguo. En estos casos es mejor usar un intervalo de tiempo como se muestra a continuación.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica un período de tiempo para recopilar datos de muestra. Opcionalmente, también especifica el porcentaje de muestras que deben estar disponibles en el marco de tiempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` debería devolver 20 muestras si todas las muestras de los últimos 10 minutos están presentes en el historial de CPUPercent. Si el último minuto del historial no estaba disponible, solo se devolverán, no obstante, 18 muestras. En este caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` daría error porque solo el 90 por ciento de las muestras están disponibles.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` se realizaría correctamente.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica un período de tiempo para recopilar datos, con una hora de inicio y una hora de finalización.<br/><br/>Como se mencionó anteriormente, hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para una fórmula. Esto debe tenerlo en cuenta cuando use el método `GetSample`. Consulte `GetSamplePercent` a continuación. |
| GetSamplePeriod() |Devuelve el período de las muestras tomadas en un conjunto de datos de muestras históricos. |
| Count() |Devuelve el número total de ejemplos en el historial de métrica. |
| HistoryBeginTime() |Devuelve la marca de tiempo de la muestra de datos disponible más antigua para la métrica. |
| GetSamplePercent() |Devuelve el porcentaje de muestras que están disponibles para un intervalo de tiempo dado. Por ejemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Debido a que se produce un error en el método `GetSample` si el porcentaje de muestras devueltas es inferior al valor de `samplePercent` especificado, puede utilizar el método `GetSamplePercent` para comprobarlo primero. A continuación, puede realizar una acción alternativa si no hay suficientes muestras presentes, sin detener la evaluación de escalado automático. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ejemplos, porcentaje de ejemplo y el método *GetSample()*
La operación principal de una fórmula de escalado automático es la obtención de datos de métricas de tareas y recursos y el ajuste posterior del tamaño de grupo según esos datos. Por lo tanto, es importante tener una idea clara de cómo las fórmulas de escalado automático interactúan con datos de métricas, también denominados "muestras".

**Muestras**

El servicio Lote toma periódicamente *ejemplos* de métricas de tareas y recursos y las pone a disposición de las fórmulas de escalado automático. El servicio Lote graba estas muestras cada 30 segundos. No obstante, hay algo de latencia que provoca un retraso entre el momento en el que se grabaron esas muestras y el momento en el que se ponen a disposición de las fórmulas de escalado automático y estas pueden leer aquellas. Además, debido a diversos factores como problemas con la red o de infraestructura, es posible que las muestras no se hayan grabado para un intervalo determinado. Esto provoca muestras "desaparecidas".

**Porcentaje de muestras**

Al pasar `samplePercent` al método `GetSample()` o llamar al método `GetSamplePercent()`, "porcentaje" se refiere a una comparación entre el número *posible* total de ejemplos que graba el servicio Batch y el número de muestras que realmente están *disponibles* para la fórmula de escalado automático.

Echemos un vistazo a un intervalo de tiempo de 10 minutos como ejemplo. Dado que las muestras se graban cada 30 segundos, el número total máximo de muestras que Lote graba en un intervalo de tiempo de 10 minutos habría sido de 20 muestras (2 por minuto). Sin embargo, debido a la latencia inherente del mecanismo de informes o a algún otro problema dentro de la infraestructura de Azure, puede haber solo 15 muestras disponibles para leer para la fórmula de escalado automático. Esto significa que, durante ese período de 10 minutos, solo el **75 por ciento** del número total de ejemplos que se graban está realmente disponible para la fórmula.

**GetSample() e intervalos de muestra**

Las fórmulas de escalado automático van a aumentar y reducir los grupos, al agregar o quitar nodos. Dado que los nodos cuestan dinero, desea asegurarse de que las fórmulas utilizan un método de análisis inteligente que se basa en un número de datos suficiente. Por lo tanto, recomendamos que use un análisis de tipo tendencias en las fórmulas. Este tipo aumentará y reducirá los grupos basándose en un *intervalo* de ejemplos recopilados.

Para ello, utilice `GetSample(interval look-back start, interval look-back end)` para devolver un **vector** de ejemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Cuando Lote evalúe la línea anterior, devolverá un intervalo de muestras como un vector de valores. Por ejemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Una vez recopilado el vector de ejemplos, puede usar funciones como `min()`, `max()` y `avg()` para derivar valores significativos del intervalo recopilado.

Para mayor seguridad, puede forzar el *error* en una evaluación de fórmula si menos de un determinado porcentaje de ejemplos está disponible para un período de tiempo determinado. Al forzar el error en una evaluación de fórmula, indica a Lote que deje de seguir evaluando la fórmula si el porcentaje de muestras especificado no está disponible, y no se realizará ningún cambio en el tamaño del grupo. Para especificar un porcentaje necesario de ejemplos para que la evaluación se realice correctamente, especifíquelo como el tercer parámetro de `GetSample()`. En este caso, se especifica un requisito del 75 por ciento de muestras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

También es importante, debido al retraso mencionado anteriormente en la disponibilidad de las muestras, especificar siempre un intervalo de tiempo con una hora de inicio retrospectiva cuya anterioridad sea superior a un minuto. Esto es porque los ejemplos tardan aproximadamente un minuto en propagarse por el sistema, por lo que los ejemplos del intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` no suelen estar disponibles. De nuevo, puede utilizar el parámetro de porcentaje de `GetSample()` para forzar un requisito de porcentaje de ejemplos concreto.

> [!IMPORTANT]
> Es **muy recomendable** que **evite confiar *solo* en `GetSample(1)` en las fórmulas de escalado automático**. Esto es porque `GetSample(1)` básicamente dice al servicio Lote "Dame el ejemplo más reciente que tengas, independientemente de cuánto tiempo hace que lo tienes". Puesto que es solo una muestra única, y puede ser una muestra más antigua, no puede ser representativa de la imagen más grande del estado reciente de la tarea o el recurso. Si usa `GetSample(1)`, asegúrese de que forma parte de una instrucción más grande y no solo el punto de datos en el que se basa la fórmula.
> 
> 

## <a name="metrics"></a>Métricas
Puede usar tanto métricas de **recurso** como de **tarea** al definir una fórmula. El número de nodos dedicados de destino se ajusta en el grupo basándose en los datos de métricas que obtenga y evalúe. Consulte la sección [Variables](#variables) más arriba para más información sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>Las <b>métricas del recurso</b> se basan en el uso de la CPU, del ancho de banda y de la memoria de los nodos de proceso, así como en el número de nodos.</p>
        <p> Estas variables definidas por el servicio se usan para realizar ajustes basados en el número de nodos:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variables definidas por el servicio se usan para realizar ajustes basados en el uso de recursos de nodo:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Task</b></td>
    <td><p>Las <b>métricas de la tarea</b> se basan en el estado de las tareas, como Activo, Pendiente y Completado. Las siguientes variables definidas por el servicio se usan para realizar ajustes de tamaño de grupo basados en las métricas de tarea:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Escritura de una fórmula de escalado automático
Para construir una fórmula de escalado automático, es preciso formar instrucciones que usen los componentes anteriores y luego combinar esas instrucciones en una fórmula completa. En esta sección, vamos a crear una fórmula de escalado automático de ejemplo que puede realizar algunas decisiones de escalado del mundo real.

En primer lugar, vamos a definir los requisitos de nuestra nueva fórmula de escalado automático. La fórmula debe:

1. **Aumentar** el número de nodos de proceso de destino en un grupo si el uso de la CPU es alto.
2. **Reducir** el número de nodos de proceso de destino en un grupo cuando el uso de la CPU es bajo.
3. Limite siempre el número **máximo** de nodos a 400.

Para *aumentar* el número de nodos durante un uso elevado de la CPU, definimos la instrucción que rellena una variable definida por el usuario (`$totalNodes`) con un valor que es un 110 por ciento del número de nodos del destino actual, pero solo si el uso medio mínimo de la CPU en los 10 últimos minutos es superior al 70 por ciento: De lo contrario, usamos el valor dedicado actual.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Para *reducir* el número de nodos durante un uso bajo de la CPU, la siguiente instrucción de nuestra fórmula establece la misma variable `$totalNodes` en el 90 por ciento del número objetivo actual de nodos si el uso medio de la CPU en los últimos 60 minutos estaba por debajo del 20 por ciento. De lo contrario, use el valor actual de `$totalNodes` que se rellenamos en la instrucción anterior.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Ahora limite el número de destino de los nodos de ejecución dedicados a un **máximo** de 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Esta es la fórmula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Creación de un grupo habilitado para escalado automático
Para crear un nuevo grupo que tenga habilitado escalado automático, puede usar una de las técnicas siguientes:

**.NET de Lote**

1. Crear el grupo con [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
2. Establecer la propiedad [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) en `true`.
3. Establecer la propiedad [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) con la fórmula de escalado automático.
4. (Opcional) Establecer la propiedad [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (el valor predeterminado es 15 minutos).
5. Confirmar el grupo con [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) o [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**REST de Batch**

* [Agregar un grupo a una cuenta](https://msdn.microsoft.com/library/azure/dn820174.aspx): especifique los elementos `enableAutoScale` y `autoScaleFormula` en la solicitud de API de REST para configurar el escalado automático para un grupo al crearla.

El fragmento de código siguiente crea un grupo habilitado para escalado automático mediante la biblioteca de [.NET para Batch][net_api]. La fórmula de escalado automático del grupo establece el número objetivo de nodos en 5 los lunes y 1 todos los demás días de la semana. El [intervalo de escalado automático](#automatic-scaling-interval) está establecido en 30 minutos. En este y en otros fragmentos de código en C# de este artículo, "myBatchClient" es una instancia totalmente inicializada de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Además de la API de REST de Batch y del SDK de .NET, puede usar cualquiera de los demás [SDK de Batch](batch-technical-overview.md#batch-development-apis), [cmdlets de PowerShell para Batch ](batch-powershell-cmdlets-get-started.md) y la [CLI de Batch](batch-cli-get-started.md) para trabajar con el escalado automático.

> [!IMPORTANT]
> Al crear un grupo habilitado para escalado automático, **no** debe especificar el parámetro `targetDedicated`. Además, si desea cambiar manualmente el tamaño de un grupo con el escalado automático habilitado (por ejemplo, con [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), primero tiene que **deshabilitar** el escalado automático en el grupo y, después, cambiar su tamaño.
> 
> 

### <a name="automatic-scaling-interval"></a>Intervalo de escalado automático
De forma predeterminada, el servicio Lote ajusta el tamaño de un grupo según su fórmula de escalado automático cada **15 minutos**. No obstante, este intervalo es configurable mediante las siguientes propiedades de grupo:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (.NET para Batch)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API de REST)

Los intervalos mínimo y máximo son cinco minutos y 168 horas, respectivamente. Si se especifica un intervalo fuera de este margen, el servicio Lote devolverá un error de solicitud incorrecta (400).

> [!NOTE]
> Actualmente, el escalado automático no está pensado como respuesta inmediata a los cambios, sino para ajustar el tamaño del grupo gradualmente mientras ejecuta una carga de trabajo.
> 
> 

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitación del escalado automático en un grupo existente
A pesar de que ya haya creado un grupo con un número establecido de nodos de proceso mediante el parámetro *targetDedicated*, aún puede habilitar el escalado automático en el grupo. Cada SDK de Batch proporciona una operación "habilitar escalado automático", por ejemplo:

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (.NET de lote)
* [Activar la escala automática en un grupo de servidores][rest_enableautoscale] (API de REST)

Al habilitar el escalado automático en un grupo existente, se aplican las siguientes reglas:

* Si el escalado automático está actualmente **deshabilitado** en el grupo cuando emite la solicitud "habilitar escalado automático", *debe* especificar una fórmula de escalado automático válida al emitir la solicitud. *Opcionalmente* puede especificar un intervalo de evaluación de escalado automático. Si no especifica un intervalo, se usa el valor predeterminado de 15 minutos.
* Si el escalado automático está actualmente **habilitado** en el grupo, puede especificar una fórmula de escalado automático, un intervalo de evaluación o ambas cosas. No se pueden omitir ambas propiedades.
  
  * Si especifica un nuevo intervalo de evaluación de escalado automático, la programación de evaluación existente se detiene y se inicia una nueva. La hora de inicio de la nueva programación es la hora a la que se emitió la solicitud "habilitar escalado automático".
  * Si se omite la fórmula de escalado automático o el intervalo de evaluación, el servicio Batch sigue usando el valor actual de dicha configuración.

> [!NOTE]
> Si se especificó un valor para el parámetro *targetDedicated* cuando se creó el grupo, se ignora cuando se evalúa la fórmula de escalado automático.
> 
> 

Este fragmento de código de C# usa la biblioteca [.NET para Batch][net_api] para habilitar el escalado automático en un grupo existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Actualización de una fórmula de escalado automático
Usará la misma solicitud "habilitar escalado automático" para *actualizar* la fórmula en un grupo habilitado para escalado automático ya existente (por ejemplo, con [EnableAutoScale][net_enableautoscale] en .NET para Batch). No hay ninguna operación especial de actualización de escalado automático. Por ejemplo, si el escalado automático ya está habilitado en "myexistingpool" cuando se ejecuta el siguiente código, su fórmula de escalado automático se sustituye por el contenido de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Actualización del intervalo de escalado automático
Al igual que con la fórmula de escalado automático, usará el mismo método [EnableAutoScale][net_enableautoscale] para cambiar el intervalo de evaluación de escalado automático de un grupo habilitado para escalado automático ya existente. Por ejemplo, para establecer el intervalo de evaluación de escalado automático en 60 minutos para un grupo que ya está tiene habilitado el escalado automático:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Evaluación de una fórmula de escalado automático
Puede evaluar una fórmula antes de aplicarla a un grupo. De esta manera, puede realizar una "ejecución de prueba" de la fórmula para ver cómo se evalúan sus instrucciones antes de usarla en producción.

Para evaluar una fórmula de escalado automático, primero es preciso **habilitar el escalado automático** en el grupo con una **fórmula válida**. Si quiere probar una fórmula en un grupo que aún no tenga habilitado el escalado automático, puede usar la fórmula de una línea `$TargetDedicated = 0` la primera vez que habilite el escalado automático. A continuación, use uno de los siguientes métodos para evaluar la fórmula que quiere probar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)
  
    Estos métodos .NET de Batch requieren el identificador de un grupo existente y una cadena que contenga la fórmula de escalado automático que se evaluará. Los resultados de evaluación están incluidos en la instancia de [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) devuelta.
* [Evaluación de una fórmula de escalado automático](https://msdn.microsoft.com/library/azure/dn820183.aspx)
  
    En esta solicitud de API de REST, especifique el identificador del grupo en el URI y la fórmula de escalado automático en el elemento *autoScaleFormula* del cuerpo de la solicitud. La respuesta de la operación contiene cualquier información de error que pueda relacionarse con la fórmula.

En este fragmento de código de [.NET para Batch][net_api], evaluamos una fórmula antes de aplicarla a [CloudPool][net_cloudpool]. Si el grupo no tiene habilitado el escalado automático, lo habilitaremos primero.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

La evaluación correcta de la fórmula en este fragmento generará una salida similar a la siguiente:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtención de información sobre la ejecución del escalado automático
Para asegurarse de que la fórmula se ejecuta según lo esperado, se recomienda comprobar periódicamente los resultados de las ejecuciones de escalado automático que realiza Batch en su grupo. Para ello, obtenga (o actualice) una referencia al grupo y examine las propiedades de su última ejecución de escalado automático.

En Batch .NET, la propiedad [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) tiene varias propiedades que proporcionan información sobre la última ejecución de escalado automático en el grupo por el servicio Batch.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

En la API de REST, la solicitud para [obtener información sobre un grupo](https://msdn.microsoft.com/library/dn820165.aspx) devuelve información sobre el grupo, por ejemplo, la última ejecución de escalado automático en [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

El siguiente fragmento de código de C# usa la biblioteca Batch .NET para imprimir información sobre la última ejecución de escalado automático en el grupo "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Salida de ejemplo del fragmento de código anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Ejemplo de fórmulas de escalado automático
Echemos un vistazo a algunas fórmulas que muestran distintas formas de ajustar la cantidad de recursos de proceso en un grupo.

### <a name="example-1-time-based-adjustment"></a>Ejemplo 1: Ajuste basado en la fecha y hora
Quizás desee ajustar el tamaño de un grupo según el día de la semana y la hora del día, y aumentar o reducir el número de nodos del grupo en consecuencia:

En primer lugar, esta fórmula obtiene la hora actual. Si se trata de un día laborable (1-5) y dentro del horario laboral (de 8 a.m. a 6 p.m.), el tamaño del grupo de destino se establece en 20 nodos. De lo contrario, se establece en 10 nodos.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Ejemplo 2: Ajuste basado en tareas
En este ejemplo, el tamaño del grupo se ajusta en función del número de tareas en la cola. Tenga en cuenta que las cadenas de la fórmulas aceptan tanto comentarios como saltos de línea.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Ejemplo 3: Contabilidad para tareas paralelas
Este es otro ejemplo que ajusta el tamaño del grupo en función del número de tareas. Esta fórmula también tiene en cuenta el valor de [MaxTasksPerComputeNode][net_maxtasks] que se ha establecido para el grupo. Esto es especialmente útil en aquellas situaciones en las que la [ejecución de tareas paralelas](batch-parallel-node-tasks.md) se ha habilitado en el grupo.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Ejemplo 4: Configuración de un tamaño de grupo inicial
En este ejemplo se muestra un fragmento de código de C# con una fórmula de escalado automático que establece el tamaño del grupo en cierto número de nodos durante un período de tiempo inicial. A continuación, se ajusta el tamaño del grupo según el número de tareas activas y en ejecución una vez transcurrido el período de tiempo inicial.

La fórmula en el siguiente fragmento de código:

* Establece el tamaño inicial del grupo en cuatro nodos.
* No ajusta el tamaño del grupo en los 10 primeros minutos del ciclo de vida del mismo.
* Después de 10 minutos, obtiene el valor máximo del número de tareas activas y en ejecución en los últimos 60 minutos.
  * Si ambos valores son 0 (lo que indica que no hay tareas activas o en ejecución en los últimos 60 minutos) el tamaño del grupo se establece en 0.
  * Si cualquier valor es mayor que cero, no hay cambios.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Pasos siguientes
* [Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo](batch-parallel-node-tasks.md) contiene detalles acerca de cómo se pueden ejecutar varias tareas simultáneamente en los nodos de proceso en el grupo. Además del escalado automático, esta característica puede ayudar a reducir la duración del trabajo para algunas cargas de trabajo, lo que permite ahorrar dinero.
* Para otro ajuste de la eficacia, asegúrese de que la aplicación Lote consulta el servicio Lote de la manera más óptima. En [Consulta eficaz del servicio Lote de Azure](batch-efficient-list-queries.md), aprenderá a limitar la cantidad de datos que atraviesan la conexión al consultar el estado de posiblemente miles de nodos de proceso o tareas.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

