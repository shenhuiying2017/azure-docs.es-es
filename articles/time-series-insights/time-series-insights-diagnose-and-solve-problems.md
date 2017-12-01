---
title: "Diagnóstico y solución de problemas en Azure Time Series Insights | Microsoft Docs"
description: "Este artículo describe cómo diagnosticar, solucionar problemas y resolver dificultades comunes que puede surgir en el entorno de Azure Time Series Insights."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/15/2017
ms.openlocfilehash: 757d37183ad334aca462af59bad261cfa686299e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnóstico y solución de problemas de su entorno Time Series Insights

## <a name="problem-1-no-data-is-shown"></a>Problema 1: no se muestra ningún dato
Hay varias razones por las que podría no ver sus datos en su entorno en el [explorador de Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="possible-cause-a-event-source-data-is-not-in-json-format"></a>Causa posible A: los datos de origen del evento no están en formato JSON
Azure Time Series Insights solo admite datos JSON. Para ver ejemplos de JSON, consulte [Formas de JSON admitidas](time-series-insights-send-events.md#supported-json-shapes).

### <a name="possible-cause-b-event-source-key-is-missing-a-required-permission"></a>Causa posible B: a la clave de origen del evento le falta un permiso necesario
* Para IoT Hub, debe proporcionar la clave con el permiso de **conexión de servicio**.

   ![Permiso de conexión de servicio de Iot Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas **iothubowner** o **service**, dado que ambas tienen permiso de **conexión de servicio**.
   
* Para una instancia de Event Hubs, debe proporcionar la clave con el permiso de **escucha**.

   ![Permiso de escucha de centro de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas **read** o **manage**, dado que ambas tienen permiso de **escucha**.

### <a name="possible-cause-c-the-consumer-group-provided-is-not-exclusive-to-time-series-insights"></a>Causa posible C: el grupo de consumidores proporcionado no es exclusivo de Time Series Insights
Durante el registro en un centro de IoT o un centro de eventos, se especifica el grupo de consumidores que se debería usar al leer los datos. Este grupo de consumidor **no** puede ser compartido. Si el grupo de consumidor es compartido, el centro de eventos subyacente desconectará automáticamente a uno de los lectores de forma aleatoria. Proporcione un grupo de consumidor exclusivo del que Time Series Insights pueda leer.

## <a name="problem-2-some-data-is-shown-but-some-is-missing"></a>Problema 2: se muestran algunos datos pero faltan otros
Cuando puede ver datos parcialmente, pero los datos se atrasan, existen varias posibilidades para tener en cuenta:

### <a name="possible-cause-a-your-environment-is-getting-throttled"></a>Causa posible A: puede que se esté limitando el entorno
La limitación se aplica según el tipo y la capacidad de la SKU del entorno. Todos los orígenes de eventos del entorno comparten esta capacidad. Si el origen de eventos de la instancia de IoT Hub o Event Hubs está insertando datos por encima de los límites exigidos, experimentará limitaciones y retrasos.

En el siguiente diagrama se muestra un entorno de Time Series Insights con una SKU S1 y una capacidad 3. Este entorno registra 3 millones de eventos al día.

![Capacidad actual de la SKU del entorno](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Por ejemplo, supongamos que este entorno está ingiriendo mensajes de un centro de eventos. Observe la tasa de entrada que se muestra en el diagrama siguiente:

![Velocidad de entrada de ejemplo para una instancia de Event Hubs](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Como se muestra en el diagrama, la velocidad de entrada diaria es de ~67 000 mensajes. Esta velocidad se traduce aproximadamente en 46 mensajes por minuto. Si cada mensaje de centro de eventos se acopla a un evento de Time Series Insights, este entorno no experimenta ninguna limitación. Si cada mensaje de centro de eventos se acopla a 100 eventos de Time Series Insights, se deben ingerir 4600 mensajes por minuto. Un entorno de SKU S1 que tiene una capacidad de 3 solo puede admitir la entrada de 2100 eventos por minuto (1 millón de eventos por día = 700 eventos por minuto por 3 unidades = 2100 eventos por minuto). Por lo tanto, experimentará un retraso debido a la limitación. 

Para comprender de una manera más profunda cómo funciona la lógica de aplanamiento, consulte [Formas JSON admitidas](time-series-insights-send-events.md#supported-json-shapes).

### <a name="recommended-resolution-steps-for-excessive-throttling"></a>Pasos de resolución recomendados para la limitación excesiva
Para corregir el retraso, aumente la capacidad de la SKU de su entorno. Para más información, consulte [Escalado de su entorno de Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="possible-cause-b-initial-ingestion-of-historical-data-is-causing-slow-ingress"></a>Causa posible B: la ingesta inicial de los datos históricos está causando una entrada lenta
Si va a conectar un origen de eventos existente, es probable que su centro de eventos o instancia de IoT Hub ya contengan datos. El entorno comienza a extraer datos del principio del período de retención del mensaje de origen del evento.

Este comportamiento es el comportamiento predeterminado y no se puede invalidar. Podría exponerse a limitaciones y la ingesta de los datos históricos podría llevar un rato.

#### <a name="recommended-resolution-steps-of-large-initial-ingestion"></a>Pasos de resolución recomendados para una ingesta inicial grande
Para corregir el retraso, realice estos pasos:
1. Aumente la capacidad de la SKU al valor máximo permitido (10 en este caso). Cuando se aumenta la capacidad, el proceso de entrada comienza a recuperarse mucho más rápido. Puede visualizar lo rápido que se está recuperando mediante el gráfico de disponibilidad del [explorador de Time Series Insights](https://insights.timeseries.azure.com). El aumento de capacidad se le cobra.
2. Una vez recuperado el retraso, reduzca la capacidad de la SKU de nuevo a la tasa de entrada normal.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema 3: mi valor de *nombre de propiedad de marca de tiempo* del origen de eventos no funciona
Asegúrese de que el nombre y el valor se ajustan a las reglas siguientes:
* El nombre de la propiedad timestamp _distingue mayúsculas de minúsculas_.
* El valor de la propiedad timestamp que procede del origen del evento, como una cadena JSON, debe tener el formato _aaaa-MM-ddTHH:mm:ss.FFFFFFFK_. Un ejemplo de este tipo de cadena es "2008-04-12T12:53Z".

## <a name="next-steps"></a>Pasos siguientes
- Para obtener ayuda adicional, inicie una conversación en el [Foro de MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) o en [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- También puede usar el [soporte técnico de Azure](https://azure.microsoft.com/support/options/) para opciones de soporte técnico asistido.
