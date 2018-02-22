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
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: d8762ea608afed707d41a3c0a1a8725457a0e4dc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detección de anomalías en Azure Stream Analytics

> [!IMPORTANT]
> Esta funcionalidad está en versión preliminar, por lo que no es recomendable usarla con las cargas de trabajo de producción.

El operador **AnomalyDetection** se utiliza para detectar diferentes tipos de anomalías en los flujos de eventos. Por ejemplo, una disminución lenta de la memoria libre durante un periodo prolongado puede indicar una fuga de memoria o que el número de solicitudes de servicio web que son estables en un intervalo puede aumentar o disminuir drásticamente.  

El operador AnomalyDetection detecta tres tipos de anomalías: 

* **Cambio del nivel bidireccional**: un aumento o disminución sostenido en el nivel de valores, tanto al alza como a la baja. Este valor es diferente de los picos y caídas, que son cambios instantáneos o de corta duración.  

* **Tendencia positiva lenta**: un aumento lento en la tendencia con el tiempo.  

* **Tendencia negativa lenta**: una disminución lenta en la tendencia con el tiempo.  

Cuando se utiliza el operador AnomalyDetection, debe especificar la cláusula **Limit Duration**. Esta cláusula especifica el intervalo de tiempo (cuánto tiempo atrás en el historial del evento actual) que debe tenerse en cuenta al detectar anomalías. Este operador puede limitarse opcionalmente solo a los eventos que coinciden con una propiedad o condición concretas mediante la cláusula  **When** . Este operador también tiene la opción de procesar grupos de eventos por separado en función de la clave especificada en la cláusula  **Partition by** . El aprendizaje y la predicción se producen de forma independiente para cada partición. 

## <a name="syntax-for-anomalydetection-operator"></a>Sintaxis del operador AnomalyDetection

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Ejemplo de uso**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumentos

* **scalar_expression**: la expresión escalar en la que se realizaría la detección de anomalías. Los valores permitidos para este parámetro incluyen los tipos de datos Float o Bigint que devuelven un solo valor (escalar). La expresión comodín **\*** no está permitido. La expresión escalar no puede contener otras funciones de análisis ni funciones externas. 

* **partition_by_clause**: la cláusula `PARTITION BY <partition key>` divide el aprendizaje teórico y el práctico en particiones independientes. En otras palabras, se usaría un modelo independiente por cada valor de `<partition key>` y solo se usarían los eventos con dicho valor se usaría para los aprendizajes teórico y práctico en ese modelo. Por ejemplo, la siguiente consulta entrena y califica una lectura frente a otras lecturas solamente del mismo sensor:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **cláusula limit_duration** `DURATION(<unit>, <length>)`: especifica el intervalo de tiempo (cuánto tiempo atrás en el historial del evento actual) que debe tenerse en cuenta al detectar anomalías. Consulte [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) para ver una descripción detallada de las unidades que se admiten y sus abreviaturas. 

* **when_clause**: especifica una condición booleana para los eventos que se tienen en cuenta en el cálculo de la detección de anomalías.

### <a name="return-types"></a>Tipos de valor devuelto

El operador AnomalyDetection devuelve un registro que contiene las tres puntuaciones como salida. Las propiedades asociadas a los distintos tipos de detectores de anomalías son:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Para extraer los valores individuales del registro, use la función **GetRecordPropertyValue**. Por ejemplo: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

La anomalía de un tipo se detecta cuando una de estas puntuaciones supera un umbral. El umbral puede ser cualquier número de punto flotante >= 0. El umbral es un equilibrio entre la confidencialidad y la confianza. Por ejemplo, un umbral más bajo aumentaría la sensibilidad a los cambios y generaría más alertas, mientras que un umbral más alto podría reducir la sensibilidad de la detección y aumentar la seguridad pero enmascararía algunas anomalías. El valor de umbral exacto que se utiliza depende del escenario. No hay límite superior, pero el rango recomendado es 3,25-5. 

## <a name="anomaly-detection-algorithm"></a>Algoritmo de detección de anomalías

