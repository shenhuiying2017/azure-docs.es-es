---
title: "Uso de Log Analytics con una aplicación multiinquilino de SQL Database | Microsoft Docs"
description: "Configuración y uso de Log Analytics (OMS) con la aplicación de ejemplo Wingtip Tickets (WTP) de Azure SQL Database"
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 813a947ce4deb0755b44f4d287e00ae5218abfc4
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017


---
# <a name="setup-and-use-log-analytics-oms-with-the-wtp-sample-saas-app"></a>Instalación y uso de Log Analytics (OMS) con la aplicación SaaS WTP de ejemplo

En este tutorial, instalaremos y usaremos *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* con la aplicación WTP para supervisar bases de datos y grupos elásticos. Se basa en el [tutorial de administración y supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md) y muestra cómo utilizar *Log Analytics* para intensificar la supervisión y las alertas de Azure Portal. Log Analytics es especialmente adecuado para la supervisión y las alertas de escalado, ya que admite cientos de grupos y cientos de miles de bases de datos. También proporciona una única solución de supervisión, que puede integrar la supervisión de distintas aplicaciones y servicios de Azure en varias suscripciones de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* La aplicación WTP está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [Implementación y exploración de la aplicación SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte el [tutorial de administración y supervisión del rendimiento](sql-database-saas-tutorial-performance-monitoring.md) para una descripción de los patrones y escenarios de SaaS, y ver cómo afectan a los requisitos de una solución de supervisión.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Supervisión y administración del rendimiento con Log Analytics (OMS)

Para SQL Database, la supervisión y las alertas están disponibles para las bases de datos y los grupos. Esta característica integrada de supervisión y alerta es específica del recurso y práctica con un número reducido de recursos; es menos adecuada para la supervisión de instalaciones de gran tamaño o para proporcionar una vista unificada de varias suscripciones y recursos.

Para escenarios de gran volumen, puede utilizarse Log Analytics. Se trata de un servicio de Azure independiente que proporciona análisis de los registros de diagnóstico emitidos y de telemetría recopilados en un área de trabajo de Log Analytics, que recopila la telemetría de muchos servicios y sirve para consultar y establecer alertas. Log Analytics proporciona herramientas de visualización de los datos y lenguaje de consulta integrados para el análisis y la visualización de datos operativos. La solución de SQL Analytics proporciona varias vistas y consultas de supervisión y alerta para las bases de datos y los grupos elásticos; además, le permite agregar sus propias consultas ad hoc y guardarlas según sea necesario. OMS también ofrece un diseñador de vistas personalizadas.

Las soluciones analíticas y las áreas de trabajo de Log Analytics se pueden abrir tanto en Azure Portal como en OMS. Azure Portal es el punto de acceso más reciente, pero puede ser peor que OMS en ciertos aspectos.

### <a name="start-the-load-generator-to-create-data-to-analyze"></a>Inicio del generador de carga para crear datos y analizarlos

1. Abra **Demo-PerformanceMonitoringAndManagement.ps1** en **PowerShell ISE**. Mantenga este script abierto, ya que quizá quiera ejecutar varios escenarios de generación de carga durante este tutorial.
1. Aprovisione un lote de inquilinos si tiene menos de cinco para proporcionar un contexto de supervisión más interesante:
   1. Establezca **$DemoScenario = 1,** **Aprovisionamiento de un lote de inquilinos**
   1. Presione **F5** para ejecutar el script.

1. Establezca **$DemoScenario** = 2, **Generación de una carga de intensidad normal (aprox. 40 DTU)**.
1. Presione **F5** para ejecutar el script.

## <a name="get-the-wingtip-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalación y configuración de Log Analytics y la solución Azure SQL Analytics

Log Analytics es un servicio independiente que debe configurarse. Log Analytics recopila datos del registro, y telemetría y métricas de un área de trabajo de análisis de registros. Un área de trabajo es un recurso y debe crearse, como otros de Azure. Mientras que el área de trabajo no tiene por qué crearse en el mismo grupo de recursos de las aplicaciones que supervisa, a menudo esto es lo más razonable. En el caso de la aplicación WTP, esto permite que el área de trabajo se pueda eliminar fácilmente con la aplicación con solo eliminar el grupo de recursos.

1. Abra ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1* en **PowerShell ISE**.
1. Presione **F5** para ejecutar el script.

En este momento debería poder abrir Log Analytics en Azure Portal (o en el portal de OMS). La telemetría tardará unos minutos en recopilarse y en aparecer en el área de trabajo de Log Analytics. Cuanto más tiempo deje que el sistema recopile datos, más interesante será la experiencia. Ahora es un buen momento para tomar algo, siempre que se asegure de que el generador de carga sigue en ejecución.


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Uso de Log Analytics y la solución SQL Analytics para supervisar grupos y bases de datos


En este ejercicio, abra Log Analytics y el portal de OMS para observar cómo se recopila la telemetría para las bases de datos de WTP y los grupos.

1. Vaya a [Azure Portal](https://portal.azure.com) y abra haga clic en Más servicios para abrir Log Analytics, después, busque Log Analytics:

   ![apertura de Log Analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Seleccione el área de trabajo denominado *wtploganalytics -&lt;USER&gt;*.

1. Seleccione **Introducción** para abrir la solución Log Analytics en Azure Portal.
   ![vínculo a la introducción](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **IMPORTANTE**: la solución puede tardar unos minutos en activarse. Tenga paciencia.

1. Haga clic en el icono de Azure SQL Analytics para abrirlo.

    ![Información general](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![análisis](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. La vista en la hoja de la solución se desplaza lateralmente, con su propia barra de desplazamiento en la parte inferior (actualice la hoja si es necesario).

1. Explore las distintas vistas haciendo clic en ellas o en los recursos individuales para abrir un explorador de detalles, donde podrá usar el control deslizante de tiempo de la esquina superior izquierda o hacer clic en una barra vertical para centrarse en un intervalo de tiempo más breve. Con esta vista puede seleccionar bases de datos o grupos individuales para centrarse en recursos específicos:

    ![gráfico](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. En la hoja de la solución, si se desplaza a la derecha del todo verá algunas consultas guardadas en las que podrá hacer clic para abrirlas y explorarlas. Puede experimentar modificándolas y guardar las consultas interesantes que genere para después volver a abrirlas y usarlas con otros recursos.

1. De nuevo en la hoja del área de trabajo de Log Analytics, seleccione el Portal de OMS para abrir la solución ahí.

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. En el portal de OMS puede configurar alertas. Haga clic en la parte de alertas de la vista de DTU de la base de datos.

1. En la vista de búsqueda de registros que aparece, verá un gráfico de barras de las métricas que se van a representar.

    ![búsqueda de registros](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Si hace clic en Alerta en la barra de herramientas, podrá ver la configuración de las alertas y cambiarla.

    ![incorporación de regla de alerta](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

La supervisión y las alertas de Log Analytics y OMS se basan en consultas de datos en el área de trabajo, a diferencia de las alertas de las hojas de recursos, que son específicas del recurso. Por lo tanto, puede definir una alerta que se busque en todas las bases de datos, por ejemplo, en lugar de definir una por cada base de datos. También puede escribir una alerta que use una consulta compuesta para varios tipos de recursos. Las consultas solo se ven limitadas por los datos disponibles en el área de trabajo.

Log Analytics para SQL Database se cobra en función del volumen de datos del área de trabajo. En este tutorial ha creado un área de trabajo disponible, que está limitado a 500 MB al día. Una vez alcanzado ese límite, ya no se agregan más datos al área de trabajo.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Instalar y configurar Log Analytics (OMS)
> * Usar Log Analytics para supervisar grupos y bases de datos

[Tutorial sobre el análisis de inquilinos](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)

