---
title: Escalar los trabajos de Stream Analytics para incrementar el rendimiento | Microsoft Docs
description: "Aprenda a escalar los trabajos de Análisis de transmisiones mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el ajuste de las unidades de streaming del trabajo."
keywords: "datos de streaming, procesamiento de datos de streaming, optimización del análisis"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: f1e5e11e82d344508aa4375c42d509f96aaa1d00
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Escalado de trabajos de Análisis de transmisiones de Azure para incrementar el rendimiento de procesamiento de flujo de datos
En este artículo se muestra cómo ajustar una consulta de Stream Analytics para aumentar la capacidad de procesamiento de trabajos de Stream Analytics. Aprenda a escalar los trabajos de Stream Analytics mediante la configuración de particiones de entrada, el ajuste de la definición de consulta y el cálculo y establecimiento de las *unidades de streaming* (SU) del trabajo. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>¿Cuáles son las partes de un trabajo de Análisis de transmisiones?
Una definición de trabajo de Análisis de transmisiones incluye entradas, una consulta y la salida. Las entradas proceden del lugar en el cual el trabajo lee el flujo de datos. La consulta se usa para transformar el flujo de entrada de datos y la salida es el lugar al que el trabajo envía los resultados.  

Un trabajo requiere al menos un origen de entrada de streaming de datos. El origen de entrada del flujo de datos puede almacenarse en un centro de eventos de Azure o en Azure Blob Storage. Para más información, vea [¿Qué es Azure Stream Analytics?](stream-analytics-introduction.md) e [Introducción al uso de Azure Stream Analytics: detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-event-hubs-and-azure-storage"></a>Particiones en Event Hubs y Azure Storage
El escalado de un trabajo de Stream Analytics aprovecha las particiones en la entrada o la salida. La creación de particiones permite dividir los datos en subconjuntos en función de una clave de partición. Un proceso que consume los datos (por ejemplo, un trabajo de Stream Analytics) puede consumir y escribir diferentes particiones en paralelo, lo que aumenta la capacidad de procesamiento. Si trabaja con Stream Analytics, puede aprovechar las ventajas de la creación de particiones en Event Hubs y Blob Storage. 

Para más información sobre las particiones, vea los siguientes artículos:

* [Información general de las características de Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Creación de particiones de datos](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>Unidades de streaming (SU)
Las unidades de streaming (SU) representan los recursos y la capacidad informática que se necesitan para ejecutar un trabajo de Azure Stream Analytics. Las SU proporcionan una forma de describir la capacidad de procesamiento del evento relativo en función de una medida que combina la CPU, la memoria y las tasas de lectura y escritura. Cada unidad de streaming corresponde aproximadamente a 1 MB por segundo de capacidad de procesamiento. 

La elección del número de unidades de streaming que se necesitan para un trabajo en concreto depende de la configuración de particiones para las entradas y de la consulta definida para el trabajo. Puede seleccionar como máximo su cuota en unidades de streaming para un trabajo. De manera predeterminada, cada suscripción de Azure tiene una cuota máxima de 50 unidades de streaming en todos los trabajos de análisis de una región específica. Para aumentar las unidades de streaming de sus suscripciones, contacte con [Soporte técnico de Microsoft](http://support.microsoft.com). Los valores válidos para unidades de streaming por trabajo son 1, 3, 6 y más en incrementos de 6.

## <a name="embarrassingly-parallel-jobs"></a>Trabajos embarazosamente paralelos
Un trabajo *embarazosamente paralelo* es el escenario más escalable que tenemos en Azure Stream Analytics. Conecta una partición de la entrada en una instancia de la consulta a una partición de la salida. Este paralelismo tiene los siguientes requisitos:

1. Si la lógica de la consulta depende de la misma clave que procesa la misma instancia de consulta, ha de asegurarse de que los eventos vayan a la misma partición de la entrada. En Event Hubs, esto significa que los datos del evento deben tener establecido el valor **PartitionKey**. También puede usar remitentes con particiones. En Blob Storage, esto significa que los eventos se envían a la misma carpeta de partición. Si la lógica de consulta no requiere que la misma instancia de consulta procese la misma clave, puede ignorar este requisito. Un ejemplo de esta lógica sería una sencilla consulta select-project-filter.  

2. Una vez dispuestos los datos en la salida, hay que asegurarse de que la consulta está particionada. Esto requiere el uso de **Partition By** en todos los pasos. Se pueden usar varios pasos, pero todos deben particionarse con la misma clave. Actualmente, la clave de partición debe establecerse en **PartitionId** para que el trabajo sea totalmente paralelo.  

3. Solo Event Hubs y Blob Storage admiten en este momento salidas con particiones. En la salida de Event Hubs, debe configurar la clave de partición para que sea **PartitionId**. En la salida de Blob Storage, no tiene que hacer nada.  

4. El número de particiones de entrada debe ser igual al número de particiones de salida. La salida de Blob Storage no admite en este momento la creación de particiones. Pero no importa, porque hereda el esquema de partición de la consulta ascendente. Vea ejemplos de valores de partición que permiten un trabajo totalmente paralelo:  

   * Ocho particiones de entrada de Event Hubs y ocho particiones de salida de Event Hubs
   * Ocho particiones de entrada de Event Hubs y salida de Blob Storage  
   * Ocho particiones de entrada de Blob Storage y salida de Blob Storage  
   * Ocho particiones de entrada de Blob Storage y ocho particiones de salida de Event Hubs  

En las siguientes secciones se describen algunos escenarios de ejemplo que son embarazosamente paralelos.

### <a name="simple-query"></a>Consulta sencilla

* Entrada: Event Hubs con ocho particiones
* Salida: Event Hubs con ocho particiones

Consulta:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Esta consulta es un filtro sencillo. Por consiguiente, no hay que preocuparse por crear particiones en la entrada que se envía al centro de eventos. Observe que la consulta incluye **Partition By PartitionId**, por lo que cumple el requisito 2 de antes. En cuanto a la salida, es preciso configurar la salida del centro de eventos en el trabajo para que el la clave de partición se establezca en **PartitionId**. Una última comprobación consiste en asegurarse de que el número de particiones de entrada es igual al número de particiones de salida.

### <a name="query-with-a-grouping-key"></a>Consulta con una clave de agrupación

* Entrada: Event Hubs con ocho particiones
* Salida: Blob Storage

Consulta:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tiene una clave de agrupación. Por lo tanto, es preciso que la misma clave se procese en la misma instancia de consulta, lo que significa que deben enviarse eventos al centro de eventos de manera particionada. ¿Pero qué clave debe usarse? **PartitionId** es un concepto de lógica de trabajos. La clave que realmente importa es **TollBoothId**, por lo que el valor **PartitionKey** de los datos de evento debe ser **TollBoothId**. Esto se hace en esta consulta estableciendo **Partition By** en **PartitionId**. Puesto que la salida es Blob Storage, no hay que preocuparse en configurar un valor de clave de partición, como estipula el requisito 4.

### <a name="multi-step-query-with-a-grouping-key"></a>Consulta de varios pasos con una clave de agrupación
* Entrada: Event Hubs con ocho particiones
* Salida: instancia de Event Hubs con ocho particiones

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tiene una clave de agrupación, por lo que es necesario que la misma instancia de procese la misma clave. Se puede usar la misma estrategia que en el ejemplo anterior. En este caso, la consulta tiene varios pasos. ¿Cada paso tiene **Partition By PartitionId**? Sí, por lo que la consulta cumple el requisito 3. En cuanto a la salida, es necesario establecer la clave de partición en **PartitionId**, tal y como se ha explicado antes. También puede verse que tiene el mismo número de particiones que la entrada.

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Escenarios de ejemplo que *no* son embarazosamente paralelos

En la sección anterior, se han mostrado algunos escenarios embarazosamente paralelos. En esta sección se describen escenarios en los que no se cumplen todos los requisitos para que sean embarazosamente paralelos. 

### <a name="mismatched-partition-count"></a>Recuento de particiones no coincidente
* Entrada: Event Hubs con ocho particiones
* Salida: Event Hubs con 32 particiones

En este caso, no importa cuál es la consulta. Si el recuento de particiones de entrada no coincide con el recuento de particiones de salida, la topología no es embarazosamente paralela.

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>No se usa Event Hubs ni Blob Storage como salida
* Entrada: Event Hubs con ocho particiones
* Salida: PowerBI

La salida de PowerBI no admite en este momento la creación de particiones. Por consiguiente, este escenario no es embarazosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de varios pasos con diferentes valores de Partition By
* Entrada: Event Hubs con ocho particiones
* Salida: Event Hubs con ocho particiones

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Como puede ver, el segundo paso utiliza **TollBoothId** como clave de partición. Este paso no es igual al primer paso y, por tanto, hay que seguir un orden aleatorio. 

Los ejemplos anteriores muestran algunos trabajos de Stream Analytics que se ajustan (o no) a una topología embarazosamente paralela. Si se ajustan, tienen posibilidades de alcanzar la escala máxima. En el caso de los trabajos que no se ajustan a uno de estos perfiles, habrá una guía de escalado disponible en futuras actualizaciones. Por ahora, use la guía general en las siguientes secciones.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Cálculo de las unidades máximas de streaming para un trabajo
El número total de unidades de streaming que se puede utilizar en un trabajo de Stream Analytics depende del número de pasos de la consulta definida para el trabajo y del número de particiones para cada paso.

### <a name="steps-in-a-query"></a>Pasos de una consulta
Una consulta puede tener uno o varios pasos. Cada paso es una subconsulta definida mediante la palabra clave **WITH**. La consulta que queda al margen de la palabra clave **WITH** (una sola consulta) también se cuenta como un paso; por ejemplo, la instrucción **SELECT** de la consulta siguiente:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Esta consulta tiene dos pasos.

> [!NOTE]
> Esta consulta se analiza con más detalle más adelante en el artículo.
>  

### <a name="partition-a-step"></a>Posicionamiento de un paso
El particionamiento de un paso requiere las siguientes condiciones:

* El origen de entrada debe tener particiones. 
* La instrucción **SELECT** de la consulta debe leer desde un origen de entrada particionada.
* La consulta comprendida en el paso debe incluir la palabra clave **Partition By**.

Cuando una consulta está particionada, los eventos de entrada se procesan y agregan en grupos de particiones independientes, y se generan eventos de salida para cada uno de los grupos. Si quiere usar un agregado combinado, debe crear un segundo paso sin particionar que agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Cálculo de las unidades máximas de streaming para un trabajo
Todos los pasos sin particiones juntos pueden escalarse hasta seis unidades de streaming (SU) para un trabajo de Stream Analytics. Para agregar unidades de streaming, un paso debe estar particionado. Cada partición puede tener seis unidades de streaming.

<table border="1">
<tr><th>Consultar</th><th>Número máximo de unidades de streaming del trabajo</th></td>

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
<li>La secuencia de datos de entrada está particionada en tres.</li>
<li>La consulta contiene dos pasos. El paso de entrada tiene particiones y el segundo paso no.</li>
<li>La instrucción <strong>SELECT</strong> se lee de la entrada particionada.</li>
</ul>
</td>
<td>24 (18 para los pasos particionados y 6 para los pasos no particionados)</td></tr>
</table>

### <a name="examples-of-scaling"></a>Ejemplos de escalado

La siguiente consulta calcula el número de vehículos que pasan por una estación de peaje con tres cabinas de peaje en una ventana temporal de tres minutos. Esta consulta se puede escalar hasta seis unidades de streaming.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Para usar más unidades de streaming en la consulta, el flujo de datos de entrada y la consulta deben estar particionados. Dado que la partición del flujo de datos está establecida en 3, la siguiente consulta modificada puede escalarse hasta 18 unidades de streaming:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cuando una consulta está particionada, se procesan los eventos de entrada y se agregan en grupos de particiones independientes. También se generan eventos de salida para cada uno de los grupos. La creación de particiones puede ocasionar algunos resultados inesperados cuando el campo **GROUP BY** no es la clave de partición del flujo de datos de entrada. Por ejemplo, el campo **TollBoothId** de la consulta anterior no es la clave de partición de **Input1**. El resultado es que los datos de la cabina de peaje 1 se pueden distribuir en varias particiones.

Cada una de las particiones de **Input1** se procesará por separado en Stream Analytics. Por consiguiente, se crearán varios registros de recuento de vehículos para la misma cabina en la misma ventana de saltos de tamaño constante. Si la clave de partición de entrada no se puede cambiar, este problema se puede solucionar agregando un paso adicional sin particiones, como en el ejemplo siguiente:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Esta consulta se puede escalar hasta 24 unidades de streaming.

> [!NOTE]
> Si va a combinar dos flujos de datos, asegúrese de que estén particionados mediante la clave de partición de la columna que usa para realizar las combinaciones. Asegúrese también de que tiene el mismo número de particiones en ambos flujos de datos.
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>Configuración de las unidades de streaming de Stream Analytics

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la lista de recursos, busque el trabajo de Stream Analytics que quiere escalar y ábralo.
3. En la hoja del trabajo, en **Configurar**, haga clic en **Escala**.

    ![Configuración del trabajo de Stream Analytics en Azure Portal][img.stream.analytics.preview.portal.settings.scale]

4. Use el control deslizante para establecer las unidades de streaming del trabajo. Tenga en cuenta que está limitado a la configuración de unidades de streaming específica.


## <a name="monitor-job-performance"></a>Supervisión del rendimiento del trabajo
Mediante Azure Portal, puede realizar el seguimiento de la capacidad de procesamiento de un trabajo:

![Trabajos de supervisión de Análisis de transmisiones de Azure][img.stream.analytics.monitor.job]

Calcule la capacidad de procesamiento esperada de la carga de trabajo. Si la capacidad de procesamiento es inferior a la esperada, ajuste la partición de entrada, ajuste la consulta y agregue unidades de streaming al trabajo.


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>Visualización de la capacidad de procesamiento de Stream Analytics a escala: escenario de Raspberry Pi
Para ayudarle a comprender cómo se escalan los trabajos de Stream Analytics, se ha realizado un experimento basado en un dispositivo Raspberry Pi. Este experimento permite ver los efectos sobre la capacidad de procesamiento de varias unidades de streaming y particiones.

En este escenario, el dispositivo envía datos de sensores (clientes) a un centro de eventos. Stream Analytics procesa los datos y envía una alerta o estadísticas como salida a otro centro de procesamiento. 

El cliente envía los datos del sensor en formato JSON. La salida de datos también está en formato JSON. Los datos tienen este aspecto:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

La siguiente consulta se utiliza para enviar una alerta cuando se apaga una luz:

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Medición de la capacidad de procesamiento

En este contexto, la capacidad de procesamiento es la cantidad de datos de entrada que procesa Stream Analytics en una cantidad fija de tiempo. (Se mide durante 10 minutos). Para conseguir la mejor capacidad de procesamiento de los datos de entrada, tanto la entrada de flujo de datos como la consulta se han particionado. También se incluye **COUNT()** en la consulta para medir el número de eventos de entrada procesados. Para asegurarse de que el trabajo no está esperando simplemente a que lleguen los eventos de entrada, cada partición del centro de eventos de entrada se ha cargado previamente con aproximadamente 300 MB.

La siguiente tabla muestra los resultados se observan al aumentar el número de unidades de streaming y los correspondientes recuentos de particiones en Event Hubs.  

<table border="1">
<tr><th>Particiones de entrada</th><th>Particiones de salida</th><th>Unidades de streaming</th><th>Capacidad de procesamiento sostenida
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 MB/s</td>
</tr>
</table>

Y en el gráfico siguiente se muestra una visualización de la relación entre unidades de streaming y capacidad de procesamiento.

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obtener ayuda
Para obtener ayuda adicional, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-real-time-fraud-detection.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


