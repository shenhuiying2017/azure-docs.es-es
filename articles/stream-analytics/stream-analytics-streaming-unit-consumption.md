---
title: "Azure Stream Analytics: descripción y ajuste de las unidades de streaming | Microsoft Docs"
description: Conozca los factores que afectan el rendimiento en Azure Stream Analytics.
keywords: unidad de streaming, rendimiento de consulta
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e8812f10662ee7b571e8e353074c2537d1a3181b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="understand-and-adjust-streaming-units"></a>Descripción y ajuste de las unidades de streaming

Azure Stream Analytics agrega el "peso" de rendimiento de ejecutar un trabajo en unidades de streaming (SU). Las SU representan los recursos informáticos que se usan para ejecutar un trabajo. Las SU proporcionan una forma de describir la capacidad de procesamiento del evento relativo en función de una medida que combina la CPU, la memoria y las tasas de lectura y escritura. Esta capacidad le permite centrarse en la lógica de consulta y elimina la necesidad de conocer las consideraciones de rendimiento de los niveles de almacenamiento, de asignar memoria para su trabajo manualmente y de aproximar el número de núcleos de CPU para ejecutar su trabajo de manera oportuna.

Con el fin de lograr un procesamiento de streaming de latencia baja, los trabajos de Azure Stream Analytics realizan todo el procesamiento en memoria. El trabajo de streaming presenta un error cuando se queda sin memoria. Como resultado, en el caso de un trabajo de producción, resulta importante supervisar el uso de recursos de un trabajo de streaming, y debe asegurarse de que hay recursos asignados suficientes para mantener los trabajos en ejecución de manera ininterrumpida.

