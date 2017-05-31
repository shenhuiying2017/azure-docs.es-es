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
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnóstico y solución de problemas de su entorno Time Series Insights

## <a name="i-dont-see-my-data"></a>No veo mis datos
Estas son algunas de las razones por las que podría no ver sus datos en su entorno en el [portal de Azure Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>Su origen de eventos no tiene datos en formato JSON
Azure Time Series Insights solo admite actualmente datos JSON. Para ver ejemplos de JSON, consulte [Formas de JSON admitidas](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Cuando registró el origen de eventos, no proporcionó la clave que dispone del permiso necesario
* Para IoT Hub, debe proporcionar la clave con el permiso de **conexión de servicio**.

   ![Permiso de conexión de servicio de Iot Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas **iothubowner** o **service**, dado que ambas tienen permiso de **conexión de servicio**.
* Para una instancia de Event Hubs, debe proporcionar la clave con el permiso de **escucha**.

   ![Permiso de escucha de centro de eventos](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Como se muestra en la imagen anterior, servirían las directivas **read** o **manage**, dado que ambas tienen permiso de **escucha**.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>El grupo de consumidores proporcionado no es exclusivo de Time Series Insights
Para IoT Hub o Event Hubs, durante el registro le pedimos que especifique el grupo de consumidores que se debería usar al leer los datos. Este grupo de consumidores no debe compartirse. Si se comparte, el centro de eventos subyacente desconectará automáticamente uno de los lectores de forma aleatoria.

## <a name="i-see-my-data-but-theres-a-lag"></a>Veo mis datos, pero hay un retraso
Estas son las razones por las que podría ver datos parciales en su entorno en el [portal de Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>Puede que se esté limitando el entorno
La limitación se aplica según el tipo y la capacidad de la SKU del entorno. Todos los orígenes de eventos del entorno comparten esta capacidad. Si el origen de eventos de la instancia de IoT Hub o Event Hubs está insertando datos por encima de los límites exigidos, experimentará limitaciones y retrasos.

En el siguiente diagrama se muestra un entorno de Time Series Insights con una SKU S1 y una capacidad 3. Este entorno registra 3 millones de eventos al día.

![Capacidad actual de la SKU del entorno](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Supongamos que este entorno ingiere mensajes de un centro de eventos con la tasa de entrada que se muestra en el siguiente diagrama:

![Velocidad de entrada de ejemplo para una instancia de Event Hubs](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Como se muestra en el diagrama, la velocidad de entrada diaria es de ~67 000 mensajes. Esta velocidad se traduce aproximadamente en 46 mensajes por minuto. Si cada mensaje de centro de eventos se acopla a un evento de Time Series Insights, este entorno no experimenta ninguna limitación. Si cada mensaje de centro de eventos se acopla a 100 eventos de Time Series Insights, se deben ingerir 4600 mensajes por minuto. Un entorno de SKU S1 que tiene una capacidad de 3 solo puede admitir la entrada de 2100 eventos por minuto (1 millón de eventos por día = 700 eventos por minuto por 3 unidades = 2100 eventos por minuto). Por lo tanto, experimentará un retraso debido a la limitación. 

Para comprender de una manera más profunda cómo funciona la lógica de aplanamiento, consulte [Formas JSON admitidas](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Pasos recomendados
Para corregir el retraso, aumente la capacidad de la SKU de su entorno. Para más información, consulte [Escalado de su entorno de Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Está insertando datos históricos lo cual provoca una entrada lenta
Si va a conectar un origen de eventos existente, es probable que su centro de eventos o instancia de IoT Hub ya contengan datos. De modo que el entorno comienza a extraer datos del principio del período de retención del mensaje de origen del evento. 

Este comportamiento es el comportamiento predeterminado y no se puede invalidar. Podría exponerse a limitaciones y la ingesta de los datos históricos podría llevar un rato.

#### <a name="recommended-steps"></a>Pasos recomendados
Para corregir el retraso, realice estos pasos:
1. Aumente la capacidad de la SKU al valor máximo permitido (10 en este caso). Cuando se aumenta la capacidad, el proceso de entrada comienza a recuperarse mucho más rápido. Puede visualizar lo rápido que se está recuperando mediante el gráfico de disponibilidad del [portal de Time Series Insights](https://insights.timeseries.azure.com). El aumento de capacidad se le cobra.
2. Una vez recuperado el retraso, reduzca la capacidad de la SKU de nuevo a la tasa de entrada normal.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Mi configuración de *nombre de propiedad timestamp* del origen de eventos no funciona
Asegúrese de que el nombre y el valor se ajustan a las reglas siguientes:
* El nombre de la propiedad timestamp _distingue mayúsculas de minúsculas_.
* El valor de la propiedad timestamp que procede del origen del evento, como una cadena JSON, debe tener el formato _aaaa-MM-ddTHH:mm:ss.FFFFFFFK_. Un ejemplo de este tipo de cadena es "2008-04-12T12:53Z".

