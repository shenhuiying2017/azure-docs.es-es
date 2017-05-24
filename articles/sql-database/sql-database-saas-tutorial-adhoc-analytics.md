---
title: "Ejecución de consultas de análisis ad-hoc entre varias bases de datos de Azure SQL| Microsoft Docs"
description: "Ejecución de consultas de análisis ad-hoc entre varias bases de datos en una aplicación multiinquilino"
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Ejecutar consultas de análisis ad hoc en todos los inquilinos de WTP

En este tutorial, se crea una base de datos de análisis ad hoc y se ejecutan varias consultas en todos los inquilinos. Estas consultas pueden extraer información incluida en los datos operativos diarios de la aplicación WTP.

Para ejecutar consultas de análisis ad hoc (en varios inquilinos), la aplicación WTP usa [consulta elástica](sql-database-elastic-query-overview.md) junto con una base de datos de análisis.


En este tutorial, obtendrá información sobre cómo:

> [!div class="checklist"]

> * Implementar una base de datos de análisis ad hoc
> * Ejecutar consultas distribuidas en todas las bases de datos de inquilinos



Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* La aplicación WTP está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [Implementación y exploración de la aplicación SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para obtener más información, vea [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).


## <a name="ad-hoc-analytics-pattern"></a>Patrón de análisis ad hoc

Una de las grandes oportunidades que ofrecen las aplicaciones SaaS es el uso de la gran cantidad de datos de inquilino que ha almacenado centralmente en la nube. Use estos datos para obtener información sobre la operación y el uso de las aplicaciones, sus inquilinos, sus usuarios y sus preferencias y comportamientos. La información que encuentre puede guiarle en el desarrollo de características, mejoras de facilidad de uso y otras inversiones en sus aplicaciones y servicios.

Es fácil obtener acceso a estos datos en una sola base de datos multiinquilino, pero no es tan fácil cuando se distribuyen a escala en miles de bases de datos. Un enfoque es usar la consulta elástica, que permite consultas ad hoc en un conjunto distribuido de bases de datos con un esquema común. La consulta elástica usa una base de datos *principal* única en la que se definen las tablas externas que reflejan tablas en las bases de datos distribuidas (inquilino). Las consultas que se envían a esta base de datos principal se compilan para producir un plan de consulta distribuido, con partes de la consulta aplicadas en las bases de datos de inquilino según sea necesario. La consulta elástica usa el mapa de particiones de la base de datos del catálogo para proporcionar la ubicación de las bases de datos de inquilino. La configuración y la consulta son sencillos con T-SQL normal, y admiten consultas ad hoc de herramientas como Power BI y Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obtener los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Implementar la base de datos usada para las consultas de análisis ad hoc

En este ejercicio se implementa la base de datos de análisis ad hoc que contiene el esquema que se ha usado para emitir consultas ad hoc que abarcan todas las bases de datos de inquilino.

1. Abra ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* en *PowerShell ISE* y establezca los valores siguientes:
   * **$DemoScenario** = 2, **Implementar una base de datos de análisis ad hoc**.

1. Presione **F5** para ejecutar el script y cree una nueva SQL Database para el análisis ad hoc y agréguela al catálogo de WTP. Las tablas TicketPurchases, Tickets y Venues se agregan como tablas externas que pueden consultarse.
   No pasa nada si encuentra advertencias aquí sobre *El servidor RPC no está disponible*.


Ahora tiene una base de datos *adhocanalytics*, que puede usarse para ejecutar consultas distribuidas y recopilar información en todos los inquilinos.

## <a name="run-ad-hoc-analytics-queries"></a>Ejecutar consultas de análisis ad hoc

En este ejercicio se ejecutan consultas de análisis ad hoc para revelar información de inquilino desde la aplicación WTP.

1. Abra ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* en SSMS.
1. Asegúrese de que está conectado a la base de datos **adhocanalytics**
1. Seleccione la consulta individual que quiere ejecutar y presione **F5**:

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información sobre cómo:

> [!div class="checklist"]

> * Implementar una base de datos de análisis ad hoc
> * Ejecutar consultas distribuidas en todas las bases de datos de inquilinos

[Tutorial de Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación de la aplicación inicial Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)

