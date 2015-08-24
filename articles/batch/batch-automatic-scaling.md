
<properties
	pageTitle="Escalación automática de los nodos de ejecución en un grupo de Lote de Azure"
	description="La escalación automática se realiza si se habilita en un grupo y se asocia una fórmula al grupo que se usa para calcular el número de nodos de ejecución que se necesita para procesar la aplicación."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/05/2015"
	ms.author="davidmu"/>

# Escalación automática de los nodos de ejecución en un grupo de Lote de Azure

La escalación automática de nodos de ejecución en un grupo de Lote de Azure es un ajuste dinámico de la capacidad de procesamiento que usa su aplicación. Esta facilidad de ajuste ahorra tiempo y dinero. Para obtener más información acerca de los grupos y nodos de ejecución, consulte [Introducción técnica a Lote de Azure](batch-technical-overview.md).

La escalación automática se produce cuando se habilita en un grupo y una fórmula está asociada al grupo. La fórmula se utiliza para determinar el número de nodos de ejecución necesario para procesar la aplicación. Puede establecer la escalación automática cuando se crea un grupo, o puede hacerlo más adelante en un grupo existente. La fórmula también se puede actualizar en un grupo donde se habilitó el escalado automático.

Cuando se habilita el escalado automático, el número de nodos de ejecución disponibles se ajusta cada 15 minutos, según la fórmula. La fórmula actúa en los ejemplos que se recopilan periódicamente pero hay un retraso entre cuando se recopila un ejemplo y cuando está disponible para la fórmula. Esto debe tenerse en cuenta cuando se usa el método GetSample que se describe a continuación.

Siempre es una buena práctica evaluar la fórmula antes de asignarla a un grupo, y es importante supervisar el estado de las ejecuciones de escalación automáticas.

> [AZURE.NOTE]Cada cuenta de Lote de Azure se limita a un número máximo de nodos de ejecución que puede utilizarse para su procesamiento. El sistema crea los nodos hasta ese límite y no puede tener acceso a los números de destino especificados por una fórmula.

## Definición de la fórmula

La fórmula que defina se utiliza para decidir el número de nodos de ejecución disponibles en el grupo para el siguiente intervalo de procesamiento. La fórmula es una cadena que no puede superar los 8 KB de tamaño y puede incluir hasta 100 instrucciones separadas por punto y coma.

Las instrucciones en una fórmula son expresiones de forma libre. Pueden incluir cualquier variable definidas por el sistema, variables definidas por el usuario, valores constantes y operaciones admitidas en estas variables o constantes:

	VAR = Function(System defined variables, user-defined variables);

Es posible combinar variables para hacer fórmulas complejas:

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Variables

Las variables definidas por el sistema y variables definidas por el usuario pueden utilizarse en una fórmula. Puede establecer el valor de estas variables definidas por el sistema para administrar los nodos de ejecución en un grupo.

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

Solo pueden leer los valores de estas variables definidas por el sistema para realizar ajustes según las métricas de nodos de ejecución en el ejemplo.

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

Estos tipos se admiten en una fórmula:

- double
- doubleVec
- cadena
- timestamp
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

Estas operaciones se permiten en los tipos enumerados anteriormente.

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

Estas funciones predefinidas están disponibles para definir una fórmula de escalación automática.

<table>
  <tr>
    <th>Función</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>El valor medio de todos los valores de doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>La longitud del vector creado a partir de doubleVecList.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Base logarítmica 2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Base logarítmica de componentes 2 Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Logaritmo natural.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Base logarítmica de componentes 2 Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double lg(double).</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Base logarítmica 10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Base logarítimica de componentes 10. Debe pasar explícitamente un vec(double) para un único parámetro doble. En caso contrario, se supone la versión double log(double).</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>El valor máximo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>El valor mínimo de doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>La norma de dos del vector creado a partir d doubleVecList.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>El elemento percentil del vector v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Un valor aleatorio entre 0,0 y 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>La diferencia entre los valores máximo y mínimo en doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>La desviación de ejemplo estándar de los valores en doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Detenga la evaluación de la expresión de la escalación automática.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>La suma de todos los componentes de doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>La marca de tiempo de la hora actual si no se pasan los parámetros o la marca de hora de la cadena dateTime si se pasó. Los formatos de dateTime compatibles son W3CDTF y RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>El valor del elemento en la ubicación i en el vector v con el índice inicial de cero.</td>
  </tr>
</table>

Algunas de las funciones descritas en la tabla pueden aceptar una lista como argumento. La lista separada por comas es cualquier combinación de double y doubleVec. Por ejemplo:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