* El operador AnomalyDetection utiliza un enfoque de **aprendizaje no supervisado** donde no asume ningún tipo de distribución en los eventos. En general, se mantienen dos modelos en paralelo en cualquier momento dado, donde uno de ellos se utiliza para calificar y el otro se entrena en el fondo. Los modelos de detección de anomalías se entrenan usando datos del flujo actual en lugar de usar un mecanismo fuera de banda. La cantidad de datos utilizados para el aprendizaje depende del tamaño de ventana d especificado por el usuario dentro del parámetro Limit Duration. Cada modelo termina siendo entrenado en base a eventos por valor d a 2d. Se recomienda tener al menos 50 eventos en cada ventana para obtener mejores resultados. 

* El operador AnomalyDetection utiliza **la semántica de ventana deslizante** para entrenar modelos y eventos de puntuación. Lo que significa que cada evento se evalúa en busca de anomalías y se devuelve una puntuación. La puntuación es una indicación del nivel de confianza de dicha anomalía. 

* El operador AnomalyDetection proporciona una **garantía de repetibilidad**, es decir, que la misma entrada siempre produce la misma puntuación independientemente de la hora de inicio de la salida del trabajo. La hora de inicio de la salida del trabajo representa la hora en la que el trabajo produce el primer evento de salida. El usuario lo establece a la hora actual, a un valor personalizado o a la última hora de salida (si el trabajo había producido una salida anteriormente). 

### <a name="training-the-models"></a>Aprendizaje de los modelos 

A medida que el tiempo avanza, los modelos se entrenan con diferentes datos. Para dar sentido a las puntuaciones, ayuda a entender el mecanismo subyacente por el cual se entrenan los modelos. En este caso, **t<sub>0</sub>**  es la **hora de inicio de salida del trabajo** y **d** es el **tamaño de la ventana** del parámetro Limit Duration. Se supone que el tiempo se divide en **saltos de tamaño d**, comenzando por `01/01/0001 00:00:00`. Los siguientes pasos se utilizan para entrenar al modelo y puntuar los eventos:

* Cuando se inicia un trabajo, lee los datos comenzando en el tiempo t<sub>0</sub> - 2d.  
* Cuando el tiempo llega al próximo salto, se crea un nuevo modelo M1 y comienza a entrenarse.  
* Cuando el tiempo avanza por otro salto, se crea un nuevo modelo M2 y comienza a entrenarse.  
* Cuando el tiempo alcanza t<sub>0</sub>, M1 se activa y su puntuación comienza a generarse.  
* En el próximo salto, suceden tres cosas al mismo tiempo:  

  * M1 ya no es necesario y se descarta.  
  * M2 se ha entrenado suficientemente para que se utilice para la puntuación.  
  * Un nuevo modelo M3 se crea e inicia el aprendizaje en segundo plano.  

* Este ciclo se repite para cada salto, donde se descarta el modelo activo, se cambia al modelo paralelo y se inicia el entrenamiento de un tercer modelo en segundo plano. 

En el diagrama, los pasos se ven como sigue: 

