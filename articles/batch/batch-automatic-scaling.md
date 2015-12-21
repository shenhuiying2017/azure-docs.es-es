
<properties
	pageTitle="Escalado automático de nodos de ejecución en un grupo de Lote de Azure | Microsoft Azure"
	description="Habilite el escalado automático en un grupo en la nube para ajustar de forma dinámica el número de nodos de ejecución del grupo."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="12/04/2015"
	ms.author="marsma"/>

# Escalación automática de los nodos de ejecución en un grupo de Lote de Azure

El escalado automático de los nodos de ejecución de un grupo de Lote de Azure es el ajuste dinámico de la potencia de procesamiento que usa su aplicación. Esta facilidad de ajuste ahorra tiempo y dinero. Para más información acerca de los grupos y nodos de ejecución, consulte [Información técnica de Lote de Azure para cargas de trabajo HPC y paralelas a gran escala](batch-technical-overview.md).

El escalado automático se produce cuando se habilita en un grupo y se asocia una fórmula a dicho grupo. La fórmula se utiliza para determinar el número de nodos de ejecución necesario para procesar la aplicación. Actuando sobre las muestras que se recopilan periódicamente, el número de nodos de ejecución disponibles en el grupo se ajusta cada 15 minutos según la fórmula asociada.

El escalado automático se puede establecer al crear un grupo, o bien se puede hacer más adelante en un grupo existente. La fórmula también se puede actualizar en un grupo en el que previamente se habilitó el escalado automático. Siempre es aconsejable evaluar cualquier fórmula antes de asignarla a un grupo y es importante supervisar el estado de las ejecuciones de escalado automático, pero estos temas se tratarán a continuación.

> [AZURE.NOTE]Cada cuenta de Lote de Azure se limita a un número máximo de nodos de ejecución que puede utilizarse para su procesamiento. El sistema creará nodos solo hasta ese límite y, por consiguiente, no es posible alcanzar los números objetivo que especifica una fórmula.

## Escalado automático de recursos de proceso