El valor doubleVecList se convierte en un único doubleVec antes de la evaluación. Por ejemplo, si v = [1,2,3], llamar a avg(v) es equivalente a llamar a avg(1,2,3) y llamar a avg(v, 7) es equivalente a llamar a avg(1,2,3,7).

### Datos de ejemplo

Las variables definidas por el sistema son objetos que proporcionan métodos para tener acceso a los datos asociados. Por ejemplo, la siguiente expresión muestra una solicitud para obtener los últimos cinco minutos de uso de CPU:

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Estos métodos que pueden usarse para obtener datos de ejemplo.

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
    <td><p>Devuelve un vector de ejemplos de datos. Por ejemplo:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b>: especifica el número de ejemplos necesario de los ejemplos más recientes.</p>
				  <p>Un ejemplo tiene un valor de 5 segundos de datos de métrica. GetSample(1) devuelve el último ejemplo disponible, pero para las métricas como $CPUPercent no debe usarlo porque no es posible saber cuándo se recopiló el ejemplo. Puede ser reciente, o debido a los problemas del sistema, puede ser mucho más antiguo. Es mejor usar un intervalo de tiempo como se muestra a continuación.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b>: especifica un plazo de tiempo para recopilar datos de ejemplo y especifica opcionalmente el porcentaje de ejemplos que debe haber en el intervalo solicitado.</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10), debería devolver 200 ejemplos si todos los ejemplos para los últimos 10 minutos están presentes en el historial de CPUPercent. Si el último minuto de historial aún no está presente, se devuelven solo 180 ejemplos.</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80) se realiza con éxito y se produce un error en $CPUPercent.GetSample(TimeInterval_Minute*10,95).</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b>: especifica un período para recopilar datos con una hora de inicio y una hora de finalización.</p></li></ul></td>
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
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b> - Puesto que se produce un error en el método GetSample  si el porcentaje de ejemplos devuelto es inferior al samplePercent especificado, puede usar los métodos GetSamplePercent para realizar primero la comprobación, luego realizar una acción alternativa cuando no haya ejemplos suficientes presentes sin detener la evaluación de la escalación automática.</p></td>
  </tr>
</table>

### Métricas

Las métricas que pueden definirse en una fórmula.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descripción</th>
  </tr>
  <tr>
    <td>Recurso</td>
    <td><p>En función del uso de CPU, el uso de ancho de banda, el uso de memoria y número de nodos de ejecución. Estas variables del sistema descritas anteriormente se utilizan en las fórmulas para administrar los nodos de ejecución en un grupo:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Estas variables del sistema se utilizan para realizar ajustes según las métricas de nodos:</p>
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
    <p>Este ejemplo muestra una fórmula que se usa para establecer el número de nodos de ejecución en el grupo al 110&#160;% del número de destino actual de nodos si el uso medio de la CPU mínimo de los últimos 10 minutos es superior al 70&#160;%:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>Este ejemplo muestra una fórmula que se usa para establecer el número de nodos de ejecución en el grupo al 90&#160;% del número de destino actual de nodos si el uso medio de la CPU de los últimos 60 minutos es inferior al 20&#160;%:</p>
    <p><b>¿totaltvms = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2)? ($CurrentDedicated \ * 0,9): totalTVMs;</b></p>
    <p>Este ejemplo establece el número de destino de los nodos de cálculo dedicado a un máximo de 400:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Tarea</td>
    <td><p>Basada en el estado de las tareas, como Activo, Pendiente y Completado.</p>
    <p>Estas variables del sistema se usan para realizar ajustes según las métricas de tarea:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>Este ejemplo muestra una fórmula que detecta si el 70&#160;% de las muestras se han registrado en los últimos 15 minutos. Si no es así, utiliza el último ejemplo. Intenta aumentar el número de nodos de ejecución para que coincida con el número de tareas activas, con un máximo de 3. Establece el número de nodos a un cuarto del número de tareas activas porque la propiedad MaxTasksPerVM del grupo se establece en 4. También establece la opción de cancelación de asignación en "taskcompletion" para mantener el equipo hasta que finalicen las tareas.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $NodeDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Evaluación de la fórmula de escalación automática

Siempre es una buena práctica evaluar una fórmula antes de usarla en su aplicación. La fórmula se evalúa realizando una ejecución de prueba en un grupo existente. Puede hacer esto mediante uno de estos métodos:

