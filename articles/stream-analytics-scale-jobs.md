<properties 
	pageTitle="Escalación de trabajos de Stream Analytics | Azure" 
	description="Obtenga información acerca de cómo escalar trabajos de análisis de secuencias" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/10/2015" 
	ms.author="jgao"/>

# Escalación de trabajos de Azure Stream Analytics 

Aprenda a calcular las *unidades de streaming* para un trabajo de Stream Analytics, y a escalar trabajos de Stream Analytics mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el establecimiento de las unidades de streaming del trabajo.

Una definición de trabajo de Azure Stream Analytics incluye entradas, consultas y salidas. Las entradas son el lugar de donde el trabajo lee la secuencia de datos, la salida es el lugar adonde el trabajo envía los resultados y la consulta se usa para transformar la secuencia de entrada.  Un trabajo requiere al menos un origen de entrada de secuencia de datos. El origen de entrada de secuencia de datos puede ser un centro de eventos de bus de servicio de Azure o un almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción a Azure Stream Analytics][stream.analytics.introduction], [Introducción al uso de Azure Stream Analytics][stream.analytics.get.started] y [Guía para desarrolladores de Azure Stream Analytics][stream.analytics.developer.guide]. 

El recurso disponible para procesar los trabajos de Stream Analytics se mide mediante una unidad de streaming. Cada unidad de streaming puede proporcionar una capacidad de procesamiento de hasta 1 MB por segundo. Cada trabajo necesita como mínimo una unidad de streaming, que es el valor predeterminado para todos los trabajos. Puede configurar hasta 12 unidades de streaming para un trabajo de Stream Analytics mediante el Portal de administración de Azure. Cada suscripción de Azure solo puede tener hasta 12 unidades de streaming en todos los trabajos de una región específica. Para aumentar las unidades de streaming de su suscripción hasta 100 unidades, póngase en contacto con el [soporte técnico de Microsoft][microsoft.support].

El número de unidades de streaming que puede utilizar un trabajo depende de la configuración de partición en las entradas y de la consulta definida para el trabajo. En este artículo se mostrará cómo calcular y ajustar la consulta para aumentar la capacidad de procesamiento.

