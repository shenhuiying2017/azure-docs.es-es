---
title: "Diagnóstico y solución de problemas | Microsoft Docs"
description: "En este tutorial se describe cómo diagnosticar y resolver problemas de su entorno Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>Diagnosticar y solucionar problemas

## <a name="i-do-not-see-my-data"></a>No veo mis datos
Estas son algunas de las razones por las que podría no ver sus datos en su entorno en [Time Series Insights Portal](https://insights.timeseries.azure.com).

### <a name="does-your-event-source-have-data-in-json-format"></a>¿Su origen de eventos tiene datos en formato JSON?
Azure Time Series Insights solo admite actualmente datos JSON. Para ver ejemplos de JSON, consulte la sección *Formas de JSON admitidas* [aquí](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>Al registrar el origen del evento, ¿proporcionó la clave con los permisos necesarios?
1. Para IoTHub, debe proporcionar la clave con el permiso de *conexión de servicio*.

   ![Permiso de conexión de servicio de IotHub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas "iothubowner" o "service", dado que ambas tienen permiso de conexión de servicio.
2. Para EventHub, debe proporcionar la clave con el permiso de *escucha*.

   ![Permiso de escucha de centro de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas "read" o "manage", dado que ambas tienen permiso de lectura.

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>¿Está seguro de que el grupo de consumidores proporcionado es exclusivo de Time Series Insights?
Para IoTHub o EventHub, durante el registro le pedimos que especifique el grupo de consumidores que se deberían usar al leer los datos. Este grupo de consumidores no debe compartirse. Si se comparte, el centro de eventos subyacente desconecta automáticamente uno de los lectores de forma aleatoria.

## <a name="i-see-my-data-but-there-is-a-lag"></a>Veo mis datos, pero hay un retraso
Estas son algunas de las razones por las que podría ver datos parciales en su entorno en [Time Series Insights Portal](https://insights.timeseries.azure.com).

### <a name="your-environment-might-be-getting-throttled"></a>Puede que se esté limitando el entorno
La limitación se aplica según la clase y la capacidad de la SKU del entorno. Todos los orígenes de eventos dentro del entorno comparten esta capacidad. Si el origen del centro de eventos o del centro de IoT está insertando datos por encima de los límites exigidos, experimentará limitaciones y retrasos.

En el siguiente diagrama se muestra un entorno de Time Series Insights con una SKU S1 y una capacidad 3. Este entorno registra 3 millones de eventos al día.

![Capacidad actual de la SKU del entorno](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Supongamos que este entorno consumía mensajes de un centro de eventos con la tasa de entrada que se muestra en el siguiente diagrama:

![Capacidad actual de la SKU del entorno](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Como se muestra en el diagrama, la velocidad de entrada diaria es de ~67 000 mensajes. Esta velocidad se traduce aproximadamente en 46 mensajes por minuto. Si cada mensaje de centro de eventos se acopla a un evento de Time Series Insights, este entorno no experimenta ninguna limitación. Si cada mensaje de centro de eventos se acopla a 100 eventos de Time Series Insights, se deben ingerir 4600 mensajes por minuto. Un entorno de SKU S1 con una capacidad de tres solo puede registrar 2100 eventos por minuto. (1 millón de eventos al día = > 700 eventos por minuto, 3 unidades = > 2100 eventos por minuto). Por lo tanto, experimentará un retraso debido a la limitación. Para comprender de una manera más profunda cómo funciona la lógica de acoplamiento, consulte la sección *Formas JSON admitidas* [aquí](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Pasos recomendados
Para corregir el retraso, aumenta la capacidad de la SKU de su entorno. [How to scale your Time Series Insights environment](time-series-insights-how-to-scale-your-environment.md) (escalado de su entorno de Time Series Insights)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>Puede que esté insertando datos históricos, de ahí la entrada lenta.
Si va a conectar un origen de eventos existente, es probable que su centro de eventos o centro de IoT ya contengan datos. De modo que el entorno comienza a extraer datos del principio del período de retención del mensaje de origen del evento. Este comportamiento es el comportamiento predeterminado y no se puede invalidar. Podría exponerse a limitaciones y actualizar la ingesta de los datos históricos podría llevar un rato.

#### <a name="recommended-steps"></a>Pasos recomendados
Para corregir el retraso, siga estos pasos:
1. Aumentar la capacidad de la SKU al valor máximo permitido (10 en este caso). Cuando se aumenta la capacidad, el proceso de entrada comienza a actualizarse mucho más rápido. Puede visualizar lo rápido que nos estamos actualizando mediante el gráfico de disponibilidad de [Time Series Insights Portal](https://insights.timeseries.azure.com). El aumento de capacidad se le cobra.
2. Cuando se actualice el retraso, reduzca la capacidad de la SKU de nuevo a la tasa de entrada normal.

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>Mi configuración de *nombre de propiedad timestamp* no funciona
Asegúrese de que el nombre y el valor se ajusta a las reglas siguientes:
1. El nombre de la propiedad timestamp __distingue mayúsculas de minúsculas__.
2. El valor de la propiedad timestamp que procede del origen del evento, como una cadena JSON, debe tener el formato __aaaa-MM-ddTHH:mm:ss.FFFFFFFK__. Un ejemplo de este tipo de cadena es "2008-04-12T12:53Z".
