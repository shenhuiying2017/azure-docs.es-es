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
ms.date: 06/20/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0e49cd8a64a48c53f5b6104703164a597c797f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Creación de una fórmula de escala automática para escalar nodos de proceso en un grupo de Batch

Azure Batch puede escalar grupos automáticamente en función de los parámetros que defina. Con el escalado automático, Batch agrega nodos dinámicamente a un grupo a medida que aumenta la demanda de tareas y quita nodos de ejecución a medida que disminuye. El ajuste automático del número de nodos de ejecución que usa la aplicación Batch puede suponer un ahorro de tiempo y dinero. 

Habilite el escalado automático en un grupo de nodos de ejecución asociándolo a una *fórmula de escalado automático* que se puede definir. El servicio Batch usa esta fórmula para determinar el número de nodos de ejecución que se necesitan para ejecutar la carga de trabajo. Los nodos de ejecución pueden ser nodos dedicados o [nodos de prioridad baja](batch-low-pri-vms.md). Batch responde a los datos de métricas de servicio que se recolectan periódicamente. Con estos datos de métricas, Batch ajusta el número de nodos de ejecución del grupo en función de la fórmula y en un intervalo configurable.

Puede habilitar el escalado automático al crear un grupo o bien en un grupo existente. También puede cambiar una fórmula existente en un grupo configurado para el escalado automático. Batch le permite evaluar las fórmulas antes de asignarlas a grupos y supervisar el estado de las ejecuciones de escalado automático.

En este artículo se describen las distintas entidades que conforman las fórmulas de escalado automático, por ejemplo, variables, operadores, operaciones y funciones. Trataremos cómo obtener distintas métricas de recursos y tareas de proceso en Batch. Puede usar estas métricas para ajustar el número de nodos del grupo en función del estado de las tareas y del uso de recursos. También explicaremos cómo construir una fórmula y habilitar el escalado automático en un grupo mediante API de .NET y API de REST de Batch. Por último, terminaremos con algunas fórmulas de ejemplo.

