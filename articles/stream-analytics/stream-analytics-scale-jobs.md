<properties
	pageTitle="Escalar los trabajos de Análisis de transmisiones para incrementar el rendimiento | Microsoft Azure"
	description="Aprenda a escalar los trabajos de Análisis de transmisiones mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el ajuste de las unidades de streaming del trabajo."
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/01/2015"
	ms.author="jeffstok"/>

# Escalar los trabajos de Análisis de transmisiones de Azure para incrementar el rendimiento #

Aprenda a calcular las *unidades de streaming* para un trabajo de Análisis de transmisiones, y a escalar trabajos de Análisis de transmisiones mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el ajuste de las unidades de streaming del trabajo.

## ¿Cuáles son las partes de un trabajo de Análisis de transmisiones? ##
Una definición de trabajo de Análisis de transmisiones de Azure incluye entradas, una consulta y la salida. Las entradas proceden del lugar en el cual el trabajo lee el flujo de datos, la consulta se usa para transformar el flujo de entrada de datos y la salida es el lugar al que el trabajo envía los resultados.

Un trabajo requiere al menos un origen de entrada de streaming de datos. El origen de entrada de streaming de datos puede almacenarse en un Centro de eventos de Bus de servicio de Azure o en un almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md), [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md) y [Guía para desarrolladores de Análisis de transmisiones de Azure](../stream-analytics-developer-guide.md).

## Configuración de unidades de streaming ##
Las Unidades de streaming (SU) representan los recursos y la capacidad de ejecutar un trabajo de Análisis de transmisiones de Azure. Las SU proporcionan una forma de describir la capacidad de procesamiento del evento relativo en función de una medida que combina la CPU, la memoria y las tasas de lectura y escritura. Cada unidad de streaming corresponde aproximadamente a 1 MB por segundo de rendimiento.

