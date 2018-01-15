---
title: "Detección de anomalías en la guía de uso de Azure (versión preliminar) | Microsoft Docs"
description: "Use Stream Analytics y Machine Learning para detectar anomalías."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: ff8571c6447f32ef9a435f5200803e76f6013ffa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="using-the-anomalydetection-operator"></a>Uso del operador ANOMALYDETECTION

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar. No es aconsejable usarlo en producción.

El operador **ANOMALYDETECTION** se puede utilizar para detectar anomalías en los flujos de eventos.
Por ejemplo, una disminución lenta de la memoria libre durante un periodo prolongado puede indicar una fuga de memoria o que el número de solicitudes de servicio web que son estables en un intervalo puede aumentar o disminuir drásticamente.

Busca los siguientes tipos de anomalías a lo largo de la duración especificada:

- Cambio de nivel bidireccional
- Tendencia positiva lenta
- Tendencia negativa lenta

El intervalo de tiempo hasta el momento del historial que es preciso examinar el evento actual se limita mediante la cláusula **LIMIT DURATION**. Existe la opción de limitar **ANOMALYDETECTION** a los eventos que coinciden con una propiedad o condición concretas mediante la cláusula **WHEN**.

También existe la opción de procesar grupos de eventos por separado en función de la clave especificada en la cláusula **PARTITION BY**. El aprendizaje y la predicción se producen de forma independiente en cada partición.

## <a name="syntax"></a>Sintaxis

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Ejemplo de uso

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`|


## <a name="arguments"></a>Argumentos

- **scalar_expression**

  La expresión escalar en la que se realizaría la detección de anomalías. Es una expresión de tipo float o bigint que devuelve un valor (escalar) individual. La expresión comodín **\*** no está permitido. **scalar_expression** no puede contener otras funciones de análisis ni funciones externas.

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  La cláusula `PARTITION BY <partition key>` divide el aprendizaje teórico y el práctico en particiones independientes. En otras palabras, se usaría un modelo independiente por cada valor de `<partition key>` y solo se usarían los eventos con dicho valor se usaría para los aprendizajes teórico y práctico en ese modelo. Por ejemplo,

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  solo entrenará y puntuará una lectura con respecto a otras del mismo sensor.

- **limit_duration clause** DURATION(\<unit\>, \<length\>)

  Especifica la cantidad del historial del evento actual que se tiene en cuenta en el cálculo de **ANOMALYDETECTION**. Para ver una descripción detallada de las unidades que se admiten y sus abreviaturas, consulte DATEDIFF.

- **when_clause** 

  Especifica una condición booleana para los eventos que se tienen en cuenta en el cálculo de **ANOMALYDETECTION**.

## <a name="return-types"></a>Tipos de valor devuelto

La función devuelve un registro que contiene las tres puntuaciones como salida. Las propiedades asociadas a los distintos tipos de detectores de anomalías se llaman:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Para extraer los valores individuales del registro, use la función **GetRecordPropertyValue**. Por ejemplo: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 


Una anomalía de un tipo concreto se detecta cuando una de estas puntuaciones supera un umbral. El umbral puede ser cualquier número de punto flotante \>= 0. El umbral es un equilibrio entre la confidencialidad y la confianza. Por ejemplo, un umbral más bajo aumentaría la sensibilidad a los cambios y generaría más alertas, mientras que un umbral más alto podría reducir la sensibilidad de la detección y aumentar la seguridad pero enmascararía algunas anomalías. El valor de umbral exacto que se utiliza depende del escenario. No hay límite superior, pero el intervalo recomendado es 3,25-5.

## <a name="algorithm"></a>Algoritmo

**ANOMALYDETECTION** usa la semántica de ventana deslizante, lo que significa que el cálculo se ejecuta cada vez que un evento entra en la función y se genera una puntuación para dicho evento. El cálculo se basa en martingalas de intercambiabilidad, que funcionan mediante la comprobación de si ha cambiado la distribución de los valores del evento. En ese caso, se ha detectado una posible anomalía. El resultado devuelto es una indicación del nivel de confianza de dicha anomalía. Como optimización interna, **ANOMALYDETECTION** calcula la puntuación de la anomalía de un evento basado en eventos *d* a *2d*, donde *d* es el tamaño de la ventana de detección especificada.

**ANOMALYDETECTION** espera que la serie temporal de entrada sea uniforme. Para que un flujo de eventos sea uniforme, hay que agregarlo a una ventana de saltos de tamaño constante o a una ventana de salto. En los escenarios en los que el espacio entre eventos es siempre menor que la ventana de agregación, una ventana de saltos de tamaño constante es suficiente para que la serie temporal sea uniforme. Si el espacio puede ser mayor, los espacios se pueden rellenar repitiendo el último valor mediante una ventana de salto. En el ejemplo siguiente se pueden controlar ambos escenarios.

## <a name="performance-guidance"></a>Guía de rendimiento

- Utilice 6 SU para los trabajos. 
- Enviar eventos con un mínimo de 1 segundo de diferencia.
- Una consulta sin particiones que use la función **ANOMALYDETECTION** puede generar resultados con una latencia media de cálculo de aproximadamente 25 ms.
- La latencia que experimenta una consulta con particiones varía ligeramente con el número de particiones, ya que el número de cálculos es mayor. Sin embargo, la latencia es aproximadamente la misma que en el caso sin particiones en un número total comparable de eventos de todas las particiones.
- Aunque se leen datos que no están en tiempo real, una gran cantidad de ellos se ingiere rápidamente. Actualmente, el procesamiento de estos datos es considerablemente más lento. Se detectó que la latencia en dichos escenarios aumenta linealmente con el número de puntos de datos de la ventana, en lugar del tamaño de la ventana o del intervalo de eventos "per se". Para reducir la latencia en los casos que no están en tiempo real, considere la posibilidad de usar una ventana de menor tamaño. Como alternativa, considere la posibilidad de comenzar el trabajo en el momento actual. Algunos ejemplos de latencias en una consulta sin particiones: 
    - Sesenta puntos de datos en la ventana de detección pueden producir una latencia de 10 segundos con un rendimiento de 3 Mbps. 
    - En 600 puntos de datos, la latencia puede llegar a aproximadamente 80 segundos con un rendimiento de 0,4 Mbps.

## <a name="example"></a>Ejemplo

La siguiente consulta se puede utilizar para generar una alerta si se detecta una anomalía.
Cuando el flujo de entrada no es uniforme, el paso de la agregación puede ayudarle a transformarla en una serie temporal uniforme. En el ejemplo se utiliza **AVG**, pero el tipo de agregación concreto depende del escenario del usuario. Además, cuando una serie temporal tiene espacios mayores que la ventana de agregación, no habrá en la serie temporal eventos que desencadenen la detección de anomalías (según la semántica de ventana deslizante). Como consecuencia, la suposición de uniformidad se interrumpirá cuando llegue el siguiente evento. En dichas situaciones, se necesita una forma de rellenar los espacios en la serie temporal. Un posible enfoque es tomar el último evento de cada ventana de salto, como se muestra a continuación.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25

## <a name="references"></a>Referencias

* [Anomaly Detection – Using Machine Learning to Detect Abnormalities in Time Series Data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) (Detección de anomalías: uso de Machine Learning para detectar anomalías en los datos de una serie temporal)
* [API de detección de anomalías de Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Detección de anomalías en una series temporal](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

