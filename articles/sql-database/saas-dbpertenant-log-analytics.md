---
title: Uso de Log Analytics con una aplicación multiinquilino de SQL Database | Microsoft Docs
description: Configuración y uso de Log Analytics con la aplicación SaaS de Azure SQL Database multiinquilino
keywords: tutorial de SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 285b8d0acc8a6cbe1a6441a4aabf372de204309e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Configuración y uso de Log Analytics con la aplicación SaaS de SQL Database multiinquilino

En este tutorial, se va a configurar y usar Azure [Log Analytics](/azure/log-analytics/log-analytics-overview) para supervisar bases de datos y grupos elásticos. Este tutorial se basa en el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md). Se muestra cómo usar Log Analytics para intensificar la supervisión y alerta proporcionadas en Azure Portal. Log Analytics admite la supervisión de miles de grupos elásticos y cientos de miles de bases de datos. Log Analytics proporciona una única solución de supervisión, que puede integrar la supervisión de distintas aplicaciones y servicios de Azure en varias suscripciones de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics.
> * Usar Log Analytics para supervisar grupos y bases de datos.

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Aplicación Wingtip Tickets SaaS Database Per Tenant implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre [implementación y exploración de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte el artículo de [introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md) para una descripción de los patrones y escenarios de SaaS, y ver cómo afectan a los requisitos de una solución de supervisión.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Supervisión y administración del rendimiento de bases de datos y grupos elásticos con Log Analytics

En Azure SQL Database, la supervisión y alerta están disponibles en bases de datos y grupos de Azure Portal. Esta supervisión y alerta integrada resulta práctica, pero también es específica del recurso. Esto significa que resulta menos adecuada para supervisar grandes instalaciones o para proporcionar una vista unificada entre recursos y suscripciones.

En escenarios de gran volumen, puede usarse Log Analytics para supervisión y alerta. Log Analytics es un servicio de Azure independiente, que permite el análisis sobre registros de diagnóstico y telemetría recopilados en un área de trabajo de muchos servicios en potencia. Log Analytics proporciona herramientas integradas de visualización de datos y lenguaje de consulta para el análisis de datos operativos. La solución SQL Analytics proporciona varias vistas y consultas predefinidas de supervisión y alerta de bases de datos y grupos elásticos. Log Analytics también ofrece un diseñador de vistas personalizadas.

Las áreas de trabajo y soluciones de análisis de Log Analytics se abren en Azure Portal y en Operations Management Suite. Azure Portal es el punto de acceso más reciente, pero, en ciertos aspectos, podría ir por detrás del portal de Operations Management Suite.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Generación de datos de diagnóstico de rendimiento mediante la simulación de una carga de trabajo en los inquilinos 

1. En PowerShell ISE, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*. Mantenga este script abierto, ya que quizá quiera ejecutar varios de los escenarios de generación de carga durante este tutorial.
2. Si aún no lo ha hecho, aprovisione un lote de inquilinos para que el contexto de supervisión sea más interesante. Este proceso tarda unos minutos.

   a. Establezca **$DemoScenario = 1**, _Aprovisionamiento de un lote de inquilinos_.

   b. Para ejecutar el script e implementar 17 inquilinos adicionales, presione F5.

3. Ahora inicie el generador de carga para ejecutar una carga simulada en todos los inquilinos.

    a. Establezca **$DemoScenario = 2**, _Generación de una carga de intensidad normal (aprox. 30 DTU)_.

    b. Presione F5 para ejecutar el script.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts de PowerShell de Wingtip Tickets.

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalación y configuración de Log Analytics y la solución Azure SQL Analytics

Log Analytics es un servicio independiente que debe configurarse. Log Analytics recopila datos de registro, telemetría y métricas de un área de trabajo de análisis de registros. Al igual que otros recursos de Azure, el área de trabajo de Log Analytics se debe crear, pero no en el mismo grupo de recursos que las aplicaciones que supervisa. Si bien, hacer esto es a menudo lo que tiene más sentido. Para la aplicación Wingtip Tickets, use un grupo de recursos único para garantizar que el área de trabajo se elimine con la aplicación.

1. En PowerShell ISE, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
2. Presione F5 para ejecutar el script.

Ahora puede abrir Log Analytics en Azure Portal o en el portal de Operations Management Suite. Se tarda unos minutos en recopilar la telemetría en el área de trabajo de Log Analytics y para que sea visible. Cuanto más tiempo deje que el sistema recopile datos de diagnóstico, más interesante será la experiencia. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Uso de Log Analytics y la solución SQL Analytics para supervisar grupos y bases de datos


En este ejercicio, abra Log Analytics y el portal de Operations Management Suite para examinar la telemetría recopilada para las bases de datos y los grupos.

1. Vaya a [Azure Portal](https://portal.azure.com). Seleccione **Todos los servicios** para abrir Log Analytics. A continuación, busque Log Analytics.

   ![Apertura de Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Seleccione el área de trabajo denominada _wtploganalytics-&lt;user&gt;_.

3. Seleccione **Introducción** para abrir la solución Log Analytics en Azure Portal.

   ![Información general](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > La solución puede tardar unos minutos en activarse. 

4. Seleccione el icono de **Azure SQL Analytics** para abrirlo.

    ![Icono de información general](media/saas-dbpertenant-log-analytics/overview.png)

5. Las vistas en la solución se desplazan lateralmente, con su propia barra de desplazamiento interna en la parte inferior. Si es necesario, actualice la página.

6. Para explorar la página de resumen, seleccione los iconos o bases de datos individuales para abrir un explorador de exploración en profundidad.

    ![Panel de Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Cambie la configuración de filtro para modificar el intervalo de tiempo. En este tutorial, seleccione **Last 1 hour** (Última hora).

    ![Filtro de tiempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Seleccione una base de datos única para explorar las métricas y el uso de consultas para esa base de datos.

    ![Análisis de base de datos](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. Para ver las métricas de uso, desplace la página de análisis a la derecha.
 
     ![Métricas de base de datos](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Desplace la página de análisis a la izquierda y seleccione el icono de servidor en la lista de **información de recursos**.  

    ![Lista de información de recursos](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Se abre una página que muestra los grupos y las bases de datos en el servidor.

    ![Servidor con grupos y bases de datos](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Seleccione un grupo. En la página de grupo que se abre, desplácese hacia la derecha para ver las métricas del grupo. 

    ![Métricas de grupo](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. De nuevo en el área de trabajo de Log Analytics, seleccione el **portal de OMS** para abrir ahí el área de trabajo.

    ![Icono del portal de Operations Management Suite](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

En el portal de Operations Management Suite, puede explorar aún más los datos de registro y métricas del área de trabajo. 

La supervisión y las alertas de Log Analytics se basan en consultas de datos en el área de trabajo, a diferencia de las alertas definidas en cada recurso en Azure Portal. Al basar las alertas en consultas, puede definir una alerta única que busque en todas las bases de datos, en lugar de definir una por cada base de datos. Las consultas solo se ven limitadas por los datos disponibles en el área de trabajo.

Para más información sobre cómo usar Log Analytics para consultar y definir alertas, consulte [Uso de reglas de alertas en Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics para SQL Database se cobra en función del volumen de datos del área de trabajo. En este tutorial, ha creado un área de trabajo gratuita, que está limitada a 500 MB al día. Una vez alcanzado ese límite, ya no se agregan más datos al área de trabajo.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics.
> * Usar Log Analytics para supervisar grupos y bases de datos.

Pruebe el [tutorial de análisis de inquilinos](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
