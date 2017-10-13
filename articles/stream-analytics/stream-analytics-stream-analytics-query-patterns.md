---
title: Ejemplos de consulta para patrones de uso comunes de Stream Analytics | Microsoft Docs
description: Patrones de consulta comunes de Azure Stream Analytics
keywords: ejemplos de consultas
services: stream-analytics
documentationcenter: 
author: samacha
manager: jenniehubbard
editor: cgronlun
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: a27bae1828bd469d4439e0ce43098edd73f54243
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Ejemplos de consulta para patrones de uso comunes de Stream Analytics
## <a name="introduction"></a>Introducción
Las consultas de Azure Stream Analytics se expresan en un lenguaje de consulta similar a SQL. Estas consultas se documentan en la guía [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). En este artículo se describen las soluciones para varios patrones de consulta comunes basados en situaciones del mundo real. Es un trabajo en curso y continúa actualizándose con nuevos patrones de forma continuada.

## <a name="query-example-convert-data-types"></a>Ejemplo de consulta: conversión de tipos de datos
**Descripción**: defina los tipos de propiedades en el flujo de entrada.
Por ejemplo, el peso del vehículo se incorpora al flujo de entrada como cadena y se debe convertir en **INT** para realizar la operación **SUM**.

**Entrada**:

| Asegúrese | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Salida**:

| Asegúrese | Peso |
| --- | --- |
| Honda |3000 |