- [IPoolManager.EvaluateAutoScale Method](https://msdn.microsoft.com/library/azure/dn931617.aspx) o [IPoolManager.EvaluateAutoScaleAsync Method](https://msdn.microsoft.com/library/azure/dn931545.aspx): estos métodos .NET requieren el nombre de un grupo existente y la cadena que contiene la fórmula de escalación automática. Los resultados de la llamada aparecen en una instancia de [AutoScaleEvaluation Class](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Evaluar una fórmula de escalación automática](https://msdn.microsoft.com/library/azure/dn820183.aspx) – En esta operación de REST, el nombre de grupo se especifica en el URI y la fórmula de escalación automática se especifica en el elemento autoScaleFormula del cuerpo de solicitud. La respuesta de la operación contiene cualquier información de error que pueda relacionarse con la fórmula.

## Creación de un grupo con el escalado automático habilitado

Creación de un grupo de una de las siguientes maneras:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx) – Este cmdlet usa el parámetro AutoScaleFormula para especificar la fórmula de escalación automática.
- [IPoolManager.CreatePool Method](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) – Después de que se llame a este método .NET para crear un grupo, [ICloudPool.AutoScaleEnabled Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) y [ICloudPool.AutoScaleFormula Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) se configuran en el grupo para habilitar el escalado automático.
- [Agregar un grupo a una cuenta](https://msdn.microsoft.com/library/azure/dn820174.aspx): los elementos enableAutoScale y autoScaleFormula se utilizan en esta API de REST para configurar el escalado automático para el grupo cuando se crea.

> [AZURE.NOTE]Si establece la escalación automática cuando el grupo se crea con los recursos mencionados anteriormente, no se usa el parámetro targetDedicated para el grupo.

## Habilitación de la escalación automática después de crear un grupo

Si ya configuró un grupo con un número específico de nodos de ejecución con el parámetro targetDedicated, puede actualizar el grupo existente posteriormente para realizar la escalación automáticamente. Realice esto de una de estas formas:

- [IPoolManager.EnableAutoScale Method](https://msdn.microsoft.com/library/azure/dn931709.aspx): este método .NET requiere el nombre del grupo existente y la formula de escalación automática.
- [Escalado automático de habilitar o deshabilitar](https://msdn.microsoft.com/library/azure/dn820173.aspx): esta API de REST requiere el nombre de grupo existente en el URI y la fórmula de escala automática en el cuerpo de solicitud.

> [AZURE.NOTE]El valor que se especificó para el parámetro targetDedicated cuando se creó el grupo se ignoró cuando se evaluó la fórmula de escalación automática.

## Obtención de información sobre las ejecuciones de escalado automático

Debe comprobar periódicamente los resultados de las ejecuciones de escalado automático. Realice este procedimiento de una de estas maneras:

- [ICloudPool.AutoScaleRun Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx): cuando use la biblioteca .NET, esta propiedad de un grupo proporciona una instancia de [AutoScaleRun Class](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), que proporciona [AutoScaleRun.Error Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), [AutoScaleRun.Results Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) y [AutoScaleRun.Timestamp Property](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Obtener información sobre un grupo](https://msdn.microsoft.com/library/dn820165.aspx): esta API de REST devuelve información acerca del grupo, que incluye la actualización de escalado automático más reciente.

## Pasos siguientes

1.	Es posible que necesite obtener acceso al nodo de ejecución para evaluar al completo la eficiencia de la aplicación. Para aprovechar el acceso remoto, debe agregarse una cuenta de usuario al nodo de ejecución al que desee obtener acceso y debe recuperarse un archivo RDP de ese nodo. Agregue la cuenta de usuario de una de estas maneras:

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx): este cmdlet adquiere el nombre de grupo, el nombre de nodo de ejecución, el nombre de cuenta y la contraseña como parámetros.
	- [IVM.CreateUser Method](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx): este método .NET crea una instancia de [IUser Interface](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx) en la que el nombre de cuenta y la contraseña se pueden establecer para el nodo de ejecución.
	- [Agregar una cuenta de usuario a un nodo](https://msdn.microsoft.com/library/dn820137.aspx): el nombre de grupo y el nodo de ejecución se especifican en el URI y el nombre de cuenta y la contraseña se envían al nodo en el cuerpo de la solicitud de esta API de REST.

		Obtención del archivo RDP:

	- [IVM.GetRDPFile Method](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx): este método .NET requiere el nombre del archivo RDP que crear.
	- [Obtener un archivo de protocolo de escritorio remoto de un nodo](https://msdn.microsoft.com/library/dn820120.aspx): esta API de REST requiere el nombre del grupo y el nombre del nodo de ejecución. La respuesta incorpora el contenido del archivo RDP.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx): este cmdlet obtiene el archivo RDP del nodo de ejecución especificado y lo guarda en la ubicación del archivo especificada o en una transmisión.
2.	Algunas aplicaciones generan grandes cantidades de datos que pueden ser difíciles de procesar. Una manera de resolver esto es a través de una [consulta de lista eficiente](batch-efficient-list-queries.md).

<!---HONumber=August15_HO7-->