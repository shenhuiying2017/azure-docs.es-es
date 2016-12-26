---
title: Recomendaciones sobre el costo de Azure Advisor | Microsoft Docs
description: Utilice Azure Advisor para optimizar el costo de las implementaciones de Azure.
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: a560aa9cc6c70fd3827f649627466c9071b8ab8b
ms.openlocfilehash: 39a882a8338ce820c6b9b98f58d1a9db7448c7be

---

# <a name="advisor-cost-recommendations"></a>Recomendaciones sobre el costo de Advisor

Advisor lo ayuda a optimizar y reducir el gasto global de Azure mediante la identificación de recursos inactivos e infrautilizados. Puede obtener recomendaciones sobre el costo en la pestaña **Cost** (Costo) del panel de Advisor.

![Pestaña Cost (Costo) de Advisor](./media/advisor-cost-recommendations/advisor-cost-tab2.png)

## <a name="low-utilization-virtual-machines"></a>Máquinas virtuales con un uso escaso 

Mientras que determinados escenarios de aplicaciones pueden dar lugar a un uso escaso debido al diseño, a menudo puede ahorrar dinero administrando el tamaño y el número de máquinas virtuales. Advisor supervisa la utilización de las máquinas virtuales durante 14 días e identifica aquellas con una utilización escasa. Se considera que una máquina virtual tiene una utilización escasa si su utilización de la CPU es del 5 % o menos y el de la red es de 7 MB o menos durante cuatro o más días.

Advisor le muestra el costo estimado de seguir ejecutando la máquina virtual. Puede apagar o cambiar el tamaño de la máquina virtual.  

![Recomendaciones sobre el costo de Advisor para cambiar el tamaño de máquinas virtuales](./media/advisor-cost-recommendations/advisor-cost-resizevms.png)

## <a name="sql-elastic-database-pool-recommendations"></a>Recomendaciones sobre los grupos de bases de datos elásticas SQL

Advisor identifica las instancias de SQL Server que pueden beneficiarse de la creación de grupos de bases de datos elásticas. Los grupos de bases de datos elásticas proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de utilización diferentes. Para obtener más información sobre los grupos de bases de datos elásticas de Azure, consulte [¿Qué es un grupo elástico de Azure?](https://azure.microsoft.com/en-us/documentation/articles/sql-database-elastic-pool/)

![Recomendaciones sobre el costo de Advisor para grupos de bases de datos elásticas](./media/advisor-cost-recommendations/advisor-cost-elasticdbpools.png)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Obtención de acceso a las recomendaciones sobre el costo en Azure Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, haga clic en **More services** (Más servicios), en el panel de menú de servicio, desplácese hacia abajo hasta **Monitoring and Management** (Supervisión y administración), y, a continuación, haga clic en **Azure Advisor**. Esto inicia el panel de Advisor. 
3. En el panel de Advisor, haga clic en la pestaña **Cost** (Costo), seleccione la suscripción para la que le gustaría recibir las recomendaciones y después haga clic en **Obtener recomendaciones**

> [!NOTE]
> Azure Advisor genera recomendaciones para las suscripciones en las que se le ha asignado el rol de **propietario**, **colaborador** o **lector**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las recomendaciones de Advisor, consulte los recursos siguientes:
-  [Introducción a Advisor](advisor-overview.md)
-  [Primeros pasos](advisor-get-started.md)
-  [Recomendaciones sobre alta disponibilidad de Advisor](advisor-cost-recommendations.md)
-  [Recomendaciones sobre seguridad de Advisor](advisor-cost-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-cost-recommendations.md)



<!--HONumber=Nov16_HO3-->