**Solución**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicación**: use una instrucción **CAST** en el campo **Weight** (Peso) para especificar su tipo de datos. Vea la lista de tipos de datos admitidos en [Data types (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx) [Tipos de datos (Azure Stream Analytics)].

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Ejemplo de consulta: uso de Like/Not like para realizar la coincidencia de patrones
**Descripción**: compruebe que el valor de un campo del evento coincide con un patrón determinado.
Por ejemplo, compruebe que el resultado devuelve las matrículas que empiezan por A y terminan en nueve.

**Entrada**:

| Asegúrese | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Salida**:

| Asegúrese | LicensePlate | Hora |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Solución**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explicación**: use la instrucción **LIKE** para comprobar el valor del campo **LicensePlate**. Debe comenzar con una A, seguida de cualquier cadena de ceros o más caracteres y terminar en nueve. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Ejemplo de consulta: especificación de la lógica para los distintos casos/valores (instrucciones CASE)
**Descripción**: proporcione un cálculo diferente para un campo en función de un criterio concreto.
Por ejemplo, proporcione una descripción de cadena para el número de vehículos que han pasado de la misma marca, con un caso especial para 1.

**Entrada**:

| Asegúrese | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Salida**:

| CarsPassed | Hora |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyotas |2015-01-01T00:00:10.0000000Z |

**Solución**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explicación**: la cláusula **CASE** permite proporcionar un cálculo diferente en función de criterios determinados (en nuestro caso, el número de automóviles en la ventana de agregado).

## <a name="query-example-send-data-to-multiple-outputs"></a>Ejemplo de consulta: envío de datos a varias salidas
**Descripción**: envíe datos a varios destinos de salida desde un único trabajo.
Por ejemplo, analice los datos para una alerta de umbral y archive todos los eventos en Blob Storage.

**Entrada**:

| Asegúrese | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Salida1**:

| Asegúrese | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Salida2**:

| Asegúrese | Hora | Recuento |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Solución**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explicación**: la cláusula **INTO** indica a Stream Analytics en cuál de las salidas se escribirán los datos de esta instrucción.
La primera consulta es una transferencia de los datos que se recibe en una salida que denominamos **ArchiveOutput**.
La segunda consulta hace una agregación y un filtrado simples y envía los resultados a un sistema de alertas descendente.

Tenga en cuenta que también puede reutilizar los resultados de las expresiones de tabla comunes (CTE), como las instrucciones **WITH**, en varias instrucciones de salida. Esta opción ofrece el beneficio adicional de la apertura de algunos lectores para el origen de entrada.
Por ejemplo: 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-count-unique-values"></a>Ejemplo de consulta: recuento de valores únicos
**Descripción**: cuente el número de valores de campo únicos que aparecen en el flujo durante una ventana de tiempo determinada.
Por ejemplo, ¿cuántas marcas de vehículos únicas pasan a través de la cabina de peaje en un intervalo de dos segundos?

**Entrada**:

| Asegúrese | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Salida:**

| Recuento | Hora |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Solución:**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Explicación:**
** COUNT(DISTINCT Make)** devuelve la cantidad de valores distintos de la columna **Make** dentro de una ventana de tiempo.

## <a name="query-example-determine-if-a-value-has-changed"></a>Ejemplo de consulta: determinar si un valor ha cambiado
**Descripción**: busque un valor anterior para determinar si es diferente del valor actual.
Por ejemplo, ¿el vehículo anterior de la autopista de peaje es de la misma marca que el actual?

**Entrada**:

| Asegúrese | Hora |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Salida**:

| Asegúrese | Hora |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Solución**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explicación**: use **LAG** para revisar en el flujo de entrada de un evento anterior y obtener el valor **Marca**. A continuación, compárelo con el de la **marca** del evento actual y genere la salida del evento si son distintos.

## <a name="query-example-find-the-first-event-in-a-window"></a>Ejemplo de consulta: búsqueda del primer evento en una ventana
**Descripción**: ¿desea buscar el primer vehículo en un intervalo de cada diez minutos?

**Entrada**:

| LicensePlate | Asegúrese | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Salida**:

| LicensePlate | Asegúrese | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Solución**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Ahora se va a cambiar el problema y se va a buscar el primer vehículo de una marca concreta en un intervalo de cada diez minutos.

| LicensePlate | Asegúrese | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solución**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Ejemplo de consulta: búsqueda del último evento en una ventana
**Descripción**: busque el último vehículo en un intervalo de cada diez minutos.

**Entrada**:

| LicensePlate | Asegúrese | Hora |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Salida**:

| LicensePlate | Asegúrese | Hora |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solución**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explicación**: hay dos pasos en la consulta. El primero consiste en buscar la última marca de tiempo en ventanas de diez minutos. El segundo paso combina los resultados de la primera consulta con el flujo original para buscar eventos que coinciden con las últimas marcas de tiempo en cada ventana. 

## <a name="query-example-detect-the-absence-of-events"></a>Ejemplo de consulta: detectar la ausencia de eventos
**Descripción**: compruebe que un flujo no tiene ningún valor que cumpla un criterio determinado.
Por ejemplo, ¿han entrado dos vehículos consecutivos de la misma marca en la autopista de peaje durante los últimos noventa segundos?

**Entrada**:

| Asegúrese | LicensePlate | Hora |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Salida**:

| Asegúrese | Hora | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Solución**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explicación**: use **LAG** para revisar en el flujo de entrada de un evento anterior y obtener el valor **Marca**. Compárelo con el valor **MARCA** del evento actual y después genere la salida del evento en caso de que los valores sean los mismos. También puede usar **LAG** para obtener datos sobre el vehículo anterior.

## <a name="query-example-detect-the-duration-between-events"></a>Ejemplo de consulta: detección de la duración entre eventos
**Descripción**: busque la duración de un evento determinado. Por ejemplo, dada una secuencia de clics de web, determine el tiempo invertido en una característica.

**Entrada**:  

| Usuario | Característica | Evento | Hora |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Iniciar |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |End |2015-01-01T00:00:08.0000000Z |

**Salida**:  

| Usuario | Característica | Duración |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solución**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explicación**: use la función **LAST** para recuperar el último valor **TIME** en el que el tipo de evento era **Start**. La función **LAST** usa **PARTITION BY [user]** para indicar que el resultado se calcula por usuario único. La consulta tiene un umbral máximo de una hora para el intervalo de tiempo entre eventos **Start** y **Stop**, pero se puede configurar según sea necesario **(LIMIT DURATION(hour, 1)**.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Ejemplo de consulta: detección de la duración de una condición
**Descripción**: averigüe la duración de una condición.
Por ejemplo, supongamos que, por error, todos los vehículos tienen un peso incorrecto (por encima de 20 000 libras). Queremos calcular la duración del error.

**Entrada**:

| Asegúrese | Hora | Peso |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Salida**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Solución**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explicación**: use **LAG** para ver el flujo de entrada que se produjo durante veinticuatro horas y busque instancias donde**StartFault** y **StopFault** superan el peso de &lt;20 000.

## <a name="query-example-fill-missing-values"></a>Ejemplo de consulta: rellenar los valores que faltan
**Descripción**: para la transmisión de eventos con valores que faltan, genere una transmisión de eventos con intervalos regulares.
Por ejemplo, genere un evento cada cinco segundos que notifique el punto de datos visto más recientemente.

**Entrada**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5 |
| "2014-01-01T06:01:35" |6 |

**Salida (10 primeras filas)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5 |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6 |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6 |

**Solución**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explicación**: esta consulta genera eventos cada cinco segundos y genera como resultado el último evento que se recibió anteriormente. La duración de la [ventana de salto](https://msdn.microsoft.com/library/dn835041.aspx "ventana de salto - Azure Stream Analytics") determina hasta cuándo se remontará la consulta para encontrar el evento más reciente (en este ejemplo, trescientos segundos).

## <a name="get-help"></a>Obtener ayuda
Para obtener ayuda adicional, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

