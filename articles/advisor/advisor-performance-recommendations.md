---
title: Recomendaciones sobre rendimiento de Azure Advisor | Microsoft Docs
description: Utilice Advisor para optimizar el rendimiento de las implementaciones de Azure.
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
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: d04c209894ab8ee35b92381f9679f68addd64f00
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-performance-recommendations"></a>Recomendaciones sobre rendimiento de Advisor

Las recomendaciones sobre rendimiento de Advisor ayudan a aumentar y mejorar la velocidad y la capacidad de respuesta de las aplicaciones empresariales críticas. Puede obtener las recomendaciones sobre rendimiento mediante Advisor desde la pestaña **Performance** (Rendimiento) del panel de Advisor.

![Pestaña Advisor Performance (Rendimiento de Advisor)](./media/advisor-performance-recommendations/advisor-performance-tab.png)

## <a name="sql-database-recommendations"></a>Recomendaciones de SQL Database

Advisor proporciona una vista coherente y consolidada de recomendaciones para todos los recursos de Azure. Se integra con SQL Database Advisor para ofrecer recomendaciones y mejorar el rendimiento de la base de datos de SQL Azure. SQL Database Advisor evalúa el rendimiento de las bases de datos SQL Azure mediante el análisis del historial de utilización. Después, ofrece las recomendaciones más adecuadas para ejecutar la carga de trabajo habitual de su base de datos. 

> [!NOTE]
> Para obtener recomendaciones, es preciso que una base de datos tenga aproximadamente una semana de utilización y, dentro de esa semana, debe haber alguna actividad coherente. SQL Database Advisor puede optimizar los patrones de consultas coherentes con más facilidad que en el caso de ráfagas irregulares aleatorias de actividad.

Para obtener más información acerca de SQL Database Advisor, vea [SQL Database Advisor](https://azure.microsoft.com/en-us/documentation/articles/sql-database-advisor/).

![Recomendaciones de SQL Database](./media/advisor-performance-recommendations/advisor-performance-sql.png)

## <a name="redis-cache-recommendations"></a>Recomendaciones de Redis Cache

Azure Advisor identifica las instancias de Redis Cache donde el rendimiento puede verse afectado negativamente por la utilización intensa de la memoria, la carga del servidor, el ancho de banda de red o un número elevado de conexiones de cliente. También proporciona sugerencias de los procedimientos recomendados para evitar posibles problemas. Para obtener más información acerca de las recomendaciones de Redis Cache, vea [Advisor de Redis Cache](https://azure.microsoft.com/en-us/documentation/articles/cache-configure/#redis-cache-advisor).


## <a name="app-services-recommendations"></a>Recomendaciones de App Services

Azure Advisor integra las sugerencias de los procedimientos recomendados para mejorar su experiencia de App Services y para descubrir las funciones de la plataforma adecuada. Ejemplos de recomendaciones de App Services:
- Detección de instancias en las que los tiempos de ejecución de las aplicaciones con opciones de mitigación agotan los recursos de la memoria o la CPU.
- Detección de instancias donde la colocación de recursos como Web Apps y bases de datos puede mejorar el rendimiento y reducir el costo. 

Para obtener más información acerca de las recomendaciones de App Services, consulte los [procedimientos recomendados para Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/app-service-best-practices/).
![Recomendaciones de App Services](./media/advisor-performance-recommendations/advisor-performance-app-service.png)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Obtención de acceso a las recomendaciones sobre rendimiento en Advisor

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, haga clic en **More services** (Más servicios), en el panel de menú de servicio, desplácese hacia abajo hasta **Monitoring and Management** (Supervisión y administración), y, a continuación, haga clic en **Azure Advisor**. Esto inicia el panel de Advisor. 
3. En el panel de Advisor, haga clic en la pestaña **Performance** (Rendimiento) y seleccione la suscripción para la que le gustaría recibir las recomendaciones.

> [!NOTE]
> Para obtener acceso a las recomendaciones de Advisor, primero debe **registrar** su suscripción en él. Una suscripción se registra cuando el **propietario de esta** inicia el panel de Advisor y hace clic en el botón **Get recommendations** (Obtener recomendaciones). Esta operación **solo se realiza una vez**. Una vez registrada la suscripción, los **propietarios**, **contribuidores**, o **lectores** de dicha suscripción, un grupo de recursos o un recursos concreto pueden acceder a las recomendaciones de Advisor.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las recomendaciones de Advisor, consulte los recursos siguientes:

-  [Introducción a Advisor](advisor-overview.md)
-  [Introducción a Advisor](advisor-get-started.md)
-  [Recomendaciones sobre alta disponibilidad de Advisor](advisor-high-availability-recommendations.md)
-  [Recomendaciones sobre seguridad de Advisor](advisor-security-recommendations.md)
-  [Recomendaciones sobre el costo de Advisor](advisor-performance-recommendations.md)