La fórmulas de escalado que se definan determinarán el número de nodos de ejecución disponibles en un grupo para el siguiente intervalo de procesamiento. Una fórmula de escalado automático no es más que un valor de cadena asignado al elemento [autoScaleFormula](https://msdn.microsoft.com/library/azure/dn820173.aspx) del cuerpo de una solicitud (API de REST) o a la propiedad [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) (API de .NET) de un grupo. Dicha cadena no puede superar los 8 KB y puede incluir hasta 100 instrucciones separadas por punto y coma, y saltos de línea y comentarios.

Las instrucciones en una fórmula son expresiones de forma libre. Pueden incluir variables definidas por el sistema, variables definidas por el usuario, valores constantes y operaciones admitidas en estas variables o constantes:

	VAR = Expression(system-defined variables, user-defined variables);

Las fórmulas complejas se crean mediante el uso de varias instrucciones y variables:

	VAR₀ = Expression₀(system-defined variables);
	VAR₁ = Expression₁(system-defined variables, VAR₀);

> [AZURE.NOTE]Las fórmulas de escalado automática constan de variables, tipos, operaciones y funciones de la API de [REST de Azure Batch](https://msdn.microsoft.com/library/azure/dn820158.aspx). Se usan en las cadenas de fórmula aunque se trabaje con la biblioteca de [.NET de Lote de Azure](https://msdn.microsoft.com/library/azure/mt348682.aspx).

### Variables

En las fórmulas se pueden usar tanto variables definidas por el sistema como variables definidas por el usuario.

*Obtenga* y *establezca* el valor de estas **variables definidas por el sistema** para administrar el número de nodos de ejecución de un grupo.

<table>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>El número de destino de nodos de ejecución dedicados para el grupo. El valor se puede cambiar según el uso real de las tareas.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>La acción que se produce cuando se quitan los nodos de ejecución de un grupo. Los valores posibles son:
      <br/>
      <ul>
        <li><p><b>requeue</b>: finalizar las tareas inmediatamente y volverlas a poner en la cola de trabajos para que se vuelvan a programar.</p></li>
        <li><p><b>terminate</b>: finalizar las tareas inmediatamente y quitarlas de la cola de trabajo.</p></li>
        <li><p><b>taskcompletion</b>: esperar a que las tareas en ejecución actualmente finalicen y, a continuación, quitar el nodo del grupo.</p></li>
        <li><p><b>retaineddata</b>: esperar a que todos los datos que se conservan en la tarea local en el nodo se limpien antes de quitar el nodo de grupo.</p></li>
      </ul></td>
   </tr>
</table>

*Obtenga* el valor de estas **variables definidas por el sistema** para realizar ajustes según las métricas de los nodos de ejecución de los ejemplos. Estas variables son de solo lectura.

<table>
  <tr>
    <th>Variable</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>El porcentaje medio de uso de CPU</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>El número de segundos consumidos</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>La media de megabytes usados</td>
  <tr>
    <td>$DiskBytes</td>
    <td>La media de gigabytes usados en los discos locales</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>El número de bytes leídos</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>El número de bytes escritos</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>El número de operaciones de disco de lectura realizadas</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>El número de operaciones de disco de escritura realizadas</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>El número de bytes entrantes</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>El número de bytes salientes</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>El número de nodos de ejecución</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>El número de tareas que se encuentran en estado activo</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>El número de tareas en un estado de ejecución</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>El número de tareas que finalizó correctamente</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>El número de tareas erróneas</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>El número actual de dedicado de nodos de ejecución dedicados</td>
  </tr>
</table>

### Tipos

Estos son los **tipos** que se admiten en las fórmulas.

- double
- doubleVec
- cadena
- timestamp: timestamp es una estructura compuesta que contiene los siguientes miembros:
	- year
	- mes (1-12)
	- día (1-31)
	- weekday (en formato de número; p.ej., 1 para lunes)
	- hour (en formato de número de 24 horas; p.ej., 13 significa 1 p.m.)
	- minuto (00-59)
	- segundo (00-59)
- timeinterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

### Operaciones

Estas **operaciones** se permiten en los tipos enumerados arriba.

<table>
  <tr>
    <th>Operación</th>
    <th>Operadores permitidos</th>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operator> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operator> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operator>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operator>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operator> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operator> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operator> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operator> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>test double only (non-zero is true, zero is false)</td>
    <td>? :</td>
  </tr>
</table>

### Funciones

Estas **funciones** predefinidas están disponibles para definir fórmulas de escalado automático.

<table>
  <tr>
    <th>Función</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>El valor medio de todos los valores de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>La longitud del vector creado a partir de doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Base logarítmica 2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Base logarítmica de componentes 2 Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Logaritmo natural.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Base logarítmica de componentes 2 Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Base logarítmica 10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Base logarítimica de componentes 10. Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double log(double).</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>El valor máximo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>El valor mínimo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>La norma de dos del vector creado a partir d doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>El elemento percentil del vector v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Un valor aleatorio entre 0,0 y 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>La diferencia entre los valores máximo y mínimo en doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>La desviación de ejemplo estándar de los valores en doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Detenga la evaluación de la expresión de la escalación automática.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>La suma de todos los componentes de doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>La marca de tiempo de la hora actual si no se pasan los parámetros o la marca de hora de la cadena dateTime si se pasó. Los formatos de dateTime compatibles son W3CDTF y RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>El valor del elemento en la ubicación i en el vector v con el índice inicial de cero.</td>
  </tr>
</table>

Algunas de las funciones descritas en la tabla anterior pueden aceptar una lista como argumento. La lista separada por comas es cualquier combinación de *double* y *doubleVec*. Por ejemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

El valor *doubleVecList* se convierte en un *doubleVec* individual antes de la evaluación. Por ejemplo, `v = [1,2,3]`, llamar a `avg(v)` es equivalente a llamar a `avg(1,2,3)` y llamar `avg(v, 7)` es equivalente a llamar a `avg(1,2,3,7)`.

### Obtención de datos de ejemplo

Las variables definidas por el sistema descritas arriba son objetos que proporcionan métodos para obtener acceso a los datos asociados. Por ejemplo, la siguiente expresión muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

Estos métodos se pueden usar para obtener datos de ejemplo.

<table>
  <tr>
    <th>Método</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Devuelve el número total de ejemplos en el historial de métrica.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Devuelve un vector de ejemplos de datos.
	<p>Un ejemplo tiene un valor de 30 segundos de datos de métrica. Es decir, los ejemplos se recopilan cada 30 segundos, pero según se indica a continuación, hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para la fórmula. Por lo tanto, puede que no todos los ejemplos durante un período de tiempo determinado estén disponibles para la evaluación a través de una fórmula.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: especifica el número de ejemplos a obtener a partir de los ejemplos más recientes recogidos.</p>
				  <p>GetSample (1) devuelve el último ejemplo disponible. Para las métricas como $CPUPercent, sin embargo, esto no debe usarse porque es imposible saber <em>cuándo</em> se recopiló el ejemplo: puede ser reciente o, debido a problemas del sistema, puede ser mucho más antiguo. En estos casos es mejor usar un intervalo de tiempo como se muestra a continuación.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: especifica un plazo de tiempo para recopilar datos de ejemplo y especifica opcionalmente el porcentaje de ejemplos que tiene que estar disponible en el plazo de tiempo solicitado.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em>, debería devolver 20 ejemplos si todos los ejemplos para los últimos 10 minutos están presentes en el historial de CPUPercent. Si el último minuto de historial no estaba disponible, sin embargo, solo se devolverán 18 ejemplos, en cuyo caso:<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample (TimeInterval_Minute * 10, 95)</em> produciría un error porque solo el 90 % de los ejemplos están disponible, y<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample (TimeInterval_Minute * 10, 80)</em> se realizaría correctamente.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: especifica un período para recopilar datos con una hora de inicio y una hora de finalización.</p></li></ul>
		  <p>Como se mencionó anteriormente, hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para una fórmula. Esto debe tenerse en cuenta cuando se usa el método <em>GetSample</em>, consulte <em>GetSamplePercent</em> a continuación.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Devuelve el período de los ejemplos tomados en un conjunto de datos de ejemplo históricos.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Devuelve la marca de tiempo del ejemplo de datos disponible más antiguo para la métrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Devuelve el porcentaje de ejemplos que un historial tiene actualmente para un intervalo de tiempo determinado. Por ejemplo:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Puesto que se produce un error en el método GetSample si el porcentaje de muestra devuelto es inferior al samplePercent especificado, puede usar el método GetSamplePercent para realizar primero la comprobación y, a continuación, realizar una acción alternativa cuando no haya suficientes muestras sin detener su evaluación del escalado automático.</p></td>
  </tr>
</table>

### Métricas

Al definir una fórmula puede usar **métricas** de recurso y de tarea, y estas métricas puede usarse para administrar los nodos de ejecución de un grupo.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>Recurso</td>
    <td><p>Las métricas de recurso se basan en el uso de la CPU, el uso del ancho de banda, el uso de la memoria y el número de nodos de ejecución. Estas variables definidas por el sistema (descritas en **Variables**) se usan en las fórmulas para administrar los nodos de ejecución de un grupo:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Estas variables definidas por el sistema se usan para realizar ajustes basados en las métricas de recurso del nodo:</p>
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
    <td>Tarea</td>
    <td><p>Basada en el estado de las tareas, como Activo, Pendiente y Completado.</p>
    <p>Estas variables definidas por el sistema se usan para realizar ajustes basados en las métricas de tarea:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p></td>
  </tr>
</table>

## Compilación de una fórmula de escalado automático

Para construir una fórmula de escalado automático, es preciso formar instrucciones mediante los componentes anteriores y combinar dichas instrucciones en una fórmula completa. Por ejemplo, aquí construimos una fórmula, para lo que primero definimos los requisitos para una fórmula que:

1. Aumentará el número de nodos de ejecución de un grupo si el uso de la CPU es alto.
2. Reducirá el número de nodos de ejecución de un grupo cuando el uso de la CPU es bajo.
3. Limitará siempre el número máximo de nodos a 400.

Para que *aumente* el número de nodos durante un uso elevado de la CPU, definimos la instrucción que rellena una variable definida por el usuario ($TotalNodes) con un valor que es un 110 % del número de nodos del destino actual si el uso medio mínimo de la CPU en los 10 últimos minutos es superior al 70 %:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;

La siguiente instrucción establece la misma variable al 90 % del número de nodos de destino actual si el uso medio de la CPU en los 60 últimos minutos estaba *por debajo del* 20 %, lo que reduce el número destino cuando el uso de la CPU es bajo. Tenga en cuenta que esta instrucción también hace referencia a la variable definida por el usuario *$TotalNodes* de la instrucción anterior.

	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;

Ahora limite el número de destino de los nodos de ejecución dedicados a un **máximo** de 400:

	$TargetDedicated = min(400, $TotalNodes)

Esta es la fórmula completa:

	$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
	$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
	$TargetDedicated = min(400, $TotalNodes)

## Creación de un grupo con el escalado automático habilitado

Para habilitar el escalado automático al crear un grupo, use una de las técnicas siguientes:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx): este cmdlet de Azure PowerShell usa el parámetro AutoScaleFormula para especificar la fórmula de escalado automático.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx): después de llamar a este método .NET para crear un grupo, establecerá las propiedades [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) y [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) del grupo para habilitar el escalado automático.
- [Agregar un grupo a una cuenta](https://msdn.microsoft.com/library/azure/dn820174.aspx): los elementos enableAutoScale y autoScaleFormula se usan en esta solicitud de API de REST para configurar el escalado automático del grupo al crearlo.

> [AZURE.NOTE]Si configura el escalado automático cuando el grupo se crea mediante una de estás técnicas, no se especifica el parámetro *targetDedicated* para el grupo (ni debe especificarse) al crearlo. Tenga en cuenta también que si desea cambiar manualmente el tamaño de un grupo con el escalado automático habilitado (por ejemplo, con [BatchClient.PoolOperations.ResizePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx)), primero tiene que deshabilitar el escalado automático en el grupo y, a continuación, cambiar el tamaño.

El fragmento de código siguiente muestra la creación de [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) con el escalado automático habilitado mediante la biblioteca de [.NET de Lote](https://msdn.microsoft.com/library/azure/mt348682.aspx) cuya fórmula establece el número de nodos de destino en 5 los lunes y en 1 cada dos días el resto de la semana. En el fragmento, "myBatchClient" es una instancia de [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) inicializada correctamente.

		CloudPool pool myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
		pool.AutoScaleEnabled = true;
		pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
		pool.Commit();

## Habilitación de la escalación automática después de crear un grupo

Si ya configuró un grupo con un número específico de nodos de ejecución mediante el parámetro *targetDedicated*, puede actualizar el grupo existente posteriormente para que el escalado se realice de forma automática. Realice este procedimiento de una de estas maneras:

- [BatchClient.PoolOperations.EnableAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx): este método .NET requiere el identificador de un grupo existente y la fórmula de escalado automático que se va a aplicar al grupo.
- [Habilitar el escalado automático en un grupo](https://msdn.microsoft.com/library/azure/dn820173.aspx): esta API de REST requiere el identificador del grupo existente en el URI y la fórmula de escalado automática en el cuerpo de la solicitud.

> [AZURE.NOTE]Si se especificó un valor para el parámetro *targetDedicated* cuando se creó el grupo, se ignora cuando se evalúa la fórmula de escalado automático.

Este fragmento de código muestra cómo habilitar el escalado automático mediante la biblioteca de [.NET de Lote](https://msdn.microsoft.com/library/azure/mt348682.aspx). Tenga en cuenta que tanto la habilitación como la actualización de la fórmula en un grupo existente usan el mismo método. Por lo tanto, esta técnica *actualizaría* la fórmula en el grupo especificado si el escalado automático ya se había habilitado. Este fragmento asume que "myBatchClient" es una instancia inicializada correctamente de [BatchClient](http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx) y "mypool" es el identificador de un [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx) existente.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Evaluación de la fórmula de escalación automática

Siempre es recomendable evaluar una fórmula antes de usarla en su aplicación. La fórmula se evalúa realizando una "ejecución de prueba" de la misma en un grupo existente. Para ello, use:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) o [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx): estos métodos .NET requieren el identificador de un grupo existente y la cadena que contiene la fórmula del escalado automático. Los resultados de la llamada aparecen en una instancia de la clase [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Evaluar una fórmula de escalado automático](https://msdn.microsoft.com/library/azure/dn820183.aspx): en esta solicitud de la API de REST, el identificador del grupo se especifica en el URI y la fórmula de escalado automático se especifica en el elemento *autoScaleFormula* del cuerpo de la solicitud. La respuesta de la operación contiene cualquier información de error que pueda relacionarse con la fórmula.

> [AZURE.NOTE]Para evaluar una fórmula de escalado automático, primero es preciso habilitar el escalado automático en el grupo con una fórmula válida.

En este fragmento de código se evalúa una fórmula antes de aplicarla al [CloudPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx), para lo que se usa la biblioteca de [.NET de Lote](https://msdn.microsoft.com/library/azure/mt348682.aspx).

		// First obtain a reference to the existing pool
		CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

		// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
		if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
		{
			// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
			string myFormula = @"
				$CurTime=time();
				$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
				$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
				$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
				$TargetDedicated=$IsWorkingWeekdayHour?20:10;
			";

			// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
			// the pool.
			AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

			if (eval.AutoScaleRun.Error == null)
			{
				// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
				// variable as evaluated by the the autoscaling formula.
				Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

				// Apply the formula to the pool since it evaluated successfully
				client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
			}
			else
			{
				// Evaluation failed, output the message associated with the error
				Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
			}
		}

La evaluación correcta de la fórmula en este fragmento generará una salida similar a la siguiente:

		AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0

## Obtención de información sobre las ejecuciones de escalado automático

La comprobación periódica de los resultados de las ejecuciones de escalado automático que debe realizarse en una fórmula se realiza según lo esperado. Realice este procedimiento de una de estas maneras:

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx): si se usa la biblioteca .NET, esta propiedad de un grupo proporciona una instancia de la clase [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) que proporciona las siguientes propiedades de la última ejecución del escalado automático:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Obtener información sobre un grupo](https://msdn.microsoft.com/library/dn820165.aspx): esta solicitud de API de REST devuelve información acerca del grupo, lo que incluye la última ejecución del escalado automático.

## Fórmulas de ejemplo

Echemos un vistazo a algunos ejemplos que muestran varias de las formas en que pueden usarse las fórmulas para escalar automáticamente los recursos de proceso de un grupo.

### Ejemplo 1

Quizás desee ajustar el tamaño de un grupo según el día de la semana y la hora del día, y aumentar o reducir el número de nodos del grupo en consecuencia:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

En primer lugar, esta fórmula obtiene la hora actual. Si se trata de un día laborable (1-5) y dentro del horario laboral (8 a.m. a 6 p.m.), el tamaño del grupo de destino se establece en 20 nodos. De lo contrario, el tamaño del grupo se establece en 10 nodos.

### Ejemplo 2

En este ejemplo, el tamaño del grupo se ajusta en función del número de tareas en la cola. Tenga en cuenta que las cadenas de la fórmulas aceptan tanto comentarios como saltos de línea.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Ejemplo 3

Otro ejemplo que ajusta el tamaño del grupo en función del número de tareas, esta fórmula también tiene en cuenta el valor de [MaxTasksPerComputeNode](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx) establecido para el grupo. Esto es especialmente útil en aquellas situaciones en que se desea la ejecución de tareas paralelas en nodos de ejecución.

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Ejemplo 4

Este ejemplo muestra una fórmula de escalado automático que establece el tamaño de grupo en una cierta cantidad de nodos durante un período de tiempo inicial, y después ajusta el tamaño del grupo en función del número de tareas en ejecución y activas, una vez transcurrido el período de tiempo inicial.

```
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

La fórmula en el fragmento de código anterior tiene las siguientes características:

- Establece el tamaño inicial del grupo en 4 nodos
- No ajusta el tamaño del grupo dentro de los primeros 10 minutos del ciclo de vida del mismo
- Después de 10 minutos, obtiene el valor máximo del número de tareas activas y en ejecución en los últimos 60 minutos
  - Si ambos valores son 0 (lo que indica que no hay tareas activas o en ejecución en los últimos 60 minutos) el tamaño del grupo se establece en 0
  - Si cualquier valor es mayor que cero, no hay cambios

## Pasos siguientes

1. Para evaluar exhaustivamente la eficiencia de una aplicación, es posible que tenga que acceder a un nodo de ejecución. Para aprovechar el acceso remoto, debe agregarse una cuenta de usuario al nodo al que desea obtener acceso y debe recuperarse un archivo RDP de dicho nodo.
    - Agregue la cuenta de usuario de una de estas maneras:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): este cmdlet de PowerShell toma el nombre del grupo, el nombre del nodo de ejecución, el nombre de la cuenta y la contraseña como parámetros.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx): este método .NET crea una instancia de la clase [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) en la que se pueden establecer el nombre de la cuenta y la contraseña del nodo de ejecución y, a continuación, se llama a [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) en la instancia para crear el usuario en dicho nodo.
        * [Agregar una cuenta de usuario a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): el nombre del grupo y el nodo de ejecución se especifican en el URI y el nombre de la cuenta y la contraseña se envían al nodo en el cuerpo de esta solicitud de API de REST.
    - Obtención del archivo RDP:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx): este método .NET requiere el identificador del grupo, el identificador del nodo y el nombre del archivo RDP que se va a crear.
        * [Obtener un archivo de protocolo de escritorio remoto de un nodo](https://msdn.microsoft.com/library/dn820120.aspx): esta solicitud de API de REST requiere el nombre del grupo y el nombre del nodo de ejecución. La respuesta incorpora el contenido del archivo RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): este cmdlet de PowerShell obtiene el archivo RDP del nodo de ejecución especificado y lo guarda en la ubicación del archivo especificada o en una transmisión.
2.	Algunas aplicaciones generan grandes cantidades de datos que pueden ser difíciles de procesar. Una manera de resolver esto es a través de una [consulta de lista eficiente](batch-efficient-list-queries.md).

<!---HONumber=AcomDC_1210_2015-->