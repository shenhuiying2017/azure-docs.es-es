---
title: Planeamiento de la escala del entorno Azure Time Series Insights | Microsoft Docs
description: En este artículo se describe cómo seguir los procedimientos recomendados cuando se planea un entorno de Azure Time Series Insights, incluida la capacidad de almacenamiento, la retención de datos, la capacidad de entrada y la supervisión.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 991db58db1bb07f338c0f80aa4db69ddb868dcab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planee el entorno de Azure Time Series Insights

En este artículo se describe cómo planear el entorno de Azure Time Series Insights según la velocidad de entrada esperada y los requisitos de retención de datos.

## <a name="best-practices"></a>Procedimientos recomendados

Para empezar a trabajar con Time Series Insights, se recomienda saber cuántos datos espera insertar por minuto, así como también durante cuánto tiempo debe almacenar los datos.  

Para más información sobre la capacidad y la retención de ambas SKU de Time Series Insights, consulte los [precios de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Considere estos atributos para planear mejor el entorno para el éxito a largo plazo: 
- Capacidad de almacenamiento
- Período de retención de datos
- Capacidad de entrada 
- Eventos con forma
- Confirmación de la presencia de datos de referencia

## <a name="understand-storage-capacity"></a>Descripción de la capacidad de almacenamiento
De manera predeterminada, Time Series Insights conserva los datos en función de la cantidad de almacenamiento que aprovisionó (unidades por la cantidad de almacenamiento por unidad) y la entrada.

## <a name="understand-data-retention"></a>Descripción de la retención de los datos
Puede establecer la configuración del **tiempo de retención de datos** del entorno de Time Series Insights, que habilita hasta 400 días de retención.  Time Series Insights tiene dos modos, uno que optimiza el entorno para garantizar que tenga los datos más actualizados (opción que está activada de manera predeterminada) y otro que optimiza para garantizar que se cumplan los límites de retención, donde la entrada se pone en pausa si se alcanza la capacidad de almacenamiento general del entorno.  Puede ajustar la retención y alternar entre ambos modos en la página de configuración del entorno en Azure Portal.

Puede configurar un máximo de 400 días de retención de datos en el entorno de Time Series Insights.

## <a name="configure-data-retention"></a>Configuración de la retención de datos

1. En [Azure Portal](https://portal.azure.com), seleccione el entorno de Time Series Insights.

2. En la **página del entorno de Time Series Insights**, en el encabezado **Configuración**, seleccione **Configurar**. 

3. En el cuadro **Tiempo de retención de datos (en días)**, escriba un valor de 1 a 400.

   ![Configuración de la retención](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Descripción de la capacidad de entrada

La otra área de interés para el planeamiento es la capacidad de entrada, que deriva de la asignación por minuto. 

Desde la perspectiva de la limitación, un paquete de datos de entrada con un tamaño de 32 KB se trata como 32 eventos de 1 KB cada uno. El tamaño de evento máximo permitido es 32 KB. Los paquetes de datos con más de 32 KB se truncan.

En la tabla siguiente se resume la capacidad de ingreso de cada SKU:

|SKU  |Recuento de eventos por mes, por unidad  |Tamaño de eventos por mes, por unidad  |Recuento de eventos por minuto, por unidad  | Tamaño por minuto, por unidad   |
|---------|---------|---------|---------|---------|
|S1     |   30 millones     |  30 GB     |  700    |  700 KB   |
|S2     |   300 millones    |   < 300 GB   | 7000   | 7000 KB  |

Puede aumentar la capacidad de una SKU S1 o S2 a 10 unidades en un solo entorno. No puede migrar de un entorno S1 a uno S2 o desde un entorno S2 a uno S1. 

Para la capacidad de entrada, primero debe determinar la entrada total que requiere de manera mensual. A continuación, determine cuáles son las necesidades por minuto, puesto que es aquí donde la limitación y la latencia desempeñan un papel.

Si tiene un incremento en la entrada de datos que dura menos de 24 horas, Time Series Insights puede "ponerse al día" con una velocidad de entrada que doble las velocidades ya mencionadas. 

Por ejemplo, si tiene una sola SKU S1 y datos de entrada a una velocidad de 700 eventos por minuto, y hay un incremento durante menos de una hora a una velocidad de 1400 eventos o menos, no habría una latencia apreciable en el entorno. Sin embargo, si supera los 1400 eventos por minuto durante más de una hora, es probable que experimente una latencia respecto de los datos que se visualizan y están disponibles para consulta en el entorno. 

Es posible que no sepa de antemano cuántos datos espera insertar. En este caso, puede encontrar la telemetría de datos para [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) y [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) en Azure Portal. Esta telemetría puede ayudar a determinar cómo aprovisionar el entorno. Use la página **Métricas** del origen del evento correspondiente en Azure Portal para ver su telemetría. Si comprende las métricas de origen del evento, puede planea de manera más eficaz y aprovisionar el entorno de Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Cálculo de los requisitos de entrada

- Confirme que la capacidad de entrada está por encima de la velocidad promedio por minuto y que el entorno es suficientemente grande para controlar la entrada anticipada equivalente al doble de la capacidad durante menos de una hora.

- Si los incrementos de entrada se producen durante más de una hora, use la velocidad del incremento como promedio y aprovisione un entorno con la capacidad de controlar esta velocidad.
 
### <a name="mitigate-throttling-and-latency"></a>Mitigación de la limitación y la latencia

Para información sobre cómo prevenir la limitación y la latencia, consulte el artículo sobre la [mitigación de la limitación y la latencia](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Eventos con forma
Es importante asegurarse de que el método de envío de eventos a TSI es compatible con el tamaño del entorno que se aprovisiona (de otra manera, puede asignar el tamaño del entorno a la cantidad de eventos que TSI lea y al tamaño de cada evento).  Del mismo modo, es importante pensar en los atributos que desea segmentar y usar de filtro al consultar los datos.  Teniendo esto en cuenta, se recomienda revisar la sección sobre formas de JSON en nuestra [documentación] sobre el *envío de eventos* (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-send-events).  Se encuentra en la parte inferior de la página.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Confirmación de la presencia de datos de referencia
Un conjunto de datos de referencia es una colección de elementos que aumentan los eventos de un origen de eventos. El motor de entrada de Time Series Insights combina cada evento del origen de eventos con la fila de datos correspondiente en el conjunto de datos de referencia. A partir de ese momento, este evento aumentado está disponible para consultas. Esta combinación se basa en las columnas de clave principal definidas en el conjunto de datos de referencia.

Tenga en cuenta que los datos de referencia no se combinan de manera retroactiva. Esto significa que solo los datos de entrada actuales y futuros se combinan y unen con el conjunto de datos de referencia una vez que se configuran y cargan.  Si va a enviar una gran cantidad de datos históricos a TSI y no cargar ni crea primero datos de referencia en TSI, deberá volver a realizar el trabajo (sugerencia, esto no es agradable).  

Para más información sobre cómo crear, cargar y administrar los datos de referencia en TSI, diríjase a nuestra [documentación] sobre los *datos de referencia* (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).


## <a name="next-steps"></a>Pasos siguientes
- [Adición de un origen de eventos de Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Adición de un origen de eventos de IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
