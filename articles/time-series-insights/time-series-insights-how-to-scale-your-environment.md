---
title: Escalado del entorno de Azure Time Series Insights | Microsoft Docs
description: "Este tutorial describe cómo escalar el entorno de Azure Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Escalado de su entorno de Time Series Insights

Este tutorial describe cómo escalar el entorno de Time Series Insights.

> [!NOTE]
> No se permite el escalado vertical entre los tipos de SKU. Un entorno con una SKU S1 no se puede convertir en un entorno de S2.

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

Las capacidades se escalan linealmente, por lo que una SKU de S1 con capacidad 2 admite una tasa de entrada de 2 GB (2 millones) de eventos al día y 60 GB (60 millones de eventos) al mes.

## <a name="changing-the-capacity-of-your-environment"></a>Cambio de la capacidad del entorno

1. En Azure Portal, seleccione el entorno cuya capacidad desee cambiar.
1. En Configuración, haga clic en Configurar.
1. Utilice el control deslizante de la capacidad para seleccionar la capacidad que cumple los requisitos para las tarifas de entrada y la capacidad de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

* Compruebe que la nueva capacidad sea suficiente para evitar la limitación. Para más información, vea la sección *Puede que se esté limitando el entorno* [aquí](time-series-insights-diagnose-and-solve-problems.md).
