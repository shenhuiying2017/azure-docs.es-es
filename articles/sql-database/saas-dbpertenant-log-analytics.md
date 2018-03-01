---
title: "Uso de Log Analytics con una aplicación multiinquilino de SQL Database | Microsoft Docs"
description: "Configuración y uso de Log Analytics (OMS) con la aplicación de SaaS de Azure SQL Database multiinquilino"
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: billgib; sstein
ms.openlocfilehash: 90510520e5f6bbfa8aea4026d7437a4a4881984f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configuración y uso de Log Analytics (OMS) con la aplicación SaaS de Azure SQL Database multiinquilino

En este tutorial, se va a configurar y usar *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para supervisar bases de datos y grupos elásticos. Este tutorial se basa en el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md). Muestra cómo usar *Log Analytics* para intensificar la supervisión y alerta proporcionadas en Azure Portal. Log Analytics admite la supervisión de miles de grupos elásticos y cientos de miles de bases de datos. Log Analytics proporciona una única solución de supervisión, que puede integrar la supervisión de distintas aplicaciones y servicios de Azure en varias suscripciones de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* Aplicación Wingtip Tickets SaaS Database Per Tenant implementada. Para implementarla en menos de cinco minutos, consulte el artículo de [implementación y exploración de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Consulte el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md) para una descripción de los patrones y escenarios de SaaS, y ver cómo afectan a los requisitos de una solución de supervisión.

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Supervisión y administración del rendimiento de bases de datos y grupos elásticos de Log Analytics u Operations Management Suite (OMS)

Para SQL Database, la supervisión y las alertas están disponibles para las bases de datos y los grupos en Azure Portal. Esta característica integrada de supervisión y alerta es práctica, pero se adapta menos a la supervisión de instalaciones de gran tamaño o para proporcionar una vista unificada de varias suscripciones y recursos.

Para escenarios de gran volumen, puede utilizarse Log Analytics para supervisión y alertas. Log Analytics es un servicio de Azure independiente, que permite el análisis sobre registros de diagnóstico y telemetría recopilados en un área de trabajo de potencialmente muchos servicios. Log Analytics proporciona herramientas integradas de visualización de los datos y lenguaje de consulta para el análisis de datos operativos. La solución de SQL Analytics proporciona varias vistas y consultas predefinidas de supervisión y alerta para las bases de datos y los grupos elásticos. OMS también ofrece un diseñador de vistas personalizadas.

Las soluciones analíticas y las áreas de trabajo de Log Analytics se pueden abrir tanto en Azure Portal como en OMS. Azure Portal es el punto de acceso más reciente, pero puede ser peor que OMS en ciertos aspectos.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Generación de datos de diagnóstico de rendimiento mediante la simulación de una carga de trabajo en los inquilinos 

1. En **PowerShell ISE**, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\**Demo-PerformanceMonitoringAndManagement.ps1***. Mantenga este script abierto, ya que quizá quiera ejecutar varios escenarios de generación de carga durante este tutorial.
1. Si aún no lo ha hecho, aprovisione un lote de inquilinos para proporcionar un contexto de supervisión más interesante. Esto tarda unos minutos:
   1. Establezca **$DemoScenario = 1**, _Aprovisionamiento de un lote de inquilinos_
   1. Para ejecutar el script e implementar 17 inquilinos adicionales, presione **F5**.  

1. Ahora inicie el generador de carga para ejecutar una carga simulada en todos los inquilinos.  
    1. Establezca **$DemoScenario = 2**, _Generación de una carga de intensidad normal (aprox. 30 DTU)_.
    1. Presione **F5** para ejecutar el script.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts PowerShell de Wingtip Tickets.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalación y configuración de Log Analytics y la solución Azure SQL Analytics