##En este artículo
+ [Cálculo de las unidades máximas de streaming para un trabajo](#calculate)
+ [Configuración de la partición de trabajo de Stream Analytics](#configure)
+ [Supervisión del rendimiento de los trabajos de Stream Analytics](#monitor)
+ [Pasos siguientes](#nextstep)


##<a name="calculate"></a>Cálculo de las unidades máximas de streaming para un trabajo
El número total de unidades de streaming que se puede utilizar en un trabajo de Stream Analytics depende del número de pasos de la consulta definida para el trabajo y del número de particiones para cada paso.

### Pasos de una consulta
Una consulta puede tener uno o varios pasos. Cada paso es una subconsulta que se define mediante la palabra clave WITH. La única consulta que se encuentra fuera de la palabra clave WITH también se cuenta como paso. Por ejemplo, la instrucción SELECT en la consulta siguiente:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

La consulta anterior tiene dos pasos. 

> [WACOM.NOTE] Esta consulta de ejemplo se explicará más adelante en este artículo.

### Particionamiento de un paso

El particionamiento de un paso requiere las siguientes condiciones:

- El origen de entrada debe estar particionado. Consulte la [Guía para desarrolladores de Azure Stream Analytics][stream.analytics.developer.guide] y la [Guía para desarrolladores de centros de eventos de Azure][azure.event.hubs.developer.guide].
- La instrucción SELECT de la consulta se debe leer desde un origen de entrada particionado. 
- La consulta dentro del paso debe tener la palabra clave Partition By. 

Cuando una consulta está particionada, los eventos de entrada se procesan y agregan en grupos de particiones sdeparados, y se generan eventos de salida para cada uno de los grupos. Si un agregado combinado es aconsejable, debe crear un segundo paso sin particionar para agregar.

La versión preliminar de Azure Stream Analytics no admite el particionamiento por nombres de columna. Solo se puede particionar por el campo PartitionId, que es un campo integrado en la consulta. El campo ParitionId indica de qué partición de la secuencia de datos de origen procede el evento.  Para obtener más información, consulte [Limitaciones y problemas conocidos de Azure Stream Analytics][stream.analytics.limitations].

### Cálculo de las unidades máximas de streaming para un trabajo

Juntos, todos los pasos no particionados pueden escalar hasta seis unidades de streaming para un trabajo de Stream Analytics. Para agregar unidades de streaming adicionales, se debe particionar un paso. Cada partición puede tener seis unidades de streaming.

<table border="1">
<tr><th>Consulta de un trabajo</th><th>Unidades máximas de streaming para el trabajo</th></td>

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
<li>La consulta contiene dos pasos.</li>
<li>La instrucción SELECT se lee de la entrada particionada.</li>
</ul>
</td>
<td>24 (18 para el paso particionado + 6 para el paso no particionado)</td></tr>
</table>

###Ejemplo de escala
La siguiente consulta calcula el número de vehículos que pasan por una estación de peaje con tres cabinas de peaje en una ventana de tres minutos. Esta consulta se puede escalar hasta 6 unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Para utilizar más unidades de streaming en la consulta, la entrada de la secuencia de datos y la consulta se deben particionar. Dado que la partición de la secuencia de datos está establecida en 3, la siguiente consulta modificada se puede escalar hasta 18 unidades de streaming.

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Cuando una consulta está particionada, los eventos de entrada se procesan y agregan en grupos de particiones separados, y también se generan eventos de salida para cada uno de los grupos. El particionamiento puede ocasionar algunos resultados inesperados cuando el campo group-by no es la clave de partición en la entrada de la secuencia de datos. Por ejemplo, el campo TollBoothId de la consulta de ejemplo anterior no es la clave de partición de Input1. Los datos de la cabina 1 se pueden distribuir en varias particiones. Cada una de las particiones de Imput1 se procesará por separado en Stream Analytics y se crearán varios registros del recuento de vehículos que pasan de la misma cabina de peaje en la misma ventana de saltos. En caso de que no se pueda cambiar la clave de partición de entrada, este problema se puede solucionar agregando un paso adicional sin partición. Por ejemplo:

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta se puede escalar hasta 24 unidades de streaming. 

>[WACOM.NOTE] Si va a unir dos secuencias, asegúrese de que estén particionadas por la clave de partición de la columna por la que hace las uniones y de que tenga el mismo número de particiones en ambas secuencias.


##<a name="configure"></a>Configuración de la partición de trabajo de Stream Analytics

**Para ajustar la unidad de streaming de un trabajo, siga estos pasos:**

1. Inicie sesión en el [Portal de administración][azure.management.portal].
2. Haga clic en **Stream Analytics** en el lado izquierdo.
3. Haga clic en el trabajo de Stream Analytics que desea escalar.
4. Haga clic en **ESCALA** en la parte superior de la página.

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>Supervisión del rendimiento del trabajo

Mediante el portal de administración, puede realizar el seguimiento de la capacidad de procesamiento de un trabajo en eventos por segundo:

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
Calcule la capacidad de procesamiento esperada de la carga de trabajo en eventos por segundo. En caso de que la capacidad de procesamiento sea inferior a la esperada, ajuste la partición de entrada, ajuste la consulta y agregue unidades de streaming adicionales a su trabajo.

##<a name="nextstep"></a> Pasos siguientes
En este artículo, ha aprendido a calcular unidades de streaming y a escalar un trabajo de Stream Analytics. Para obtener más información acerca de Stream Analytics, consulte:

- [Introducción a Azure Stream Analytics][stream.analytics.introduction]
- [Introducción al uso de Azure Stream Analytics][stream.analytics.get.started]
- [Guía para desarrolladores de Azure Stream Analytics][stream.analytics.developer.guide]
- [Problemas conocidos y limitaciones de Azure Stream Analytics][stream.analytics.limitations]
- [Referencia de lenguaje de consulta de Azure Stream Analytics][stream.analytics.query.language.reference]
- [Referencia de la API de REST de administración de Azure Stream Analytics][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301



<!--HONumber=46--> 
