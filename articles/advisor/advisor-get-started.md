---
title: "Introducción a Azure Advisor | Microsoft Docs"
description: "Introducción a Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: a662841bebda460d4225e080f16705b3f16fdc46
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-advisor"></a>Introducción a Azure Advisor

Obtenga información acerca de cómo acceder a Advisor mediante Azure Portal y obtener, implementar y buscar las recomendaciones, así como actualizarlas.

## <a name="get-advisor-recommendations"></a>Obtención de recomendaciones de Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el panel izquierdo, haga clic en **Más servicios**.

3. En el panel de menú de servicio, en **Supervisión y administración**, haga clic en **Azure Advisor**.  
 Se muestra el panel de Advisor.

   ![Acceso a Azure Advisor mediante Azure Portal](./media/advisor-overview/advisor-azure-portal-menu.png) 

4. En el panel de Advisor, seleccione la suscripción para la que le gustaría recibir las recomendaciones.  
El panel de Advisor muestra recomendaciones personalizadas de la suscripción seleccionada. 

5. Para obtener recomendaciones para una categoría determinada, haga clic en una de las pestañas **High Availability** (Alta disponibilidad), **Security** (Seguridad), **Performance** (Rendimiento) o **Cost** (Costo).
 
> [!NOTE]
> Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

  ![Panel de Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Obtener detalles de recomendaciones de Advisor e implementar una solución

La hoja **Recommendation** (Recomendación) en Advisor ofrece información adicional acerca de la recomendación de Advisor. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después inicie [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. En el panel **Advisor recommendations** (Recomendaciones de Advisor), haga clic en **Obtener recomendación**.

3. En la lista de recomendaciones, haga clic en una recomendación que desee revisar en detalle.  
Se abre la hoja **Recomendación**.

4. En la hoja **Recomendaciones**, revise la información sobre las acciones que puede llevar a cabo para resolver un problema potencial o aprovechar una oportunidad de ahorro de costos. 
  
  ![Hoja Recomendación de Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Búsqueda de recomendaciones de Advisor

Puede buscar recomendaciones para una suscripción específica o un grupo de recursos. También puede buscar recomendaciones por estado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después inicie [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Busque las recomendaciones mediante el filtrado de las suscripciones, grupos de recursos y el estado de la recomendación (**Active** [Activa] o **Snoozed** [Pospuesta]).

3. Haga clic en **Obtener recomendaciones** para obtener una lista de las recomendaciones de Advisor en función de los filtros de búsqueda.

  ![Criterios de filtro de búsqueda de Advisor](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Posponer o descartar recomendaciones de Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después inicie [Azure Advisor](https://aka.ms/azureadvisordashboard).

2. Haga clic en **Obtener recomendaciones** y después haga clic en una recomendación de la lista de recomendaciones.

3. En la hoja **Recommendation** (Recomendación), haga clic en **Snooze** (Posponer).  

   ![Ejemplo de acción de recomendación de Advisor](./media/advisor-get-started/advisor-snooze.png)

4. Puede especificar el tiempo que se pospondrá o seleccionar **Never** (Nunca) para descartar la recomendación.


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Advisor, consulte:
* [Introducción a Azure Advisor](advisor-overview.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)