Log Analytics es un servicio independiente que debe configurarse. Log Analytics recopila datos del registro, telemetría y métricas de un área de trabajo de análisis de registros. Un área de trabajo de análisis de registro es un recurso, como otros de Azure, y debe crearse. Mientras que el área de trabajo no tiene por qué crearse en el mismo grupo de recursos de las aplicaciones que supervisa, a menudo esto es lo más razonable. Para la aplicación Wingtip Tickets, usar un grupo de recursos único garantiza que el área de trabajo se elimine con la aplicación.

1. En **PowerShell ISE**, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\**Demo-LogAnalytics.ps1***.
1. Presione **F5** para ejecutar el script.

En este momento debería poder abrir Log Analytics en Azure Portal (o en el portal de OMS). La telemetría tardará unos minutos en recopilarse y en aparecer en el área de trabajo de Log Analytics. Cuanto más tiempo deje que el sistema recopile datos de diagnóstico, más interesante será la experiencia. Ahora es un buen momento para tomar algo, siempre que se asegure de que el generador de carga sigue en ejecución.

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Uso de Log Analytics y la solución SQL Analytics para supervisar grupos y bases de datos


En este ejercicio, abra Log Analytics y el portal de OMS para observar cómo se recopila la telemetría para las bases de datos y los grupos.

1. Vaya a [Azure Portal](https://portal.azure.com) y abra haga clic en **Todos los servicios** para abrir Log Analytics, después, busque Log Analytics:

   ![apertura de Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Seleccione el área de trabajo denominada _wtploganalytics-&lt;user&gt;_.

1. Seleccione **Introducción** para abrir la solución Log Analytics en Azure Portal.

   ![vínculo a la introducción](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > La solución puede tardar unos minutos en activarse. Tenga paciencia.

1. Haga clic en el icono de Azure SQL Analytics para abrirlo.

    ![Información general](media/saas-dbpertenant-log-analytics/overview.png)

    ![análisis](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Las vistas en la solución se desplazan lateralmente, con su propia barra de desplazamiento interna en la parte inferior (actualice la página si es necesario).

1. Para explorar la página de resumen, haga clic en los iconos o en una base de datos individual para abrir el explorador en profundidad.

1. Cambie la configuración del filtro para modificar el intervalo de tiempo; para este tutorial, elija _Última hora_

    ![filtro de tiempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Seleccione una base de datos única para explorar las métricas y el uso de consultas para esa base de datos.

    ![análisis de base de datos](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver las métricas de uso, desplace la página de análisis a la derecha.
 
     ![métricas de base de datos](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Desplace la página de análisis a la izquierda y haga clic en el icono de servidor en la lista Información de recursos. Esto abre una página que muestra los grupos y las bases de datos en el servidor. 

     ![información de recursos](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![servidor con grupos y bases de datos](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. En la página del servidor que se abre y que muestra los grupos y las bases de datos en el servidor, haga clic en el grupo.  En la página de grupo que se abre, desplácese hacia la derecha para ver las métricas del grupo.  

     ![métricas de grupo](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. De nuevo en el área de trabajo de Log Analytics, seleccione el **Portal de OMS** para abrir el área de trabajo allí.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

En el Portal de OMS, puede explorar aún más los datos de registro y métricas en el área de trabajo.  

La supervisión y las alertas de Log Analytics y OMS se basan en consultas de datos en el área de trabajo, a diferencia de las alertas definidas en cada recurso en Azure Portal. Al basar las alertas en consultas, puede definir una alerta única que busque en todas las bases de datos, en lugar de definir una por cada base de datos. Las consultas solo se ven limitadas por los datos disponibles en el área de trabajo.

Para obtener más información sobre el uso de OMS para consultar y establecer alertas, consulte [Uso de reglas de alertas en Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics para SQL Database se cobra en función del volumen de datos del área de trabajo. En este tutorial, ha creado un área de trabajo disponible, que está limitado a 500 MB al día. Una vez alcanzado ese límite, ya no se agregan más datos al área de trabajo.


## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

[Tutorial sobre el análisis de inquilinos](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