Elegir cuántas SU son necesarias para un trabajo en concreto depende de la configuración de la partición de las entradas y de la consulta definida para el trabajo. Puede seleccionar tantas unidades de streaming para un trabajo como su cuota se lo permita, mediante el Portal de Azure. De forma predeterminada, cada suscripción de Azure tiene una cuota máxima de 50 unidades de streaming en todos los trabajos de análisis de una región específica. Para aumentar las unidades de streaming de su suscripción póngase en contacto con el [Servicio de soporte técnico de Microsoft](http://support.microsoft.com).

El número de unidades de streaming que puede usar un trabajo depende de la configuración de particiones para las entradas y de la consulta definida para el trabajo. Tenga en cuenta que también se debe usar un valor válido para las unidades de streaming. Los valores válidos comienzan por 1, 3, 6 y, a continuación, van aumentando de seis en seis, tal y como se muestra a continuación.

![Escala de las unidades de streaming de Análisis de transmisiones de Azure][img.stream.analytics.streaming.units.scale]

En este artículo aprenderá a calcular y a ajustar la consulta para aumentar el rendimiento de los trabajos de análisis.

## Cálculo de las unidades máximas de streaming para un trabajo ##
El número total de unidades de streaming que se puede utilizar en un trabajo de Stream Analytics depende del número de pasos de la consulta definida para el trabajo y del número de particiones para cada paso.

### Pasos de una consulta ###
Una consulta puede tener uno o varios pasos. Cada paso es una subconsulta que se define mediante la palabra clave WITH. La única consulta que se queda excluida de la palabra clave WITH también se cuenta como un paso; por ejemplo, la instrucción SELECT en la consulta siguiente:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId

La consulta anterior tiene dos pasos.

> [AZURE.NOTE]Esta consulta de ejemplo se explicará más adelante en este artículo.

### Posicionamiento de un paso ###

El particionamiento de un paso requiere las siguientes condiciones:

- El origen de entrada debe tener particiones. Para obtener más información, consulte [Guía para el desarrollador de Análisis de transmisiones de Azure](../stream-analytics-developer-guide.md) y [Guía de programación de Centros de eventos](../azure-event-hubs-developer-guide.md).
- La instrucción SELECT de la consulta se debe leer desde un origen de entrada particionado.
- La consulta dentro del paso debe incluir la palabra clave **Partition By**.

Cuando una consulta está particionada, los eventos de entrada se procesan y agregan en grupos de particiones sdeparados, y se generan eventos de salida para cada uno de los grupos. Si un agregado combinado es aconsejable, debe crear un segundo paso sin particionar para agregar.

### Cálculo de las unidades máximas de streaming para un trabajo ###

Todos los pasos sin particiones juntos pueden escalar hasta seis unidades de streaming para un trabajo de Análisis de transmisiones. Para agregar unidades de streaming adicionales, se debe particionar un paso. Cada partición puede tener seis unidades de streaming.

<table border="1">
<tr><th>Consulta de un trabajo</th><th>Número máximo de unidades de streaming para el trabajo</th></td>

<tr><td>
<ul>
<li>La consulta contiene un solo paso.</li>
<li>El paso no está particionado.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>La secuencia de datos de entrada está particionada en tres.</li>
<li>La consulta contiene un solo paso.</li>
<li>El paso está particionado.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La consulta contiene dos pasos.</li>
<li>Ninguno de los pasos está particionado.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>La entrada de la secuencia de datos está particionada en tres.</li>
<li>La consulta contiene dos pasos. El paso de entrada tiene particiones y el segundo paso no.</li>
<li>La instrucción SELECT se lee de la entrada particionada.</li>
</ul>
</td>
<td>24 (18 para los pasos particionados y 6 para los pasos no particionados)</td></tr>
</table>

### Ejemplo de escala ###
La siguiente consulta calcula el número de vehículos que pasan por una estación de peaje con tres cabinas de peaje en una ventana temporal de tres minutos. Esta consulta se puede escalar hasta seis unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar más unidades de streaming en la consulta, la entrada del streaming de datos y la consulta deben tener particiones. Dado que la partición del streaming de datos está establecida en 3, la siguiente consulta modificada puede escalarse hasta 18 unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cuando una consulta está particionada, se procesan los eventos de entrada y se agregan en grupos de particiones independientes. También se generan eventos de salida para cada uno de los grupos. La creación de particiones puede ocasionar algunos resultados inesperados cuando el campo **Group-by** no es la clave de partición en la entrada de streaming de datos. Por ejemplo, el campo TollBoothId de la consulta de ejemplo anterior no es la clave de partición de Input1. Los datos de la cabina 1 se pueden distribuir en varias particiones.

Cada una de las particiones de Imput1 se procesará por separado en Análisis de transmisiones y se crearán varios registros del recuento de vehículos que pasan por la misma cabina de peaje en la misma ventana de saltos de tamaño constante. Si la clave de partición de entrada no se puede cambiar, este problema se puede solucionar agregando un paso adicional sin particiones, por ejemplo:

	WITH Step1 AS (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta se puede escalar hasta 24 unidades de streaming.

>[AZURE.NOTE]Si va a unir dos secuencias, asegúrese de que estén particionadas mediante la clave de partición de la columna que usa para realizar las uniones, y de que tenga el mismo número de particiones en ambas secuencias.


## Configuración de la partición de trabajo de Stream Analytics ##

**Para ajustar una unidad de streaming de un trabajo**

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2. Haga clic en **Análisis de transmisiones** en el panel izquierdo.
3. Haga clic en el trabajo de Stream Analytics que desea escalar.
4. Haga clic en **ESCALA** en la parte superior de la página.

![Análisis de transmisiones de Azure -- Configuración de escala de trabajo][img.stream.analytics.configure.scale]


## Supervisión del rendimiento del trabajo ##

Mediante el portal de administración, puede realizar el seguimiento de la capacidad de procesamiento de un trabajo en eventos por segundo:

![Trabajos de supervisión de Análisis de transmisiones de Azure][img.stream.analytics.monitor.job]

Calcule la capacidad de procesamiento esperada de la carga de trabajo en eventos por segundo. Si la capacidad de procesamiento es inferior a la esperada, ajuste la partición de entrada, ajuste la consulta y agregue unidades de streaming adicionales a su trabajo.

## Capacidad de procesamiento de ASA a escala - Escenario Raspberry PI ##


Para entender cómo se escala ASA en un escenario típico en términos de capacidad de procesamiento en varias unidades de streaming, aquí tenemos un experimento que envía los datos de sensor (clientes) al Centro de eventos, ASA los procesa y envía una alerta o estadísticas como salida a otro Centro de eventos.

El cliente envía datos de sensor sintetizados a Centros de eventos en formato JSON para ASA, la salida de datos también está en formato JSON. Aquí se muestra el aspecto que tendrían los datos de ejemplo:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Consulta: "enviar una alerta cuando se apague la luz"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

Medición de la capacidad de procesamiento: la capacidad de procesamiento en este contexto es la cantidad de datos de entrada procesados por ASA en una cantidad fija de tiempo (10 minutes). Para conseguir la mejor capacidad de procesamiento de los datos de entrada, tanto la entrada de streaming de datos como la consulta deben tener particiones. Además se incluye COUNT() en la consulta para medir el número de eventos de entrada que se han procesado. Para asegurarse de que ASA no está simplemente esperando a que lleguen eventos de entrada, cada partición del Centro de eventos de entrada se cargó previamente con suficientes datos de entrada (aprox. 300 MB).

A continuación se muestran los resultados por número creciente de unidades de streaming y los correspondientes recuentos por partición en los Centros de eventos.

<table border="1">
<tr><th>Particiones de entrada</th><th>Particiones de salida</th><th>Unidades de streaming</th><th>Capacidad de procesamiento sostenida
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06&#160;MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06&#160;MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32&#160;MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67&#160;MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27&#160;MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69&#160;MB/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## Obtener ayuda ##
Para obtener ayuda adicional, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Pasos siguientes ##

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=July15_HO2-->