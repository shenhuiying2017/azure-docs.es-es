---
title: Escalado del entorno de Azure Time Series Insights | Microsoft Docs
description: "En este artículo se describe cómo escalar el entorno de Azure Time Series Insights. Use Azure Portal para agregar o restar capacidad en una SKU de precios."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: edcd9561778998c4df09cc5014f8b8ba81c0e369
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Escalado de su entorno de Time Series Insights

En este artículo se describe cómo cambiar la capacidad de su entorno en el entorno de Azure Time Series Insights a través de Azure Portal. La capacidad es el multiplicador que se aplica a la tasa de entrada, la capacidad de almacenamiento y el costo asociado a la SKU seleccionada. 

Puede usar Azure Portal para aumentar o disminuir la capacidad en una SKU de precios determinada. 

Sin embargo, el plan de tarifa de la SKU no se puede cambiar. Por ejemplo, un entorno con una SKU de precios de S1 no se puede convertir en una de S2 o viceversa. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>Capacidades y tasas de entrada de SKU de S1

| Capacidad de SKU de S1 | Velocidad de entrada | Capacidad máxima de almacenamiento
| --- | --- | --- |
| 1 | 1 GB (1 millones de eventos) | 30 GB (30 millones de eventos) al mes |
| 10 | 10 GB (10 millones de eventos) | 300 GB (300 millones de eventos) al mes |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Capacidades y tasas de entrada de SKU de S2

| Capacidad de SKU de S2 | Velocidad de entrada | Capacidad máxima de almacenamiento
| --- | --- | --- |
| 1 | 10 GB (10 millones de eventos) | 300 GB (300 millones de eventos) al mes |
| 10 | 100 GB (100 millones de eventos) | 3 TB (3 mil millones de eventos) al mes |

Las capacidades se escalan linealmente, por lo que una SKU de S1 con capacidad 2 admite una velocidad de entrada de 2 GB (2 millones) de eventos al día y 60 GB (60 millones de eventos) al mes.

## <a name="change-the-capacity-of-your-environment"></a>Cambio de la capacidad del entorno
1. En Azure Portal, localice y seleccione el entorno de Time Series Insights. 

2. En el menú de su entorno de Time Series Insights, seleccione **Configurar**.

   ![configure.png](media/scale-your-environment/configure.png)

3. Utilice el control deslizante **Capacidad** para seleccionar la que cumpla los requisitos para la velocidad de entrada y la capacidad de almacenamiento. Tenga en cuenta la **velocidad de entrada**, **la capacidad de almacenamiento** y las actualizaciones de la **estimación de costos** dinámicamente para mostrar el efecto del cambio. 

   ![Control deslizante](media/scale-your-environment/slider.png)

   También puede escribir número del multiplicador de capacidad en el cuadro de texto de la derecha del control deslizante. 

4. Seleccione **Guardar** para escalar el entorno. El indicador de progreso se muestra hasta que el cambio se confirma, un tiempo breve. 

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Compruebe que la nueva capacidad sea suficiente para evitar la limitación](time-series-insights-diagnose-and-solve-problems.md).
