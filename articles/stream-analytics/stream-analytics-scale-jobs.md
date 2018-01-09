---
title: Escalar los trabajos de Stream Analytics para incrementar el rendimiento | Microsoft Docs
description: "Aprenda a escalar los trabajos de Stream Analytics mediante la configuración de particiones de entrada, la optimización de la definición de consulta y el ajuste de las unidades de streaming premium del trabajo."
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
ms.openlocfilehash: 781a3b71c35cb48e40202e3b1acc8edbbaf865c4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Escalar los trabajos de Azure Stream Analytics para incrementar el rendimiento
En este artículo se muestra cómo ajustar una consulta de Stream Analytics para aumentar la capacidad de procesamiento de trabajos de Stream Analytics. Puede usar la guía siguiente para escalar un trabajo para administrar una carga más elevada y aprovecha más recursos del sistema, como más ancho de banda, más recursos de CPU y más memoria.
Como requisito previo, es posible que tenga que leer los artículos siguientes:
-   [Descripción y ajuste de las unidades de streaming premium](stream-analytics-streaming-unit-consumption.md)
-   [Creación de trabajos que se pueden paralelizar](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Caso 1: la consulta se puede paralelizar completamente de manera inherente en distintas particiones de entrada
Si la consulta se puede paralelizar completamente de manera inherente en distintas particiones de entrada, puede seguir estos pasos:
1.  Cree una consulta que sea embarazosamente paralela mediante la palabra clave **PARTITION BY**. Consulte más detalles en la sección de trabajos embarazosamente paralelos [en esta página](stream-analytics-parallelization.md).
2.  En función de los tipos de salida que se usan en la consulta, es posible que algunas salidas no se puedan paralelizar o que necesiten una configuración adicional para ser embarazosamente paralelas. Por ejemplo, las salidas de SQL, SQL DW y PowerBI no se pueden paralelizar. Las salidas siempre se combinan antes de enviarlas al receptor de salida. Blobs, Tables, ADLS, Service Bus y Azure Function se pueden paralelizar de manera automática. CosmosDB y Event Hub deben tener la configuración PartitionKey establecida para coincidir con el campo **PARTITION BY** (habitualmente PartitionId). Para Event Hub, también debe poner atención en que el número de particiones de todas las entradas coincida con el de todas las salidas para evitar el intercambio entre las particiones. 
3.  Ejecute la consulta con **6 SU** (que es la capacidad total de un solo nodo de ejecución) para medir el rendimiento máximo posible y si usa **GROUP BY**, mida cuántos grupos (cardinalidad) puede controlar el trabajo. Los síntomas generales de que el trabajo alcanza los límites de los recursos del sistema son los siguientes.
    - La métrica del porcentaje de uso de SU supera el 80 %. Esto indica que el uso de la memoria es alto. [Aquí](stream-analytics-streaming-unit-consumption.md) se describen los factores que contribuyen al incremento de esta métrica. 
    -   La marca de tiempo de salida se queda atrás con respecto al tiempo de reloj. En función de la lógica de la consulta, la marca de tiempo de salida puede tener un desplazamiento lógico del tiempo de reloj. Sin embargo, debería avanzar aproximadamente a la misma velocidad. Si la marca de tiempo de salida se queda cada vez más atrás, es un indicador de que el sistema está trabajando demasiado. Esto puede ser resultado de una limitación de receptor de salida de bajada o de un alto uso de CPU. En este momento, no proporcionamos una métrica de uso de CPU, por lo que puede resultar difícil diferenciar ambas opciones.
        - Si el problema se debe a la limitación de receptor, es posible que tenga que aumentar el número de particiones de salida (y también de las particiones de entrada para que el trabajo siga siendo posible de paralelizar completamente) o aumentar la cantidad de recursos del receptor (por ejemplo, el número de Unidades de solicitud para CosmosDB).
    - En el diagrama de trabajo, hay una métrica de evento de trabajo pendiente por partición para cada entrada. Si la métrica de evento de trabajo pendiente sigue aumentando, también es un indicador de que el recurso del sistema está restringido (ya sea debido a una limitación de receptor de salid o un alto uso de CPU).
4.  Una vez que determine los límites del alcance de un trabajo de 6 SU, puede extrapolar de manera lineal la capacidad de procesamiento del trabajo a medida que agrega más SU, suponiendo que no tiene ninguna asimetría de datos que haga que cierta partición sea "activa".
>[!Note]
> Elija el número correcto de unidades de streaming premium: como Stream Analytics crea un nodo de procesamiento para cada 6 SU que se agregan, es mejor hacer que el número de nodos sea un divisor del número de particiones de entrada, para que las particiones se puedan distribuir de manera uniforme entre los nodos.
> Por ejemplo, midió que el trabajo de 6 SU puede alcanzar una velocidad de procesamiento de 4 MB/s y la cantidad de particiones de entrada es 4. Puede elegir ejecutar el trabajo con 12 SU para alcanzar una velocidad de procesamiento de 8 MB/s aproximadamente, o bien 24 SU para alcanzar 16 MB/s. Luego puede decidir cuándo aumentar el número de SU para el trabajo y a qué valor, como función de la tasa de entrada.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Caso 2: si la consulta no es embarazosamente paralela.
Si la consulta no es embarazosamente paralela, puede seguir estos pasos.
1.  Comience primero con una consulta sin **PARTITION BY** para evitar la complejidad de la creación de particiones y ejecute la consulta con 6 SU para medir la carga máxima, tal como en el [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Si puede lograr la carga prevista en términos de rendimiento, está listo. Como alternativa, puede elegir medir el mismo trabajo ejecutándose a 3 SU y 1 SU, para saber cuál es el número mínimo de SU que funciona en este escenario.
3.  Si no puede alcanzar el rendimiento deseado, intente dividir la consulta en varios pasos si es posible, si ya no tiene varios pasos, y asigne hasta 6 SU para cada paso en la consulta. Por ejemplo, si tiene tres pasos, asigne 18 SU en la opción "Escalar".
4.  Cuando se ejecuta un trabajo de este tipo, Stream Analytics coloca cada paso en su propio nodo con recursos de 6 SU dedicados. 
5.  Si todavía no alcanzó su carga de destino, puede intentar usar **PARTITION BY** partiendo por los pasos más cercanos a la entrada. En el caso del operador **GROUP BY** que probablemente no sea particionable de manera natural, puede usar el patrón de agregado local o global para realizar una operación **GROUP BY** particionada seguido de una operación **GROUP BY** no particionada. Por ejemplo, si desea contar cuántos automóviles pasan por cada cabina de peaje cada tres minutos y el volumen de los datos va más allá de lo que se puede administrar con 6 SU.

Consulta:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

En la consulta anterior, cuenta los automóviles por cabina de peaje por partición y luego agrega el recuento de todas las particiones.

Una vez que se crean las particiones, para cada partición del paso, asigne hasta 6 SU, cada partición con 6 SU como máximo, para que así cada partición se pueda colocar en su propio nodo de procesamiento.

> [!Note]
> Si la consulta no se puede particionar, agregar SU adicionales en una consulta de varios pasos no siempre puede mejorar el rendimiento. Una manera de ganar rendimiento es reducir el volumen en los pasos iniciales mediante un patrón de agregado local o global, tal como ya se describió en el paso 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Caso 3: ejecuta muchas consultas independientes en un trabajo.
Para ciertos casos de uso de ISV, donde resulta más rentable procesar los datos de varios inquilinos en solo un trabajo, usando entradas y salidas separadas para cada inquilino, es posible que termine ejecutando algunas consultas independientes (por ejemplo, 20) en un solo trabajo. El supuesto es que la carga de cada subconsulta es relativamente pequeña. En este caso, puede seguir estos pasos.
1.  En este caso, no use **PARTITION BY** en la consulta
2.  Reduzca el número de particiones de entrada al menor valor posible de 2 si usa Event Hub.
3.  Ejecute la consulta con 6 SU. Con la carga prevista para cada subconsulta, agregue tantas subconsultas como sea posible, hasta que el trabajo esté alcanzando los límites de recursos del sistema. Consulte el [caso 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) para conocer los síntomas que indican que esto ocurre.
4.  Una vez que alcanza el límite de subconsulta que se midió anteriormente, comience a agregar la subconsulta a un trabajo nuevo. El número de trabajos que se deben ejecutar como función del número de consultas independientes debe ser bastante lineal, suponiendo que no tiene ninguna distorsión de carga. Entonces puede prever cuántos trabajos de 6 SU necesita para ejecutar como función del número de inquilinos que quisiera atender.
5.  Cuando use la instrucción JOIN de los datos de referencia con estas consultas, debe unir las entradas antes de combinarlas con los mismos datos de referencia y, luego, dividir los eventos si es necesario. De lo contrario, cada instrucción JOIN de los datos de referencia conserva una copia de los datos de referencia en memoria, lo que probablemente aumente el uso de memoria de manera innecesaria.

> [!Note] 
> ¿Cuántos inquilinos se deben colocar en cada trabajo?
> Este patrón de consulta a menudo tiene una gran cantidad de subconsultas y resulta una topología muy grande y compleja. Es posible que el controlador del trabajo no pueda controlar una topología de ese tamaño. Como regla general, manténgase bajo los 40 inquilinos por trabajo de 1 SU y 60 inquilinos en el caso de trabajos de 3 SU y 6 SU. Cuando se excede la capacidad del controlador, el trabajo no se iniciará correctamente.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Un ejemplo de rendimiento de Stream Analytics a gran escala
Para ayudarle a comprender cómo se escalan los trabajos de Stream Analytics, se ha realizado un experimento basado en un dispositivo Raspberry Pi. Este experimento permite ver los efectos sobre la capacidad de procesamiento de varias unidades de streaming premium y particiones.

En este escenario, el dispositivo envía datos de sensores (clientes) a un centro de eventos. Stream Analytics procesa los datos y envía una alerta o estadísticas como salida a otro centro de procesamiento. 

El cliente envía los datos del sensor en formato JSON. La salida de datos también está en formato JSON. Los datos tienen este aspecto:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

La siguiente consulta se utiliza para enviar una alerta cuando se apaga una luz:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Medición de la capacidad de procesamiento

En este contexto, la capacidad de procesamiento es la cantidad de datos de entrada que procesa Stream Analytics en una cantidad fija de tiempo. (Se mide durante 10 minutos). Para conseguir la mejor capacidad de procesamiento de los datos de entrada, tanto la entrada de flujo de datos como la consulta se han particionado. También se incluye **COUNT()** en la consulta para medir el número de eventos de entrada procesados. Para asegurarse de que el trabajo no está esperando simplemente a que lleguen los eventos de entrada, cada partición del centro de eventos de entrada se ha cargado previamente con aproximadamente 300 MB.

La siguiente tabla muestra los resultados se observan al aumentar el número de unidades de streaming premium y los correspondientes recuentos de particiones en Event Hubs.  

<table border="1">
<tr><th>Particiones de entrada</th><th>Particiones de salida</th><th>Unidades de streaming premium</th><th>Capacidad de procesamiento sostenida
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

