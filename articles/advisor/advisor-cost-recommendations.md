---
title: Recomendaciones sobre el costo de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para optimizar el costo de las implementaciones de Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 51320d93689da3e37c0946d8877b27a11793d9c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-cost-recommendations"></a>Recomendaciones sobre el costo de Advisor

Advisor lo ayuda a optimizar y reducir el gasto global de Azure mediante la identificación de recursos inactivos e infrautilizados. Puede obtener recomendaciones sobre el costo en la pestaña **Cost** (Costo) del panel de Advisor.

![Pestaña Cost (Costo) de Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="optimize-virtual-machine-spend-by-resizing-underutilized-instances"></a>Optimización del gasto en máquinas virtuales mediante la adecuación del tamaño en instancias infrautilizadas 
Mientras que determinados escenarios de aplicaciones pueden dar lugar a un uso escaso debido al diseño, a menudo puede ahorrar dinero administrando el tamaño y número de máquinas virtuales. Advisor supervisa la utilización de las máquinas virtuales durante 14 días e identifica aquellas con una utilización escasa. Se considera que una máquina virtual tiene una utilización escasa si su utilización de la CPU es del 5 % o menos y el de la red es de 7 MB o menos durante cuatro o más días.

Advisor muestra el costo estimado de continuar ejecutando la máquina virtual, para que puede elegir entre apagarla o cambiar su tamaño.  

![Recomendaciones sobre el costo de Advisor para cambiar el tamaño de máquinas virtuales](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="use-a-cost-effective-solution-to-manage-performance-goals-of-multiple-sql-databases"></a>Usar una solución rentable para administrar los objetivos de rendimiento de varias bases de datos SQL
Advisor identifica las instancias de SQL Server que pueden beneficiarse de la creación de grupos de bases de datos elásticas. Los grupos de bases de datos elásticas proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de utilización diferentes. Para más información sobre los grupos de bases de datos elásticas de Azure, consulte [¿Qué es un grupo elástico de Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Recomendaciones sobre el costo de Advisor para grupos de bases de datos elásticas](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Obtención de acceso a las recomendaciones sobre el costo en Azure Advisor

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el panel izquierdo, haga clic en **Más servicios**.

3. En el panel de menú de servicio, en **Supervisión y administración**, haga clic en **Azure Advisor**.  
 Se muestra el panel de Advisor.

4. En el panel de Advisor, haga clic en la pestaña **Costo**.

5. Seleccione la suscripción para la que desea recibir las recomendaciones y haga clic en **Obtener recomendaciones**.

> [!NOTE]
> Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre las recomendaciones de Advisor, consulte:
* [Introducción a Advisor](advisor-overview.md)
* [Introducción](advisor-get-started.md)
* [Recomendaciones sobre rendimiento de Advisor](advisor-cost-recommendations.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-cost-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-cost-recommendations.md)