> [!IMPORTANT]
> Al crear una cuenta de Batch puede especificar la [configuración de la cuenta](batch-api-basics.md#account), que determina si se asignan grupos en una suscripción de servicio de Batch (opción predeterminada) o en su suscripción de usuario. Si ha creado su cuenta de Batch con la configuración predeterminada del servicio de Batch, la cuenta estará limitada a un máximo de núcleos que se pueden usar para el procesamiento. El servicio de Batch escala nodos de ejecución solo hasta ese límite de núcleos. Por este motivo, puede que el servicio de Batch no alcance el número de nodos de ejecución que se especifiquen con una fórmula de escalado automático. Consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md) para más información sobre la visualización y aumento de las cuotas de la cuenta.
>
>Si ha creado su cuenta con la configuración de suscripción de usuario, su cuenta comparte la cuota de núcleos de la suscripción. Para más información, consulte [Límites de Virtual Machines](../azure-subscription-service-limits.md#virtual-machines-limits) en [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Fórmulas de escalado automático
Una fórmula de escalado automático es un valor de cadena que el usuario define y contiene una o varias instrucciones. La fórmula de escalado automático se asigna a un elemento [autoScaleFormula][rest_autoscaleformula] de un grupo (REST de Batch) o a una propiedad [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). El servicio Batch usa la fórmula para determinar el número de nodos de ejecución del grupo durante el intervalo de procesamiento siguiente. La cadena de fórmula no puede superar los 8 KB y puede incluir hasta 100 instrucciones separadas por punto y coma, así como saltos de línea y comentarios.

Puede imaginarse que las fórmulas de escalado automático son un "lenguaje" de escalado automático de Batch. Las instrucciones de fórmula son expresiones de forma libre que pueden incluir variables definidas por el servicio (variables definidas por el servicio de Lote) y variables definidas por el usuario (variables que usted define). Pueden realizar diversas operaciones en estos valores mediante funciones, operadores y tipos integrados. Por ejemplo, una instrucción podría tener la forma siguiente:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Las fórmulas suelen tener varias instrucciones que realizan operaciones en los valores obtenidos en las instrucciones anteriores. Por ejemplo, en primer lugar, se obtiene un valor para `variable1`, a continuación, se pasa a una función para rellenar `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Incluya estas instrucciones en la fórmula de escalado automático para llegar a un determinado número de nodos de ejecución. Los nodos dedicados y los nodos de prioridad baja tienen sus propios valores de destino, por lo que puede definir un destino para cada tipo de nodo. Una fórmula de escalado automático puede incluir un valor de destino para los nodos dedicados, un valor de destino para los nodos de prioridad baja o ambos.

El número objetivo de nodos puede ser mayor, menor o igual que el número actual de nodos de ese tipo que hay en el grupo. Batch evalúa la fórmula de escalado automático del grupo según un intervalo específico (consulte [Intervalo de escalado automático](#automatic-scaling-interval)). Batch ajusta el número de destino de cada tipo de nodo del grupo al número que especifica la fórmula de escalado automático en el momento de la evaluación.

### <a name="sample-autoscale-formula"></a>Fórmula de escalado automático de muestras

Aquí puede ver un ejemplo de fórmula de escalado automático que se puede ajustar para trabajar en la mayoría de escenarios. Las variables `startingNumberOfVMs` y `maxNumberofVMs` de la fórmula de ejemplo se pueden ajustar a sus necesidades. Esta fórmula escala los nodos dedicados, pero se puede modificar para escalar también los nodos de prioridad baja. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Con esta fórmula de escalado automático, el grupo se crea inicialmente con una sola máquina virtual. La métrica `$PendingTasks` define el número de tareas que están en ejecución o en cola. La fórmula busca el número promedio de tareas pendientes en los últimos 180 segundos y establece la variable `$TargetDedicatedNodes` en consecuencia. La fórmula garantiza que el número de destino de nodos dedicados nunca supere la cantidad de 25 máquinas virtuales. El grupo crece automáticamente a medida que se envían nuevas tareas. Cuando las tareas finalizan, las máquinas virtuales quedan libres una a una y la fórmula de escalado automático reduce el grupo.

## <a name="variables"></a>variables
En las fórmulas de escalado automático puede usar tanto variables **definidas por el servicio** como variables **definidas por el usuario**. Las variables definidas por el servicio están integradas en el servicio de Batch. Algunas variables definidas por el servicio son de lectura y escritura, mientras que otras son de solo lectura. Las variables definidas por el usuario son las que usted define. En la fórmula de ejemplo que se muestra en la sección anterior, `$TargetDedicatedNodes` y `$PendingTasks` son variables definidas por el servicio. Las variables `startingNumberOfVMs` y `maxNumberofVMs` son variables definidas por el usuario.

> [!NOTE]
> Las variables definidas por el servicio van siempre precedidas de un símbolo de dólar ($). En el caso de las variables definidas por el usuario, el símbolo de dólar es opcional.
>
>

En las siguientes tablas se muestran las variables de lectura y escritura y de solo lectura definidas por el servicio de Batch.

Puede obtener y establecer los valores de estas variables definidas por el servicio para administrar el número de nodos de ejecución de un grupo:

| Variables definidas por el servicio de solo escritura | Descripción |
| --- | --- |
| $TargetDedicatedNodes |Número objetivo de nodos de ejecución dedicados para el grupo. El número de nodos dedicados se especifica como destino porque un grupo podría no lograr siempre el número de nodos deseado. Por ejemplo, podría pasar que el grupo no alcanzara el número de destino si el número de nodos dedicados se modifica con una evaluación de escalado automático antes de que el grupo haya alcanzado el valor de destino inicial. <br /><br /> Podría pasar que un grupo de una cuenta creada con la configuración del servicio de Batch no alcanzara su valor de destino si el destino supera una cuota de núcleos o de nodos de la cuenta de Batch. Podría pasar que un grupo de una cuenta creada con la configuración de suscripción de usuario no alcanzara su valor de destino si el destino supera la cuota de núcleos compartidos de la suscripción.|
| $TargetLowPriorityNodes |Número objetivo de nodos de ejecución de prioridad baja para el grupo. El número de nodos de prioridad baja se especifica como destino porque un grupo podría no lograr siempre el número de nodos deseado. Por ejemplo, podría pasar que el grupo no alcanzara el número de destino si el número de nodos de prioridad baja se modifica con una evaluación de escalado automático antes de que el grupo haya alcanzado el valor de destino inicial. Además, también podría pasar que un grupo no alcanzara el valor de destino si el destino supera una cuota de núcleos o de nodos de la cuenta de Batch. <br /><br /> Para obtener más información sobre los nodos de ejecución de prioridad baja, consulte [Uso de máquinas virtuales de prioridad baja con Batch (versión preliminar)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |La acción que se produce cuando se quitan los nodos de ejecución de un grupo. Los valores posibles son:<ul><li>**requeue**: finaliza las tareas de inmediato y las vuelve a poner en la cola de trabajos para que se vuelvan a programar.<li>**terminate**: finaliza las tareas de inmediato y las quitar de la cola de trabajos.<li>**taskcompletion**: espera a que finalicen las tareas actualmente en ejecución y, a continuación, quita el nodo del grupo.<li>**retaineddata**: espera a que todos los datos que se conservan en la tarea local en el nodo se limpien antes de quitar el nodo del grupo.</ul> |

Puede obtener el valor de estas variables definidas por el servicio para efectuar ajustes basados en las métricas del servicio de Batch:

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
| $ActiveTasks |Número de tareas que están listas para su ejecución pero que aún no se están ejecutando. El recuento de $ActiveTasks incluye todas las tareas que tienen un estado activo y cuyas dependencias se han cumplido. Todas las tareas que tengan un estado activo pero cuyas dependencias no se hayan cumplido se excluirán del recuento de $ActiveTasks.|
| $RunningTasks |El número de tareas en un estado de ejecución. |
| $PendingTasks |La suma de $ActiveTasks y $RunningTasks. |
| $SucceededTasks |El número de tareas que finalizó correctamente. |
| $FailedTasks |El número de tareas erróneas. |
| $CurrentDedicatedNodes |El número actual de dedicado de nodos de ejecución dedicados. |
| $CurrentLowPriorityNodes |Número actual de nodos de ejecución de prioridad baja, incluidos todos los nodos adelantados. |
| $PreemptedNodeCount | Número de nodos del grupo que se encuentran en estado adelantado. |

> [!TIP]
> Las variables de solo lectura definidas por el servicio que se muestran en la tabla anterior son *objetos* que proporcionan distintos métodos para obtener acceso a los datos asociados a cada uno de ellos. Para obtener más información, consulte la sección [Obtención de datos de ejemplo](#getsampledata), descrita más adelante en este artículo.
>
>

## <a name="types"></a>Tipos
Estos son los tipos que se admiten en las fórmulas:

* double
* doubleVec
* doubleVecList
* string
* timestamp: timestamp es una estructura compuesta que contiene los siguientes miembros:

  * year
  * mes (1-12)
  * día (1-31)
  * día de la semana (en formato de número; por ejemplo, 1 para lunes)
  * hora (en formato de número de 24 horas; por ejemplo, 13 significa 1 p.m.)
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
Estas operaciones se permiten en los tipos que constan en la sección anterior.

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
Las fórmulas de escalado automático actúan en datos de métricas (muestras) proporcionados por el servicio Lote. Una fórmula aumenta o reduce el tamaño del grupo según los valores que obtiene del servicio. Las variables definidas por el servicio descritas anteriormente son objetos que proporcionan varios métodos para obtener acceso a los datos que están asociados a ese objeto. Por ejemplo, la siguiente expresión muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Description |
| --- | --- |
| GetSample() |El método `GetSample()` devuelve un vector de muestras de datos.<br/><br/>Un ejemplo tiene un valor de 30 segundos de datos de métrica. En otras palabras, las muestras se obtienen cada 30 segundos. No obstante, tal como se mencionó anteriormente, hay un retraso entre cuándo se recopila una muestra y cuándo está disponible para una fórmula. Por lo tanto, puede que no todos los ejemplos durante un período de tiempo determinado estén disponibles para la evaluación a través de una fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica el número de muestras que se obtienen a partir de las muestras recogidas más recientes.<br/><br/>`GetSample(1)` devuelve la última muestra disponible. Para métricas como `$CPUPercent`, sin embargo, esto no debe usarse porque es imposible saber *cuándo* se recopiló la muestra. Puede ser reciente o, debido a problemas del sistema, puede ser mucho más antiguo. En estos casos es mejor usar un intervalo de tiempo como se muestra a continuación.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica un período de tiempo para recopilar datos de muestra. Opcionalmente, también especifica el porcentaje de muestras que deben estar disponibles en el marco de tiempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` debería devolver 20 muestras si todas las muestras de los últimos 10 minutos están presentes en el historial de CPUPercent. Si el último minuto del historial no estaba disponible, solo se devolverán, no obstante, 18 muestras. En este caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` daría error porque solo el 90 por ciento de las muestras están disponibles.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` se realizaría correctamente.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica un período de tiempo para recopilar datos, con una hora de inicio y una hora de finalización.<br/><br/>Como se mencionó anteriormente, hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para una fórmula. Tenga en cuenta este retraso a la hora de usar el método `GetSample`. Consulte `GetSamplePercent` a continuación. |
| GetSamplePeriod() |Devuelve el período de las muestras tomadas en un conjunto de datos de muestras históricos. |
| Count() |Devuelve el número total de ejemplos en el historial de métrica. |
| HistoryBeginTime() |Devuelve la marca de tiempo de la muestra de datos disponible más antigua para la métrica. |
| GetSamplePercent() |Devuelve el porcentaje de muestras que están disponibles para un intervalo de tiempo dado. Por ejemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Debido a que se produce un error en el método `GetSample` si el porcentaje de muestras devueltas es inferior al valor de `samplePercent` especificado, puede utilizar el método `GetSamplePercent` para comprobarlo primero. A continuación, puede realizar una acción alternativa si no hay suficientes muestras presentes, sin detener la evaluación de escalado automático. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Ejemplos, porcentaje de ejemplo y el método *GetSample()*
La operación principal de una fórmula de escalado automático es la obtención de datos de métricas de tareas y recursos y el ajuste posterior del tamaño de grupo según esos datos. Por lo tanto, es importante tener una idea clara de cómo interactúan las fórmulas de escalado automático con los datos de métricas (muestras).

**Ejemplos**

El servicio de Batch toma periódicamente ejemplos de métricas de tareas y recursos y los pone a disposición de las fórmulas de escalado automático. El servicio Lote graba estas muestras cada 30 segundos. Pero suele producirse un retraso entre el momento en el que se grabaron esas muestras y el momento en el que se ponen a disposición de las fórmulas de escalado automático y estas pueden leer aquellas. Además, debido a diversos factores como problemas con la red o de infraestructura, es posible que las muestras no se hayan grabado para un intervalo en concreto.

**Porcentaje de muestras**

Al pasar `samplePercent` al método `GetSample()` o al llamar al método `GetSamplePercent()`, _porcentaje_ hace referencia a una comparación entre el número posible total de ejemplos que graba el servicio de Batch y el número de muestras que están disponibles para la fórmula de escalado automático.

Echemos un vistazo a un intervalo de tiempo de 10 minutos como ejemplo. Dado que las muestras se graban cada 30 segundos, el número total máximo de muestras que Batch graba en un intervalo de tiempo de 10 minutos habría sido de 20 muestras (2 por minuto). Pero, debido a la latencia inherente del mecanismo de informes y a otros problemas existentes en Azure, podría haber solo 15 muestras disponibles para leer la fórmula de escalado automático. Así, por ejemplo, durante ese período de 10 minutos, solo el 75 % del número total de ejemplos que se graban podría estar disponible para la fórmula.

**GetSample() e intervalos de muestra**

Las fórmulas de escalado automático van a aumentar y reducir los grupos (al agregar o quitar nodos). Dado que los nodos cuestan dinero, desea asegurarse de que las fórmulas utilizan un método de análisis inteligente que se basa en un número de datos suficiente. Por lo tanto, recomendamos que use un análisis de tipo tendencias en las fórmulas. Este tipo aumenta y reduce los grupos basándose en un intervalo de ejemplos recopilados.

Para ello, use `GetSample(interval look-back start, interval look-back end)` para devolver un vector de ejemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Cuando Batch evalúe la línea anterior, devolverá un intervalo de muestras como vector de valores. Por ejemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Una vez recopilado el vector de ejemplos, puede usar funciones como `min()`, `max()` y `avg()` para derivar valores significativos del intervalo recopilado.

Para mayor seguridad, puede forzar el error en una evaluación de fórmula si menos de un determinado porcentaje de ejemplos está disponible para un período de tiempo determinado. Al forzar el error en una evaluación de fórmula, indica a Batch que deje de evaluar la fórmula si el porcentaje de muestras especificado no está disponible. En este caso, no se efectúa ningún cambio en el tamaño del grupo. Para especificar un porcentaje necesario de ejemplos para que la evaluación se realice correctamente, especifíquelo como el tercer parámetro de `GetSample()`. En este caso, se especifica un requisito del 75 por ciento de muestras:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Dado que puede haber un retraso en la disponibilidad de muestras, es importante especificar siempre un intervalo de tiempo con una hora de inicio retrospectiva cuya anterioridad sea superior a un minuto. Las muestras tardan aproximadamente un minuto en propagarse por el sistema, por lo que puede que las muestras del rango `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` no estén disponibles. De nuevo, puede utilizar el parámetro de porcentaje de `GetSample()` para forzar un requisito de porcentaje de ejemplos concreto.

> [!IMPORTANT]
> Es **muy recomendable** que **evite confiar *solo* en**  en las fórmulas de escalado automático`GetSample(1)`. Esto se debe a que `GetSample(1)` dice básicamente al servicio Batch "dame la muestra más reciente que tengas, independientemente de cuánto tiempo hace que la tienes". Puesto que es solo una muestra única, y puede ser una muestra más antigua, no puede ser representativa de la imagen más grande del estado reciente de la tarea o el recurso. Si usa `GetSample(1)`, asegúrese de que forma parte de una instrucción más grande y no solo el punto de datos en el que se basa la fórmula.
>
>

## <a name="metrics"></a>Métricas
Puede usar tanto métricas de recurso como de tarea al definir una fórmula. El número de nodos dedicados de destino se ajusta en el grupo basándose en los datos de métricas que obtenga y evalúe. Consulte la sección [Variables](#variables) más arriba para más información sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>Las métricas de recurso se basan en la CPU, el ancho de banda y el uso de la memoria de los nodos de ejecución, así como en el número de nodos.</p>
        <p> Estas variables definidas por el servicio se usan para realizar ajustes basados en el número de nodos:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$preemptedNodeCount</li>
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
    <td><p>Las métricas de tarea se basan en el estado de las tareas, como Activo, Pendiente y Completado. Las siguientes variables definidas por el servicio se usan para realizar ajustes de tamaño de grupo basados en las métricas de tarea:</p>
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
Para construir una fórmula de escalado automático, es preciso formar instrucciones que usen los componentes anteriores y luego combinar esas instrucciones en una fórmula completa. En esta sección vamos a crear una fórmula de escalado automático de ejemplo que puede tomar algunas decisiones reales de escalado.

En primer lugar, vamos a definir los requisitos de nuestra nueva fórmula de escalado automático. La fórmula debe:

1. Aumentar el número objetivo de nodos de ejecución dedicados en un grupo si el uso de la CPU es alto.
2. Reducir el número objetivo de nodos de ejecución dedicados en un grupo si el uso de la CPU es bajo.
3. Restringir siempre el número máximo de nodos dedicados a 400.

Para aumentar el número de nodos durante un uso elevado de la CPU, definimos la instrucción que rellena una variable definida por el usuario (`$totalDedicatedNodes`) con un valor que representa un 110 por ciento del número de nodos dedicados del destino actual, pero solo si el uso medio mínimo de la CPU en los 10 últimos minutos era superior al 70 por ciento. En caso contrario, use el valor del número actual de nodos dedicados.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Para *reducir* el número de nodos dedicados durante un uso bajo de la CPU, la siguiente instrucción de nuestra fórmula establece la misma variable `$totalDedicatedNodes` en el 90 por ciento del número objetivo actual de nodos dedicados si el uso medio de la CPU en los últimos 60 minutos estaba por debajo del 20 por ciento. De lo contrario, use el valor actual de `$totalDedicatedNodes` que se rellenamos en la instrucción anterior.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Ahora limite el número objetivo de nodos de ejecución dedicados a un máximo de 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Esta es la fórmula completa:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Crear un grupo habilitado para el escalado automático con .NET

Para crear un grupo con el escalado automático habilitado en. NET, siga estos pasos:

1. Crear el grupo con [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Establecer la propiedad [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled) en `true`.
3. Establecer la propiedad [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) con la fórmula de escalado automático.
4. (Opcional) Establecer la propiedad [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (el valor predeterminado es 15 minutos).
5. Confirmar el grupo con [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) o [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync).

El fragmento de código siguiente crea un grupo habilitado para el escalado automático en .NET. La fórmula de escalado automático del grupo establece el número objetivo de nodos dedicados en 5 los lunes y en 1 los demás días de la semana. El [intervalo de escalado automático](#automatic-scaling-interval) está establecido en 30 minutos. En este y en otros fragmentos de código de C# de este artículo, `myBatchClient` es una instancia inicializada correctamente de la clase [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "small", // single-core, 1.75 GB memory, 225 GB disk
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Al crear un grupo habilitado para el escalado automático, no especifique el parámetro _targetDedicatedComputeNodes_ ni el parámetro _targetLowPriorityComputeNodes_ en la llamada a **CreatePool**. En su lugar, especifique las propiedades **AutoScaleEnabled** y **AutoScaleFormula** en el grupo. Los valores de estas propiedades determinan el número de destino de cada tipo de nodo. Además, para cambiar manualmente el tamaño de un grupo habilitado para el escalado automático (por ejemplo, con [BatchClient.PoolOperations.ResizePoolAsync][net_poolops_resizepoolasync]), primero debe **deshabilitar** el escalado automático en el grupo y, después, cambiar su tamaño.
>
>

Además de Batch .NET, puede usar cualquiera de los demás [SDK de Batch](batch-apis-tools.md#azure-accounts-for-batch-development), [REST de Batch](https://docs.microsoft.com/rest/api/batchservice/), [cmdlets de PowerShell para Batch](batch-powershell-cmdlets-get-started.md) y la [CLI de Batch](batch-cli-get-started.md) para configurar el escalado automático.


### <a name="automatic-scaling-interval"></a>Intervalo de escalado automático
De forma predeterminada, el servicio de Batch ajusta el tamaño de un grupo según su fórmula de escalado automático cada 15 minutos. Este intervalo se puede configurar con las siguientes propiedades de grupo:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (.NET para Batch)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API de REST)

Los intervalos mínimo y máximo son cinco minutos y 168 horas, respectivamente. Si se especifica un intervalo que se sitúa fuera de este margen, el servicio de Batch devolverá un error de solicitud incorrecta (400).

> [!NOTE]
> Actualmente, el escalado automático no está pensado como respuesta inmediata a los cambios, sino para ajustar el tamaño del grupo gradualmente mientras ejecuta una carga de trabajo.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitación del escalado automático en un grupo existente

Cada SDK de Batch proporciona un método para habilitar el escalado automático. Por ejemplo:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Activar la escala automática en un grupo de servidores][rest_enableautoscale] (API de REST)

Al habilitar el escalado automático en un grupo existente, tenga en cuenta las siguientes cuestiones:

* Si el escalado automático está deshabilitado en el grupo a la hora de emitir la solicitud para habilitar el escalado automático, debe especificar una fórmula de escalado automático válida al emitir la solicitud. También puede especificar un intervalo de evaluación de escalado automático. Si no especifica un intervalo, se usa el valor predeterminado de 15 minutos.
* Si el escalado automático está habilitado en el grupo, puede especificar una fórmula de escalado automático, un intervalo de evaluación o ambas cosas. Debe especificar al menos una de estas propiedades.

  * Si especifica un nuevo intervalo de evaluación de escalado automático, la programación de evaluación existente se detiene y se inicia una nueva. La hora de inicio de la nueva programación es la hora a la que se emitió la solicitud para habilitar el escalado automático.
  * Si se omite la fórmula de escalado automático o el intervalo de evaluación, el servicio Batch sigue usando el valor actual de dicha configuración.

> [!NOTE]
> Si especificó valores para los parámetros *targetDedicatedComputeNodes* o *targetLowPriorityComputeNodes* del método **CreatePool** al crear el grupo en .NET, o bien para los parámetros comparables en otro lenguaje, dichos valores se omitirán cuando se evalúe la fórmula de escalado automático.
>
>

Este fragmento de código de C# usa la biblioteca [.NET para Batch][net_api] para habilitar el escalado automático en un grupo existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Actualización de una fórmula de escalado automático

Para actualizar la fórmula en un grupo existente habilitado para el escalado automático, vuelva a llamar a la operación para habilitar el escalado automático con la nueva fórmula. Por ejemplo, si el escalado automático ya está habilitado en `myexistingpool` cuando se ejecuta el siguiente código de .NET, su fórmula de escalado automático se sustituirá por el contenido de `myNewFormula`.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Actualización del intervalo de escalado automático

Para actualizar el intervalo de evaluación de escalado automático en un grupo existente habilitado para el escalado automático, vuelva a llamar a la operación para habilitar el escalado automático con el nuevo intervalo. Por ejemplo, para establecer el intervalo de evaluación de escalado automático en 60 minutos para un grupo que ya tiene habilitado el escalado automático en .NET:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Evaluación de una fórmula de escalado automático

Puede evaluar una fórmula antes de aplicarla a un grupo. De esta manera puede probar la fórmula para ver cómo se evalúan sus instrucciones antes de usarla en producción.

Para evaluar una fórmula de escalado automático, primero debe habilitar el escalado automático en el grupo con una fórmula válida. Para probar una fórmula en un grupo que aún no tiene habilitado el escalado automático, use la fórmula de una línea `$TargetDedicatedNodes = 0` la primera vez que habilite el escalado automático. A continuación, use uno de los siguientes métodos para evaluar la fórmula que quiere probar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) o [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Estos métodos .NET de Batch requieren el identificador de un grupo existente y una cadena que contenga la fórmula de escalado automático que se evaluará.

* [Evaluación de una fórmula de escalado automático](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    En esta solicitud de API de REST, especifique el identificador del grupo en el URI y la fórmula de escalado automático en el elemento *autoScaleFormula* del cuerpo de la solicitud. La respuesta de la operación contiene cualquier información de error que pueda relacionarse con la fórmula.

En este fragmento de código de [Batch .NET][net_api] se evalúa una fórmula de escalado automático. Si el grupo no tiene habilitado el escalado automático, lo habilitaremos primero.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = {pool.CurrentDedicatedNodes};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
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
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Una evaluación correcta de la fórmula que se muestra en este fragmento de código genera unos resultados similares a esto:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtención de información sobre la ejecución del escalado automático

Para asegurarse de que la fórmula se ejecuta según lo esperado, le recomendamos que compruebe periódicamente los resultados de las ejecuciones de escalado automático que lleva a cabo Batch en su grupo. Para ello, obtenga (o actualice) una referencia al grupo y examine las propiedades de su última ejecución de escalado automático.

En Batch .NET, la propiedad [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) tiene varias propiedades que proporcionan información sobre la última ejecución de escalado automático efectuada en el grupo:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

En la API de REST, la solicitud para [obtener información sobre un grupo](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) devuelve información sobre el grupo (por ejemplo, información sobre la última ejecución de escalado automático en la propiedad [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool#bk_autrun)).

El siguiente fragmento de código de C# usa la biblioteca Batch .NET para imprimir información sobre la última ejecución de escalado automático en el grupo _myPool_:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Salida de ejemplo del fragmento de código anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Ejemplo de fórmulas de escalado automático
Echemos un vistazo a algunas fórmulas que muestran distintas formas de ajustar la cantidad de recursos de proceso de un grupo.

### <a name="example-1-time-based-adjustment"></a>Ejemplo 1: Ajuste basado en la fecha y hora
Imagínese que quiere ajustar el tamaño del grupo en función de la hora y el día de la semana. En este ejemplo se muestra cómo aumentar o disminuir en consecuencia el número de nodos del grupo.

En primer lugar, la fórmula obtiene la hora actual. Si se trata de un día laborable (1-5) y dentro del horario laboral (de 8 a.m. a 6 p.m.), el tamaño del grupo de destino se establece en 20 nodos. De lo contrario, se establece en 10 nodos.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Ejemplo 2: Ajuste basado en tareas
En este ejemplo, el tamaño del grupo se ajusta en función del número de tareas en la cola. Las cadenas de la fórmula aceptan tanto comentarios como saltos de línea.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Ejemplo 3: Contabilidad para tareas paralelas
Este ejemplo ajusta el tamaño del grupo en función del número de tareas. Esta fórmula también tiene en cuenta el valor de [MaxTasksPerComputeNode][net_maxtasks] que se ha establecido para el grupo. Este enfoque es útil en aquellas situaciones en las que la [ejecución de tareas paralelas](batch-parallel-node-tasks.md) se ha habilitado en el grupo.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Ejemplo 4: Configuración de un tamaño de grupo inicial
En este ejemplo se muestra un fragmento de código de C# con una fórmula de escalado automático que establece el tamaño del grupo en un número de nodos específico durante un período de tiempo inicial. A continuación, se ajusta el tamaño del grupo según el número de tareas activas y en ejecución una vez transcurrido el período de tiempo inicial.

La fórmula en el siguiente fragmento de código:

* Establece el tamaño inicial del grupo en cuatro nodos.
* No ajusta el tamaño del grupo en los 10 primeros minutos del ciclo de vida del mismo.
* Después de 10 minutos, obtiene el valor máximo del número de tareas activas y en ejecución en los últimos 60 minutos.
  * Si ambos valores son 0 (lo que indica que no hay tareas activas o en ejecución en los últimos 60 minutos) el tamaño del grupo se establece en 0.
  * Si cualquier valor es mayor que cero, no hay cambios.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Pasos siguientes
* [Maximizar el uso de recursos de proceso de Lote de Azure con tareas simultáneas de nodo](batch-parallel-node-tasks.md) contiene detalles acerca de cómo se pueden ejecutar varias tareas simultáneamente en los nodos de proceso en el grupo. Además del escalado automático, esta característica puede ayudar a reducir la duración del trabajo para algunas cargas de trabajo, lo que permite ahorrar dinero.
* Para otro ajuste de la eficacia, asegúrese de que la aplicación Lote consulta el servicio Lote de la manera más óptima. Consulte [Creación de consultas para enumerar los recursos de Batch con eficacia](batch-efficient-list-queries.md) para aprender a limitar la cantidad de datos que atraviesan la conexión al consultar el estado de posiblemente miles de nodos de ejecución o tareas.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
