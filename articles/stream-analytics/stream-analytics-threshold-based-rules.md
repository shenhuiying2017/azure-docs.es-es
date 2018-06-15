---
title: Procesar reglas configurables basadas en umbrales en Azure Stream Analytics
description: En este artículo se describe cómo usar los datos de referencia para lograr una solución de alertas que tenga reglas configurables basadas en umbrales Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 1c131c2c9ca12556c1d2cd52e7976d2f4272a0c8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32768039"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Procesar reglas configurables basadas en umbrales en Azure Stream Analytics
En este artículo se describe cómo usar los datos de referencia para lograr una solución de alertas que use reglas configurables basadas en umbrales Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Escenario: alertas basadas en umbrales de regla ajustables
Es posible que deba generar una alerta cuando los eventos de streaming de entrada hayan alcanzado un determinado valor, o cuando un valor agregado basado en los eventos de streaming de entrada supere un cierto umbral. Es fácil configurar una consulta de Stream Analytics que compare el valor con un umbral estático que sea fijo y predeterminado. Un umbral fijo puede estar codificado de forma rígida en la sintaxis de consultas de streaming mediante comparaciones numéricas simples (mayor que, menor que y es igual a).

En algunos casos, los valores de umbral deben poder configurarse con mayor facilidad sin necesidad de editar la sintaxis de consulta cada vez que cambia un valor de umbral. En otros casos, puede que necesite que la misma consulta procese varios dispositivos o usuarios, donde cada una tenga valores de umbral distintos en cada tipo de dispositivo. 

Este patrón puede usarse para configurar dinámicamente los umbrales, elegir de forma selectiva qué tipo de dispositivo aplica el umbral mediante el filtrado de los datos de entrada, y elegir de forma selectiva qué campos desea incluir en la salida.

## <a name="recommended-design-pattern"></a>Patrón de diseño recomendado
Utilizar una entrada de datos de referencia a un trabajo de Stream Analytics como búsqueda de los umbrales de alerta:
- Almacene los valores de umbral en los datos de referencia, un valor por clave.
- Una los eventos de entrada de datos de streaming a los datos de referencia en la columna de clave.
- Utilice el valor con clave de los datos de referencia como valor de umbral.

## <a name="example-data-and-query"></a>Ejemplo de datos y consulta
En el ejemplo, las alertas se generan cuando el agregado del flujo de datos que provienen de los dispositivos en una ventana de un minuto coincide con los valores previstos en la regla que se proporciona como datos de referencia.

En la consulta, para cada deviceId y cada metricName en deviceId, puede configurar de 0 a 5 dimensiones en GROUP BY. Solo se agrupan los eventos que tengan los valores de filtro correspondientes. Una vez agrupados, los agregados de ventanas de Min, Max, Avg se calculan en una ventana de saltos de tamaño constante de 60 segundos. Luego se calculan los filtros en los valores agregados, según el umbral configurado en la referencia, para generar el evento de salida de alerta.

Por ejemplo, suponga que hay un trabajo de Stream Analytics que tiene una entrada de datos de referencia con el nombre **rules**, la entrada de datos de streaming con el nombre **metrics**. 

## <a name="reference-data"></a>Datos de referencia
Estos datos de referencia de ejemplo muestran cómo podría representarse una regla basada en umbrales. Un archivo JSON contiene los datos de referencia y se guarda en el almacenamiento de blobs de Azure, y ese contenedor de almacenamiento de blobs se utiliza como entrada de datos de referencia con el nombre **rules**. Puede sobrescribir este archivo JSON y reemplazar la configuración de la regla a medida que pasa el tiempo, sin detener ni iniciar el trabajo de streaming.

- La regla del ejemplo se utiliza para representar una alerta ajustable cuando la CPU supera (el promedio es mayor o igual que) el valor del `90` por ciento. El campo `value` puede configurarse según sea necesario.
- Tenga en cuenta que la regla tiene un campo **operator**, que se interpreta dinámicamente en la sintaxis de consulta más adelante en `AVGGREATEROREQUAL`. 
- La regla filtra los datos en una clave de dimensión determinada `2` con el valor `C1`. Otros campos son una cadena vacía, lo que indica que no se filtre el flujo de entrada según esos campos de evento. Podría configurar reglas de CPU adicionales para filtrar otros campos coincidentes según sea necesario.
- No todas las columnas deben incluirse en el evento de alerta de salida. En este caso, la clave `includedDim` número `2` está activada `TRUE` para representar que el campo número 2 de datos del evento en el flujo se incluirá en los eventos de salida aptos. Los demás campos no se incluyen en el resultado de la alerta, pero se puede ajustar la lista de campos.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Ejemplo de consulta de streaming
Esta consulta de Stream Analytics de ejemplo une los datos de referencia de **rule** del ejemplo anterior, en un flujo de entrada de datos denominado **metrics**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Ejemplo de datos de evento de entrada de streaming
En este ejemplo, los datos JSON representan los datos de entrada de **metrics** que se utilizan en la consulta de streaming anterior. 

- Tres eventos de ejemplos se muestran en el intervalo de tiempo de 1 minuto, valor `T14:50`. 
- Los tres tienen el mismo valor de `deviceId` `978648`.
- Los valores de métrica de CPU varían en cada evento, `98`, `95`, `80`, respectivamente. Solo los dos primeros eventos de ejemplo superan la regla de alerta de CPU establecida en la regla.
- El campo includeDim en la regla de alerta era la clave número 2. El campo correspondiente de clave 2 en los eventos de ejemplo se denomina `NodeName`. Los tres eventos de ejemplo tienen valores `N024`, `N024`, y `N014`, respectivamente. En la salida, verá solo el nodo `N024`, ya que estos son los únicos datos que coinciden con los criterios de alerta para el uso elevado de la CPU. `N014` no cumple el umbral de uso elevado de la CPU.
- La regla de alertas se configura solo con un `filter` en la clave número 2, que se corresponde con el campo `cluster` en los eventos de ejemplo. Los tres eventos de ejemplo tienen el valor `C1` y coinciden con los criterios de filtro.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Salida de ejemplo
En este ejemplo, los datos de salida JSON muestran que se produjo un evento de alerta único basado en la regla de umbral de CPU definida en los datos de referencia. El evento de salida contiene el nombre de la alerta, así como el agregado (promedio, mínimo, máximo) de los campos que se consideran. Los datos del evento de salida incluyen la clave número 2 del campo `NodeName` con valor `N024` debido a la configuración de la regla. (El JSON se modificó para mostrar los saltos de línea para mejorar la legibilidad).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```