La métrica es un número de porcentaje que va de 0 % a 100 %. Para un trabajo de streaming con una superficie mínima, la métrica de porcentaje de uso de SU suele estar entre 10 % y 20 %. Se recomienda mantener la métrica por debajo del 80 % para tener en cuenta las subidas ocasionales.  Puede establecer una alerta en la métrica (consulte [aquí para configurar las alertas de métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Configuración de las unidades de streaming (SU) de Stream Analytics
1. Inicie sesión en [Azure Portal](http://portal.azure.com/)
2. En la lista de recursos, busque el trabajo de Stream Analytics que quiere escalar y ábralo. 
3. En la página del trabajo, en Configurar, haga clic en **Escala**. 

    ![Configuración del trabajo de Stream Analytics en Azure Portal][img.stream.analytics.preview.portal.settings.scale]
    
4. Use el control deslizante para establecer las unidades de streaming del trabajo. Tenga en cuenta que está limitado a la configuración de unidades de streaming específica. 


## <a name="monitor-job-performance"></a>Supervisión del rendimiento del trabajo
Mediante Azure Portal, puede realizar el seguimiento de la capacidad de procesamiento de un trabajo:

![Trabajos de supervisión de Azure Stream Analytics][img.stream.analytics.monitor.job]

Calcule la capacidad de procesamiento esperada de la carga de trabajo. Si la capacidad de procesamiento es inferior a la esperada, ajuste la partición de entrada, ajuste la consulta y agregue unidades de streaming al trabajo.


## <a name="how-many-sus-are-required-for-a-job"></a>¿Cuántas SU son necesarias para un trabajo?

La elección del número de SU necesarias para un trabajo determinado depende de la configuración de particiones de las entradas y de la consulta definida dentro del trabajo. La página **Escala** le permite establecer el número correcto de SU. Es recomendable asignar más unidades de streaming de las necesarias. El motor de procesamiento de Stream Analytics se optimiza para la latencia y el rendimiento, a costa de asignar memoria adicional.

En general, el procedimiento recomendado es comenzar con 6 SU para consultas que no usen **PARTITION BY**. Luego, determine el punto favorable mediante un método de prueba y error en el que modifica el número de SU después de pasar las cantidades de datos representativas y examinar la métrica de porcentaje de uso de SU.

Para más información sobre cómo elegir el número correcto de SU, consulte está página: [Escalación de trabajos de Azure Stream Analytics para incrementar el rendimiento](stream-analytics-scale-jobs.md)

> [!Note]
> La elección del número de unidades de streaming que se necesitan para un trabajo en concreto depende de la configuración de particiones para las entradas y de la consulta definida para el trabajo. Puede seleccionar como máximo su cuota en unidades de streaming para un trabajo. De manera predeterminada, cada suscripción de Azure tiene una cuota máxima de 200 SU en todos los trabajos de análisis de una región específica. Para aumentar las unidades de streaming de sus suscripciones, contacte con [Soporte técnico de Microsoft](http://support.microsoft.com). Los valores válidos para unidades de streaming por trabajo son 1, 3, 6 y más en incrementos de 6.



## <a name="factors-increasing-su-utilization"></a>Factores que aumentan el porcentaje de uso de SU 
### <a name="stateful-query-logic"></a>Lógica de consulta con estado 
Una funcionalidad única de un trabajo de Azure Stream Analytics es realizar procesamiento con estado, como funciones de análisis temporal, combinaciones temporales y agregados en ventanas. Cada uno de estos operadores conserva algunos estados. 
#### <a name="windowed-aggregates"></a>Agregados en ventanas
El tamaño del estado de un agregado en ventanas es proporcional al número de grupos (cardinalidad) del grupo por operador. Por ejemplo, en la consulta siguiente, el número asociado con clusterid es la cardinalidad de la consulta. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Con el fin de mejorar los problemas causados por la cardinalidad alta en la consulta anterior, puede enviar eventos a Event Hub particionados por "clusterid" y escalar horizontalmente la consulta permitiendo que el sistema procese de manera independiente cada partición de entrada con **PARTITION BY**, como se muestra en el ejemplo siguiente:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

Una vez que la consulta está particionada, se extiende por varios nodos. Como resultado, el número de clusterid que entra en cada nodo se reduce, lo que disminuye la cardinalidad del grupo por operador. 

Las particiones de Event Hub se deben particionar según la clave de agrupación para evitar la necesidad de un paso de reducción. [Aquí](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview) se describen detalles adicionales. 
#### <a name="temporal-join"></a>Combinación temporal
El tamaño del estado de una combinación temporal es proporcional al número de eventos en el espacio de ondulación temporal de la combinación, que es la tasa de entrada del evento multiplicada por el tamaño del espacio de ondulación. 

La cantidad de eventos no coincidentes en la combinación afecta el uso de memoria de la consulta. La consulta siguiente busca las impresiones de anuncios que generan clics:

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

En este ejemplo, es posible que se muestren muchos anuncios, pero que pocas personas hagan clic en ellos, por lo que es necesario mantener todos los eventos en la ventana de tiempo. La memoria consumida es proporcional al tamaño de ventana y la tasa de eventos. 

Para corregir esto, envíe eventos a Event Hub particionados por las claves de combinación (en este caso, el identificador) y escale horizontalmente la consulta permitiendo que el sistema procese de manera independiente cada partición de entrada con **PARTITION BY**, como se muestra a continuación:

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

Una vez que la consulta está particionada, se extiende por varios nodos. Como resultado, el número de eventos que entra en cada nodo se reduce, lo que disminuye el tamaño del estado que se conserva en la ventana de la combinación. 
#### <a name="temporal-analytic-function"></a>Función de análisis temporal
El tamaño del estado de una función de análisis temporal es proporcional a la tasa de eventos multiplicada por la duración. La solución es similar a la combinación temporal. Puede escalar horizontalmente la consulta con **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Búfer desordenado 
El usuario puede configurar el tamaño de búfer desordenado en el panel de configuración de la ordenación de eventos. El búfer se usa para contener entradas para la duración de la ventana y para reordenarlas. El tamaño del búfer es proporcional a la tasa de entrada de eventos multiplicada por el tamaño de la ventana desordenada. El tamaño predeterminado de la ventana es 0. 

Para corregir esto, escale horizontalmente la consulta con **PARTITION BY**. Una vez que la consulta está particionada, se extiende por varios nodos. Como resultado, el número de eventos que entra en cada nodo se reduce, lo que disminuye el número de eventos en cada búfer de reordenación. 

### <a name="input-partition-count"></a>Número de particiones de entrada 
Cada partición de entrada de una entrada del trabajo tiene un búfer. Mientras más grande sea la cantidad de particiones de entrada, más recursos consume el trabajo. Para cada SU, Azure Stream Analytics puede procesar aproximadamente 1 MB/s de entrada, por lo que es posible que desee hacer coincidir el número de SU de ASA con la cantidad de particiones de Event Hub. Por lo general, un trabajo de 1 SU es suficiente para Event Hub con dos particiones (que es el mínimo para Event Hub). Si Event Hub tiene más particiones, el trabajo de ASA consume más recursos, pero no consume necesariamente el rendimiento adicional que proporciona Event Hub. En el caso de un trabajo de 6 SU, podría necesitar 4 u 8 particiones de Event Hub. Usar una instancia de Event Hub con 16 particiones o más en un trabajo de 1 SU a menudo contribuye a un uso excesivo de los recursos, por lo que se debe evitar. 

### <a name="reference-data"></a>Datos de referencia 
Los datos de referencia en ASA se cargan en la memoria para poder realizar una búsqueda rápida. Con la implementación actual, cada operación de combinación con datos de referencia conserva una copia de estos datos en la memoria, incluso si se combina varias veces con los mismos datos de referencia. Para consultas con **PARTITION BY**, cada partición tiene una copia de los datos de referencia, por lo que las particiones están completamente desacopladas. Con el efecto multiplicador, el uso de la memoria puede aumentar muchísimo rápidamente si combina con datos de referencia varias veces con varias particiones.  

#### <a name="use-of-udf-functions"></a>Uso de las funciones UDF
Cuando agrega una función UDF, Azure Stream Analytics carga el entorno de tiempo de ejecución de Java en la memoria. Esto afectará el porcentaje de SU.


## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Creación de consultas que se pueden paralelizar en Azure Stream Analytics](stream-analytics-parallelization.md)
* [Escalar los trabajos de Azure Stream Analytics para incrementar el rendimiento](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
