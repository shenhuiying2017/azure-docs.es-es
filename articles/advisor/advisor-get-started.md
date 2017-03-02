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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>Introducción a Azure Advisor

Obtenga información acerca de cómo acceder a Advisor mediante Azure Portal y obtener, implementar y buscar las recomendaciones, así como actualizarlas.

## <a name="get-advisor-recommendations"></a>Obtención de recomendaciones de Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, haga clic en **More services** (Más servicios), y en el panel de menú de servicio, desplácese hacia abajo hasta **Monitoring and Management** (Supervisión y administración), y, a continuación, haga clic en **Azure Advisor**. Esto inicia el panel de Advisor.

  ![Acceso a Azure Advisor mediante Azure Portal](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. En el panel de Advisor, seleccione la suscripción para la que le gustaría recibir las recomendaciones. El panel de Advisor muestra recomendaciones personalizadas de la suscripción seleccionada. 
4. Para obtener recomendaciones para una categoría determinada, haga clic en una de las pestañas **High Availability** (Alta disponibilidad), **Security** (Seguridad), **Performance** (Rendimiento) o **Cost** (Costo).
 
> [!NOTE]
> Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el **propietario de esta** inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

  ![Panel de Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Obtención de detalles de recomendación de Advisor e implementación de una recomendación

La hoja **Recommendation** (Recomendación) en Advisor ofrece información adicional acerca de la recomendación de Advisor. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después inicie [Azure Advisor](https://aka.ms/azureadvisordashboard).
2. En el panel **Advisor recommendations** (Recomendaciones de Advisor), haga clic en **Obtener recomendación**.
3. En la lista de recomendaciones, haga clic en una recomendación que desee revisar en detalle. Se abre la hoja de recomendaciones.
4. En la hoja de recomendaciones, revise la información sobre las acciones que puede llevar a cabo para resolver un problema potencial o aprovechar una oportunidad de ahorro de costos. 
  
  ![Ejemplo de acción de recomendación de Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Búsqueda de recomendaciones de Advisor

Puede buscar recomendaciones para una suscripción específica o un grupo de recursos. También puede buscar recomendaciones por estado.

1. Inicie sesión en Azure Portal y después inicie Azure Advisor.
2. Busque las recomendaciones mediante el filtrado de las suscripciones, grupos de recursos y el estado de la recomendación (**Active** [Activa] o **Snoozed** [Pospuesta]).
3. Haga clic en **Obtener recomendaciones** para obtener una lista de las recomendaciones de Advisor en función de los filtros de búsqueda.

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Posposición de recomendaciones de Advisor

1. Inicie sesión en Azure Portal y después inicie Azure Advisor.
2. Haga clic en **Obtener recomendaciones** y después haga clic en una recomendación de la lista de recomendaciones.
3. En la hoja **Recommendation** (Recomendación), haga clic en **Snooze** (Posponer).  Puede especificar un tiempo de posposición o seleccionar **Never** (Nunca) para descartar la recomendación.

  ![Ejemplo de acción de recomendación de Advisor](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Advisor, consulte estos recursos:
-  [Introducción a Azure Advisor](advisor-overview.md)
-  [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
-  [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
-  [Recomendaciones sobre rendimiento de Advisor](advisor-performance-recommendations.md)
-  [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)