![Aprendizaje de modelos](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **Hora de inicio del aprendizaje** | **Tiempo para empezar a usar su puntuación** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* El modelo M1 inicia el entrenamiento a las 11:20 a. m., que es el próximo salto después de que el trabajo empiece a leer a las 11:13 a. m. La primera salida se produce desde M1 a las 11:33 a. m. después del entrenamiento con 13 minutos de datos. 

* Un nuevo modelo M2 también comienza el aprendizaje a las 11:30 a. m. pero su puntuación no se utiliza hasta las 11:40 a. m., que es después de que se haya entrenado con 10 minutos de datos. 

* M3 sigue el mismo patrón que M2. 

### <a name="scoring-with-the-models"></a>Puntuación con los modelos 

En el nivel de Machine Learning, el algoritmo de detección de anomalías calcula un valor de extrañeza para cada evento entrante y lo compara con los eventos de una ventana de historial. El cálculo de extrañeza es diferente para cada tipo de anomalía.  

Revisemos el cálculo de la extrañeza en detalle (supongamos que existe un conjunto de ventanas de historiales con eventos): 

1. **Cambio del nivel de bidireccional:** en función de la ventana de historial, se calcula un rango operativo normal como [Percentil 10º, Percentil 90º]; es decir, el valor del percentil 10° como límite inferior y el valor del percentil 90° como límite superior. Se calcula un valor de extrañeza para el evento actual:  

   - 0, si event_value se encuentra en el rango operativo normal  
   - event_value/90th_percentile, si event_value > 90th_percentile  
   - 10th_percentile/event_value, si event_value es < 10th_percentile  

2. **Tendencia positiva lenta:** se calcula una línea de tendencia sobre los valores del evento en la ventana de historial y buscamos una tendencia positiva dentro de la línea. El valor de extrañeza se calcula como:  

   - Pendiente, si la pendiente es positiva  
   - 0, de lo contrario. 

1. **Tendencia negativa lenta:** se calcula una línea de tendencia sobre los valores de evento en la ventana de historial y buscamos tendencia negativa dentro de la línea. El valor de extrañeza se calcula como: 

   - Pendiente, si la pendiente es negativa  
   - 0, de lo contrario.  

Una vez que se calcula el valor de extrañeza para el evento entrante, se calcula un valor de Martingala basado en el valor de extrañeza (consulte el [blog de Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) para más información sobre cómo se calcula el valor de Martingala). Se devuelve este valor de Martingala como puntuación de anomalía. El valor de Martingala aumenta lentamente en respuesta a valores extraños, lo que permite al detector permanecer consistente a los cambios esporádicos y reduce las falsas alertas. También tiene una propiedad útil: 

Probabilidad [existe t tal que M<sub>t</sub> > λ] < 1/λ, donde M<sub>t</sub> es el valor de Martingala en el instante t y λ es un valor real. Por ejemplo, si se alerta cuando M<sub>t</sub>> 100, entonces la probabilidad de falsos positivos es inferior a 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Guía para usar el detector de cambios de nivel bidireccional 

El detector de cambios de nivel bidireccional se puede utilizar en situaciones como una pérdida y recuperación de energía, o tráfico en hora punta, etc. Sin embargo, funciona de tal manera que una vez que un modelo se entrena con ciertos datos, otro cambio de nivel es anómalo si y solo si el nuevo valor es mayor que el límite superior anterior (caso de cambio de nivel ascendente) o menor que el límite inferior anterior (caso de cambio de nivel descendente). Por lo tanto, un modelo no debe ver valores de datos en el rango del nuevo nivel (ascendente o descendente) en su ventana de aprendizaje para que sean considerados anómalos. 

Deben tener en cuenta los siguientes puntos cuando se usa este detector: 

1. Cuando la serie temporal ve de repente un aumento o una caída en el valor, el operador AnomalyDetection lo detecta. Pero detectar el regreso a la normalidad requiere más planeación. Si una serie temporal se encontraba en estado estable antes de la anomalía, lo que puede lograrse mediante el establecimiento de la ventana de detección del operador AnomalyDetecttion a lo sumo a la mitad de la longitud de la anomalía. En este escenario se asume que la duración mínima de la anomalía puede ser calculada con anticipación, y que hay suficientes eventos en ese plazo de tiempo para entrenar el modelo de manera suficiente (al menos 50 eventos). 

   Esto se muestra en las figuras 1 y 2, a continuación, que utilizan un cambio de límite superior (la misma lógica se aplica a un cambio de límite inferior). En ambas ilustraciones, las formas de onda constituyen un cambio de nivel anómalo. Las líneas naranja verticales indican los límites del salto y el tamaño del salto es el mismo que la ventana de detección especificada en el operador AnomalyDetection. Las líneas verdes indican el tamaño de la ventana de aprendizaje. En la figura 1, el tamaño de salto es igual al tiempo durante el cual dura la anomalía. En la figura 2, el tamaño de salto es la mitad del tiempo durante el cual dura la anomalía. En todos los casos, se detecta un cambio al alza porque el modelo utilizado para la puntuación se ha entrenado con datos normales. Pero basándonos en cómo funciona el detector de cambios de nivel bidireccional, debemos excluir los valores normales de la ventana de aprendizaje utilizada para el modelo que puntúa la vuelta a la normalidad. En la figura 1, el aprendizaje del modelo de puntuación incluye algunos eventos normales, por lo que no se puede detectar la vuelta a la normalidad. Pero en la figura 2, el aprendizaje solo incluye la parte anómala, lo que asegura que se detecte la vuelta a la normalidad. Cualquier valor menor que la mitad también funciona por la misma razón, mientras que cualquier valor mayor terminará incluyendo un poco de los eventos normales. 

   ![Detección de anomalías con tamaño de ventana igual a la longitud de la anomalía](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![Detección de anomalías con tamaño de ventana igual a la mitad de la longitud de la anomalía](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. En aquellos casos en los que no se puede predecir la longitud de la anomalía, este detector funciona con el mejor esfuerzo. Sin embargo, elegir una ventana de tiempo más estrecha limita los datos del aprendizaje, lo que aumentaría la probabilidad de detectar la vuelta a la normalidad. 

3. En el siguiente escenario, la anomalía más larga no se detecta ya que la ventana de aprendizaje incluye una anomalía del mismo valor elevado. 

   ![Anomalías con el mismo tamaño](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Consulta de ejemplo para detectar anomalías 

La siguiente consulta se puede utilizar para generar una alerta si se detecta una anomalía.
Cuando el flujo de entrada no es uniforme, el paso de la agregación puede ayudarle a transformarla en una serie temporal uniforme. En el ejemplo se utiliza AVG, pero el tipo de agregación concreto depende del escenario del usuario. Además, cuando una serie temporal tiene espacios mayores que la ventana de agregación, no hay en la serie temporal eventos que desencadenen la detección de anomalías (según la semántica de ventana deslizante). Como consecuencia, la suposición de uniformidad se interrumpe cuando llegue el siguiente evento. En estas situaciones, deben llenarse los vacíos de la serie temporal. Un posible enfoque es tomar el último evento de cada ventana de salto, como se muestra a continuación.

```sql
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
```

## <a name="performance-guidance"></a>Guía de rendimiento

* Use seis unidades de streaming para los trabajos. 
* Envíe eventos con un mínimo de un segundo de diferencia.
* Una consulta sin particiones que use el operador AnomalyDetection puede generar resultados con una latencia media de cálculo de aproximadamente 25 ms.
* La latencia que experimenta una consulta con particiones varía ligeramente con el número de particiones, ya que el número de cálculos es mayor. Sin embargo, la latencia es aproximadamente la misma que en el caso sin particiones en un número total comparable de eventos de todas las particiones.
* Aunque se leen datos que no están en tiempo real, una gran cantidad de ellos se ingiere rápidamente. Actualmente, el procesamiento de estos datos es más lento. Se detectó que la latencia en dichos escenarios aumenta linealmente con el número de puntos de datos de la ventana, en lugar del tamaño de la ventana o del intervalo de eventos. Para reducir la latencia en los casos que no están en tiempo real, considere la posibilidad de usar una ventana de menor tamaño. Como alternativa, considere la posibilidad de comenzar el trabajo en el momento actual. Algunos ejemplos de latencias en una consulta sin particiones: 
    - Sesenta puntos de datos en la ventana de detección pueden producir una latencia de 10 segundos con un rendimiento de 3 Mbps. 
    - En 600 puntos de datos, la latencia puede llegar a aproximadamente 80 segundos con un rendimiento de 0,4 Mbps.

## <a name="limitations-of-the-anomalydetection-operator"></a>Limitaciones del operador AnomalyDetection 

* Este operador no admite actualmente la detección de picos e interrupciones. Los picos y caídas son cambios espontáneos o de corta duración en la serie temporal. 

* Este operador no trata actualmente patrones de estacionalidad. Los patrones de estacionalidad son patrones repetidos en los datos, por ejemplo, un comportamiento diferente del tráfico web durante los fines de semana o diferentes tendencias de compras durante el Viernes Negro, que no son anomalías sino un patrón esperado en el comportamiento. 

* Este operador espera que la serie temporal de entrada sea uniforme. Para que un flujo de eventos sea uniforme, hay que agregarlo a una ventana de saltos de tamaño constante o a una ventana de salto. En los escenarios en los que el espacio entre eventos es siempre menor que la ventana de agregación, una ventana de saltos de tamaño constante es suficiente para que la serie temporal sea uniforme. Si el espacio puede ser mayor, los espacios se pueden rellenar repitiendo el último valor mediante una ventana de salto. 

## <a name="references"></a>Referencias

* [Anomaly Detection – Using Machine Learning to Detect Abnormalities in Time Series Data](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) (Detección de anomalías: uso de Machine Learning para detectar anomalías en los datos de una serie temporal)
* [API de detección de anomalías de Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Time series anomaly detection](https://msdn.microsoft.com/library/azure/mt775197.aspx) (Detección de anomalías en una serie temporal)

## <a name="next-steps"></a>pasos siguientes

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

