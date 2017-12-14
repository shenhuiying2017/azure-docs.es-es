---
title: "Aprovechamiento de la paralelización de consultas en Azure Stream Analytics | Microsoft Docs"
description: "Aprenda a escalar los trabajos de Stream Analytics mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el ajuste de las unidades de streaming del trabajo."
keywords: "datos de streaming, procesamiento de datos de streaming, optimización del análisis"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: dd60026cad9246da8eba141125aebf061ecf7e9d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Aprovechamiento de la paralelización de consultas en Azure Stream Analytics
En este artículo se muestra cómo aprovechar la paralelización en Azure Stream Analytics. Aprenda a escalar los trabajos de Stream Analytics mediante la configuración de particiones de entrada y el ajuste de la definición de consultas de análisis.
Como requisito previo, puede que le interese conocer la noción de Unidad de streaming que se describe en [Descripción y ajuste de las unidades de streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>¿Cuáles son las partes de un trabajo de Stream Analytics?
Una definición de trabajo de Stream Analytics incluye entradas, una consulta y la salida. Las entradas proceden del lugar en el cual el trabajo lee el flujo de datos. La consulta se usa para transformar el flujo de entrada de datos y la salida es el lugar al que el trabajo envía los resultados.  

Un trabajo requiere al menos un origen de entrada de streaming de datos. El origen de entrada del flujo de datos puede almacenarse en un centro de eventos de Azure o en Azure Blob Storage. Para más información, vea [¿Qué es Azure Stream Analytics?](stream-analytics-introduction.md) e [Introducción al uso de Azure Stream Analytics: detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Particiones en orígenes y receptores
El escalado de un trabajo de Stream Analytics aprovecha las particiones en la entrada o la salida. La creación de particiones permite dividir los datos en subconjuntos en función de una clave de partición. Un proceso que consume los datos (por ejemplo, un trabajo de Stream Analytics) puede consumir y escribir diferentes particiones en paralelo, lo que aumenta la capacidad de procesamiento. 

### <a name="inputs"></a>Entradas
Todas las entradas de Azure Stream Analytics pueden aprovechar la creación de particiones:
-   EventHub (la clave de partición se debe establecer explícitamente)
-   IoT Hub (la clave de partición se debe establecer explícitamente)
-   Almacenamiento de blobs

### <a name="outputs"></a>Salidas

Cuando trabaja con Stream Analytics, puede aprovechar la creación de particiones en las salidas:
-   Azure Data Lake Storage
-   Azure Functions
-   tabla de Azure
-   Almacenamiento de blobs
-   CosmosDB (la clave de partición se debe establecer explícitamente)
-   EventHub (la clave de partición se debe establecer explícitamente)
-   IoT Hub (la clave de partición se debe establecer explícitamente)
-   Azure Service Bus

Las salidas de PowerBI, SQL y SQL Data Warehouse no admiten la creación de particiones. Sin embargo, de todos modos puede crear particiones de la entrada, tal como se describe en [está sección](#multi-step-query-with-a-grouping-key) 

Para más información sobre las particiones, vea los siguientes artículos:

* [Información general de las características de Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Creación de particiones de datos](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Trabajos embarazosamente paralelos
Un trabajo *embarazosamente paralelo* es el escenario más escalable que tenemos en Azure Stream Analytics. Conecta una partición de la entrada en una instancia de la consulta a una partición de la salida. Este paralelismo tiene los siguientes requisitos:

1. Si la lógica de la consulta depende de la misma clave que procesa la misma instancia de consulta, ha de asegurarse de que los eventos vayan a la misma partición de la entrada. En Event Hubs, esto significa que los datos del evento deben tener establecido el valor **PartitionKey**. También puede usar remitentes con particiones. En Blob Storage, esto significa que los eventos se envían a la misma carpeta de partición. Si la lógica de consulta no requiere que la misma instancia de consulta procese la misma clave, puede ignorar este requisito. Un ejemplo de esta lógica sería una sencilla consulta select-project-filter.  

2. Una vez dispuestos los datos en la salida, hay que asegurarse de que la consulta está particionada. Esto requiere el uso de **PARTITION BY** en todos los pasos. Se pueden usar varios pasos, pero todos deben particionarse con la misma clave. Actualmente, la clave de partición debe establecerse en **PartitionId** para que el trabajo sea totalmente paralelo.  

3. La mayoría de las salidas puede aprovechar la creación de particiones, pero si se usa un tipo de salida que no la admite, el trabajo no estará completamente en paralelo. Consulte la [sección de salida](#Outputs) para más detalles.

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

Esta consulta es un filtro sencillo. Por consiguiente, no hay que preocuparse por crear particiones en la entrada que se envía al centro de eventos. Observe que la consulta incluye **PARTITION BY PartitionId**, por lo que cumple el requisito 2 mencionado. Para la salida, debemos configurar la salida de Event Hub en el trabajo para que la clave de partición esté establecida en **PartitionId**. Una última comprobación consiste en asegurarse de que el número de particiones de entrada es igual al número de particiones de salida.

### <a name="query-with-a-grouping-key"></a>Consulta con una clave de agrupación

* Entrada: Event Hubs con ocho particiones
* Salida: Blob Storage

Consulta:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Esta consulta tiene una clave de agrupación. Por lo tanto, los eventos agrupados en conjunto se deben enviar a la misma partición de Event Hub. Como en este ejemplo la agrupación se hizo por TollBoothID, debemos asegurarnos de que TollBoothID se usa como la clave de partición cuando los eventos se envían a Event Hub. A continuación, en ASA se puede usar **PARTITION BY PartitionId** para heredar desde este esquema de partición y habilitar la paralelización completa. Puesto que la salida es Blob Storage, no hay que preocuparse en configurar un valor de clave de partición, como estipula el requisito 4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Escenarios de ejemplo que *no* son embarazosamente paralelos

En la sección anterior, se han mostrado algunos escenarios embarazosamente paralelos. En esta sección se describen escenarios en los que no se cumplen todos los requisitos para que sean embarazosamente paralelos. 

### <a name="mismatched-partition-count"></a>Recuento de particiones no coincidente
* Entrada: Event Hubs con ocho particiones
* Salida: Event Hubs con 32 particiones

En este caso, no importa cuál es la consulta. Si el recuento de particiones de entrada no coincide con el recuento de particiones de salida, la topología no es embarazosamente paralela. Sin embargo, de todos modos podemos obtener cierto nivel de paralelización.

### <a name="query-using-non-partitioned-output"></a>Consulta con salida sin particiones
* Entrada: Event Hubs con ocho particiones
* Salida: PowerBI

La salida de PowerBI no admite en este momento la creación de particiones. Por consiguiente, este escenario no es embarazosamente paralelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Consulta de varios pasos con diferentes valores de PARTITION BY
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

Consulta:

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
* La consulta comprendida en el paso debe incluir la palabra clave **PARTITION BY**.

Cuando una consulta está particionada, los eventos de entrada se procesan y agregan en grupos de particiones independientes, y se generan eventos de salida para cada uno de los grupos. Si quiere usar un agregado combinado, debe crear un segundo paso sin particionar que agregar.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Cálculo de las unidades máximas de streaming para un trabajo
Todos los pasos sin particiones juntos pueden escalarse hasta seis unidades de streaming (SU) para un trabajo de Stream Analytics. Además, puede agregar 6 SU para cada partición en un paso con particiones.
Puede ver algunos **ejemplos** en la tabla a continuación.

| Consultar                                               | Número máximo de unidades de streaming del trabajo |
| --------------------------------------------------- | ------------------- |
| <ul><li>La consulta contiene un solo paso.</li><li>El paso no está particionado.</li></ul> | 6 |
| <ul><li>El flujo de datos de entrada está particionado en 16.</li><li>La consulta contiene un solo paso.</li><li>El paso está particionado.</li></ul> | 96 (6 * 16 particiones) |
| <ul><li>La consulta contiene dos pasos.</li><li>Ninguno de los pasos está particionado.</li></ul> | 6 |
| <ul><li>La secuencia de datos de entrada está particionada en tres.</li><li>La consulta contiene dos pasos. El paso de entrada tiene particiones y el segundo paso no.</li><li>La instrucción <strong>SELECT</strong> se lee de la entrada particionada.</li></ul> | 24 (18 para los pasos particionados y 6 para los pasos no particionados) |

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

Cada una de las particiones de **Input1** se procesará por separado en Stream Analytics. Por consiguiente, se crearán varios registros de recuento de vehículos para la misma cabina en la misma ventana de saltos de tamaño constante. Si la clave de partición de entrada no se puede cambiar, este problema se puede solucionar agregando un paso sin particiones para agregar valores entre las particiones, como en el ejemplo siguiente:

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





## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

