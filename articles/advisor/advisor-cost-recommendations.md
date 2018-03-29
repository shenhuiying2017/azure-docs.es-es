---
title: Recomendaciones sobre el costo de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para optimizar el costo de las implementaciones de Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7a8807a580f1a7f1fe67e026a8fbd4cc0e96c41c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="advisor-cost-recommendations"></a>Recomendaciones sobre el costo de Advisor

Advisor lo ayuda a optimizar y reducir el gasto global de Azure mediante la identificación de recursos inactivos e infrautilizados. Puede obtener recomendaciones sobre el costo en la pestaña **Cost** (Costo) del panel de Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimización del gasto en máquinas virtuales mediante la adecuación del tamaño o el apagado en instancias infrautilizadas 
Mientras que determinados escenarios de aplicaciones pueden dar lugar a un uso escaso debido al diseño, a menudo puede ahorrar dinero administrando el tamaño y número de máquinas virtuales. Advisor supervisa la utilización de las máquinas virtuales durante 14 días e identifica aquellas con una utilización escasa. Se considera que una máquina virtual tiene una utilización escasa si su utilización de la CPU es del 5 % o menos y el de la red es de 7 MB o menos durante cuatro o más días.

Advisor muestra el costo estimado de continuar ejecutando la máquina virtual, para que puede elegir entre apagarla o cambiar su tamaño.

Si desea que sea más exigente en la identificación de las máquinas virtuales infrautilizadas, puede ajustar la regla de uso promedio de la CPU según la suscripción.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Reducción de los costos mediante la eliminación de circuitos ExpressRoute no aprovisionados
Advisor identifica los circuitos ExpressRoute que han permanecido en el estado de proveedor de *No aprovisionado* durante más de un mes y recomienda eliminar el circuito si no piensa realizar el aprovisionamiento del circuito con el proveedor de conectividad.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Obtención de acceso a las recomendaciones sobre el costo en Azure Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después abra [Azure Advisor](https://aka.ms/azureadvisordashboard).

2.  En el panel de Advisor, haga clic en la pestaña **Costo**.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre las recomendaciones de Advisor, consulte:
* [Introducción a Advisor](advisor-overview.md)
* [Primeros pasos](advisor-get-started.md)
* [Recomendaciones sobre rendimiento de Advisor](advisor-cost-recommendations.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-cost-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-cost-recommendations.md)
