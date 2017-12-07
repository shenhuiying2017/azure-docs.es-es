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
ms.openlocfilehash: 48e8eb91a5febcc1109bee3404bb534bd0391f88
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configuración y uso de Log Analytics (OMS) con la aplicación SaaS de Azure SQL Database multiinquilino

En este tutorial, se va a configurar y usar *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para supervisar bases de datos y grupos elásticos. Este tutorial se basa en el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md). Muestra cómo usar *Log Analytics* para intensificar la supervisión y alerta proporcionadas en Azure Portal. Log Analytics es adecuado para la supervisión y las alertas de escalado, ya que admite cientos de grupos y cientos de miles de bases de datos. También proporciona una única solución de supervisión, que puede integrar la supervisión de distintas aplicaciones y servicios de Azure en varias suscripciones de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Aplicación Wingtip Tickets SaaS Database Per Tenant implementada. Para implementarla en menos de cinco minutos, consulte el artículo de [implementación y exploración de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte el [tutorial de administración y supervisión del rendimiento](saas-dbpertenant-performance-monitoring.md) para una descripción de los patrones y escenarios de SaaS, y ver cómo afectan a los requisitos de una solución de supervisión.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Supervisión y administración del rendimiento con Log Analytics (OMS)

Para SQL Database, la supervisión y las alertas están disponibles para las bases de datos y los grupos. Esta característica integrada de supervisión y alerta es específica del recurso y práctica con un número reducido de recursos; es menos adecuada para la supervisión de instalaciones de gran tamaño o para proporcionar una vista unificada de varias suscripciones y recursos.

Para escenarios de gran volumen, puede utilizarse Log Analytics. Se trata de un servicio de Azure independiente que proporciona análisis de los registros de diagnóstico emitidos y de telemetría recopilados en un área de trabajo de Log Analytics, que recopila la telemetría de muchos servicios y sirve para consultar y establecer alertas. Log Analytics proporciona herramientas de visualización de los datos y lenguaje de consulta integrados para el análisis y la visualización de datos operativos. La solución de SQL Analytics proporciona varias vistas y consultas de supervisión y alerta para las bases de datos y los grupos elásticos; además, le permite agregar sus propias consultas ad hoc y guardarlas según sea necesario. OMS también ofrece un diseñador de vistas personalizadas.

Las soluciones analíticas y las áreas de trabajo de Log Analytics se pueden abrir tanto en Azure Portal como en OMS. Azure Portal es el punto de acceso más reciente, pero puede ser peor que OMS en ciertos aspectos.

### <a name="create-data-by-starting-the-load-generator"></a>Creación de datos iniciando el generador de carga 

1. En **PowerShell ISE**, abra **Demo-PerformanceMonitoringAndManagement.ps1**. Mantenga este script abierto, ya que quizá quiera ejecutar varios escenarios de generación de carga durante este tutorial.
1. Aprovisione un lote de inquilinos si tiene menos de cinco para proporcionar un contexto de supervisión más interesante:
   1. Establezca **$DemoScenario = 1,** **Aprovisionamiento de un lote de inquilinos**
   1. Presione **F5** para ejecutar el script.

1. Establezca **$DemoScenario** = 2, **Generación de una carga de intensidad normal (aprox. 40 DTU)**.
1. Presione **F5** para ejecutar el script.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip Tickets SaaS Database Per Tenant

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalación y configuración de Log Analytics y la solución Azure SQL Analytics

Log Analytics es un servicio independiente que debe configurarse. Log Analytics recopila datos del registro, y telemetría y métricas de un área de trabajo de análisis de registros. Un área de trabajo es un recurso y debe crearse, como otros de Azure. Mientras que el área de trabajo no tiene por qué crearse en el mismo grupo de recursos de las aplicaciones que supervisa, a menudo esto es lo más razonable. Para la aplicación Wingtip Tickets SaaS Database Per Tenant, esto permite que el área de trabajo se pueda eliminar fácilmente con la aplicación al eliminar el grupo de recursos.

1. En **PowerShell ISE**, abra ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1*.
1. Presione **F5** para ejecutar el script.

En este momento debería poder abrir Log Analytics en Azure Portal (o en el portal de OMS). La telemetría tardará unos minutos en recopilarse y en aparecer en el área de trabajo de Log Analytics. Cuanto más tiempo deje que el sistema recopile datos, más interesante será la experiencia. Ahora es un buen momento para tomar algo, siempre que se asegure de que el generador de carga sigue en ejecución.


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Uso de Log Analytics y la solución SQL Analytics para supervisar grupos y bases de datos


En este ejercicio, abra Log Analytics y el portal de OMS para observar cómo se recopila la telemetría para las bases de datos y los grupos.

1. Vaya a [Azure Portal](https://portal.azure.com) y abra haga clic en Más servicios para abrir Log Analytics, después, busque Log Analytics:

   ![apertura de Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Seleccione el área de trabajo denominado *wtploganalytics -&lt;USER&gt;*.

1. Seleccione **Introducción** para abrir la solución Log Analytics en Azure Portal.
   ![vínculo a la introducción](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > La solución puede tardar unos minutos en activarse. Tenga paciencia.

1. Haga clic en el icono de Azure SQL Analytics para abrirlo.

    ![Información general](media/saas-dbpertenant-log-analytics/overview.png)

    ![análisis](media/saas-dbpertenant-log-analytics/analytics.png)

1. La vista en la hoja de la solución se desplaza lateralmente, con su propia barra de desplazamiento en la parte inferior (actualice la hoja si es necesario).

1. Explore las distintas vistas haciendo clic en ellas o en los recursos individuales para abrir un explorador de detalles, donde podrá usar el control deslizante de tiempo de la esquina superior izquierda o hacer clic en una barra vertical para centrarse en un intervalo de tiempo más breve. Con esta vista puede seleccionar bases de datos o grupos individuales para centrarse en recursos específicos:

    ![gráfico](media/saas-dbpertenant-log-analytics/chart.png)

1. En la hoja de la solución, si se desplaza a la derecha del todo verá algunas consultas guardadas en las que podrá hacer clic para abrirlas y explorarlas. Puede experimentar modificándolas y guardar las consultas interesantes que genere para después volver a abrirlas y usarlas con otros recursos.

1. De nuevo en la hoja del área de trabajo de Log Analytics, seleccione el Portal de OMS para abrir la solución ahí.

    ![oms](media/saas-dbpertenant-log-analytics/oms.png)

1. En el portal de OMS puede configurar alertas. Haga clic en la parte de alertas de la vista de DTU de la base de datos.

1. En la vista de búsqueda de registros que aparece, verá un gráfico de barras de las métricas que se van a representar.

    ![búsqueda de registros](media/saas-dbpertenant-log-analytics/log-search.png)

1. Si hace clic en Alerta en la barra de herramientas, podrá ver la configuración de las alertas y cambiarla.

    ![incorporación de regla de alerta](media/saas-dbpertenant-log-analytics/add-alert.png)

La supervisión y las alertas de Log Analytics y OMS se basan en consultas de datos en el área de trabajo, a diferencia de las alertas de las hojas de recursos, que son específicas del recurso. Por lo tanto, puede definir una alerta que se busque en todas las bases de datos, por ejemplo, en lugar de definir una por cada base de datos. También puede escribir una alerta que use una consulta compuesta para varios tipos de recursos. Las consultas solo se ven limitadas por los datos disponibles en el área de trabajo.

Log Analytics para SQL Database se cobra en función del volumen de datos del área de trabajo. En este tutorial ha creado un área de trabajo disponible, que está limitado a 500 MB al día. Una vez alcanzado ese límite, ya no se agregan más datos al área de trabajo.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

[Tutorial sobre el análisis de inquilinos](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
