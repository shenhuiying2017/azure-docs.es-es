---
title: "Supervisión y reducción de la limitación en Azure Time Series Insights | Microsoft Docs"
description: "En este artículo se describe cómo supervisar, diagnosticar y mitigar los problemas de rendimiento que causan la latencia y la limitación en Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: ec16f20723e4a613c953363da6cf6b463de829a9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Supervisión y reducción de la limitación para reducir la latencia en Azure Time Series Insights
Si la cantidad de datos de entrada supera la configuración del entorno, puede experimentar latencia o limitación en Azure Time Series Insights.

Para evitarlo, configure correctamente el entorno para la cantidad de datos que desee analizar.

Es más probable que experimente latencia y limitación si:

- Agrega un origen del evento que contiene datos antiguos que pueden superar la tasa de entrada asignada (ya que Time Series Insights tendrá que leer todos los datos).
- Agrega más orígenes de eventos a un entorno, lo que produce un pico de eventos adicionales (que podría superar la capacidad del entorno).
- Inserta grandes cantidades de eventos históricos en un origen del evento, lo cual resulta en un retraso (ya que Time Series Insights tendrá que leer todos los datos).
- Une los datos de referencia con la telemetría, lo que da lugar a un evento de mayor tamaño.  Desde la perspectiva de la limitación, un paquete de datos de entrada con un tamaño de 32 KB se trata como 32 eventos de 1 KB cada uno. El tamaño de evento máximo permitido es 32 KB. Los paquetes de datos con más de 32 KB se truncan.


## <a name="monitor-latency-and-throttling-with-alerts"></a>Supervisión de la latencia y la limitación con alertas

Las alertas pueden ayudarle a diagnosticar y mitigar los problemas de latencia causados por su entorno. 

1. En Azure Portal, haga clic en **Métricas**. 

   ![Métricas](media/environment-mitigate-latency/add-metrics.png)

2. Haga clic en **Agregar alerta de métrica**.  

    ![Adición de alerta de métrica](media/environment-mitigate-latency/add-metric-alert.png)

Desde allí, puede configurar alertas mediante las siguientes métricas:

|Métrica  |Descripción  |
|---------|---------|
|**Bytes de entrada recibidos**     | Número de bytes sin procesar leídos desde los orígenes de eventos. El número sin procesar normalmente incluye el nombre y el valor de la propiedad.  |  
|**Mensajes no válidos de entrada recibidos**     | Número de mensajes no válidos leídos desde todos los orígenes de eventos de Azure Event Hubs o Azure IoT Hub.      |
|**Mensajes de entrada recibidos**   | Número de mensajes leídos desde todos los orígenes de eventos de Event Hubs o IoT Hub.        |
|**Bytes de entrada almacenados**     | Tamaño total de los eventos almacenados y disponibles para su consulta. Se calcula el tamaño solo sobre el valor de propiedad.        |
|**Eventos de entrada almacenados**     |   Número de eventos planos almacenados y disponibles para su consulta.      |

![Latency](media/environment-mitigate-latency/latency.png)

Una de estas técnicas consiste en establecer una alerta de **eventos de entrada almacenados** >= un umbral ligeramente por debajo de la capacidad total del entorno durante un período de 2 horas.  Esta alerta le ayudará a conocer si se encuentra continuamente en el límite de la capacidad, lo cual indica una alta probabilidad de latencia.  

Por ejemplo, si tiene tres unidades de nivel S1 aprovisionadas (o una capacidad de entrada de 2100 eventos por minuto), puede establecer una alerta de **eventos de entrada almacenados** >= 1900 eventos durante 2 horas. Si continuamente supera este umbral y, como consecuencia, se desencadena la alerta, es posible que necesite un mayor aprovisionamiento.  

Además, si sospecha que está experimentando alguna limitación, puede comparar los **mensajes de entrada recibidos** con los mensajes de origen del evento de salida.  Si la entrada en el centro de eventos es mayor que los **mensajes de entrada recibidos**, es probable que se esté limitando su instancia de Time Series Insights.

## <a name="improving-performance"></a>Mejora del rendimiento 
Para reducir la limitación o la posibilidad de experimentar latencia, la mejor forma de solucionarlo es aumentar la capacidad del entorno. 

Para evitarlo, configure correctamente el entorno para la cantidad de datos que desee analizar. Para más información acerca de cómo agregar capacidad al entorno, consulte [Escalado del entorno](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Pasos siguientes
- Para conocer pasos de solución de problemas adicionales, consulte [Diagnóstico y solución de problemas de su entorno Time Series Insights](time-series-insights-diagnose-and-solve-problems.md).
- Para obtener ayuda adicional, inicie una conversación en el [Foro de MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) o en [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). También puede ponerse en contacto con el [soporte técnico de Azure](https://azure.microsoft.com/support/options/) para opciones de soporte técnico asistido.
