---
title: Planeamiento de la escala del entorno Azure Time Series Insights | Microsoft Docs
description: "En este artículo se describe cómo seguir los procedimientos recomendados cuando se planea un entorno de Azure Time Series Insights, incluida la capacidad de almacenamiento, la retención de datos, la capacidad de entrada y la supervisión."
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
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planee el entorno de Azure Time Series Insights

En este artículo se describe cómo planear el entorno de Azure Time Series Insights según la velocidad de entrada esperada y los requisitos de retención de datos.

## <a name="best-practices"></a>Prácticas recomendadas

Para empezar a trabajar con Time Series Insights, se recomienda saber cuántos datos espera insertar por minuto, así como también durante cuánto tiempo debe almacenar los datos.  

Para más información sobre la capacidad y la retención de ambas SKU de Time Series Insights, consulte los [precios de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Considere estos atributos para planear mejor el entorno para el éxito a largo plazo: 
- Capacidad de almacenamiento
- Período de retención de datos
- Capacidad de entrada 

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

## <a name="calculate-ingress-requirements"></a>Cálculo de los requisitos de entrada

- Confirme que la capacidad de entrada está por encima de la velocidad promedio por minuto y que el entorno es suficientemente grande para controlar la entrada anticipada equivalente al doble de la capacidad durante menos de una hora.

- Si los incrementos de entrada se producen durante más de una hora, use la velocidad del incremento como promedio y aprovisione un entorno con la capacidad de controlar esta velocidad.
 
## <a name="mitigate-throttling-and-latency"></a>Mitigación de la limitación y la latencia

Para información sobre cómo prevenir la limitación y la latencia, consulte el artículo sobre la [mitigación de la limitación y la latencia](time-series-insights-environment-mitigate-latency.md). 

## <a name="next-steps"></a>Pasos siguientes
- [Adición de un origen de eventos de Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Adición de un origen de eventos de IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
