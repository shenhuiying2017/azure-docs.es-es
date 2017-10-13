---
title: Recomendaciones sobre rendimiento de Azure Advisor | Microsoft Docs
description: Utilice Advisor para optimizar el rendimiento de las implementaciones de Azure.
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
ms.openlocfilehash: 995a1f37a3fd68b39c14a95d46109c0f7814018d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-performance-recommendations"></a>Recomendaciones sobre rendimiento de Advisor

Las recomendaciones sobre rendimiento de Azure Advisor ayudan a mejorar la velocidad y la capacidad de respuesta de las aplicaciones empresariales críticas. Puede obtener las recomendaciones sobre rendimiento de Advisor en la pestaña **Rendimiento** del panel de Advisor.

![Pestaña Advisor Performance (Rendimiento de Advisor)](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="improve-database-performance-with-sql-db-advisor"></a>Mejora del rendimiento de la base de datos con SQL DB Advisor

Advisor proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure. Se integra con SQL Database Advisor para ofrecer recomendaciones y mejorar el rendimiento de la base de datos de SQL Azure. SQL Database Advisor evalúa el rendimiento de las bases de datos SQL Azure mediante el análisis del historial de utilización. Después, ofrece las recomendaciones más adecuadas para ejecutar la carga de trabajo habitual de la base de datos. 

> [!NOTE]
> Para obtener recomendaciones, es preciso que una base de datos lleve usándose aproximadamente una semana y que, dentro de esa semana, muestre alguna actividad coherente. SQL Database Advisor puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas aleatorias de actividad.

Para más información acerca de SQL Database Advisor, consulte [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Recomendaciones de SQL Database](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="improve-redis-cache-performance-and-reliability"></a>Mejora de la confiabilidad y el rendimiento de Redis Cache

Advisor identifica las instancias de Redis Cache donde el rendimiento puede verse afectado negativamente por la utilización intensa de la memoria, la carga del servidor, el ancho de banda de red o un número elevado de conexiones de cliente. Advisor también sugiere procedimientos recomendados para evitar posibles problemas. Para obtener más información acerca de las recomendaciones de Redis Cache, vea [Advisor de Redis Cache](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="improve-app-service-performance-and-reliability"></a>Mejora de la confiabilidad y el rendimiento de App Service

Azure Advisor integra las sugerencias de los procedimientos recomendados para mejorar su experiencia de App Services y para descubrir las funciones de la plataforma adecuada. Ejemplos de recomendaciones de App Services:
* Detección de instancias en las que los tiempos de ejecución de las aplicaciones con opciones de mitigación agotan los recursos de la memoria o la CPU.
* Detección de instancias donde la colocación de recursos como aplicaciones web y bases de datos puede mejorar el rendimiento y reducir el costo. 

Para obtener más información acerca de las recomendaciones de App Services, consulte los [procedimientos recomendados para Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Recomendaciones de App Services](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Obtención de acceso a las recomendaciones sobre rendimiento en Advisor

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el panel izquierdo, haga clic en **Más servicios**.

3. En el panel de menú de servicio, en **Supervisión y administración**, haga clic en **Azure Advisor**.  
 Se muestra el panel de Advisor.

4. En el panel de Advisor, haga clic en la pestaña **Rendimiento**.

5. Seleccione la suscripción para la que desea recibir las recomendaciones y haga clic en **Obtener recomendaciones**.

> [!NOTE]
> Para acceder a las recomendaciones de Advisor, primero debe *registrar su suscripción* en dicha solución. Una suscripción se registra cuando el *propietario de esta* inicia el panel de Advisor y hace clic en el botón **Obtener recomendaciones**. Esta operación *solo se realiza una vez*. Una vez registrada la suscripción, puede acceder a las recomendaciones de Advisor como *Propietario*, *Contribuidor* o *Lector* para una suscripción, un grupo de recursos o un recurso concreto.

## <a name="next-steps"></a>Pasos siguientes

Para aprender más sobre las recomendaciones de Advisor, consulte:

* [Introducción a Advisor](advisor-overview.md)
* [Introducción a Advisor](advisor-get-started.md)
* [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)
* [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
* [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)

