---
title: "Ejecución de consultas de análisis ad-hoc entre varias bases de datos de Azure SQL| Microsoft Docs"
description: "Ejecute consultas de análisis ad hoc en varias bases de datos SQL en la aplicación multiinquilino SaaS de Wingtip."
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
ms.date: 05/22/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 31be50ca3f64cc183e516f1b0f06f5a4265f6103
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>Ejecución de consultas de análisis ad hoc en todos los inquilinos SaaS de Wingtip

En este tutorial, se crea una base de datos de análisis ad hoc y se ejecutan varias consultas en todos los inquilinos. Estas consultas pueden extraer información incluida en los datos operativos diarios de la aplicación WTP.

Para ejecutar consultas de análisis ad hoc (en varios inquilinos), la aplicación SaaS de Wingtip usa la [consulta elástica](sql-database-elastic-query-overview.md) junto con una base de datos de análisis.


En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Implementar una base de datos de análisis ad hoc
> * Ejecutar consultas distribuidas en todas las bases de datos de inquilinos



Para completar este tutorial, asegúrese de cumplir los siguientes requisitos previos:

* Se implementa la aplicación SaaS de Wingtip. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).


## <a name="ad-hoc-analytics-pattern"></a>Patrón de análisis ad hoc

Una de las grandes oportunidades que ofrecen las aplicaciones SaaS es el uso de la gran cantidad de datos de inquilino almacenados centralmente en la nube. Use estos datos para obtener información sobre la operación y el uso de las aplicaciones, sus inquilinos, sus usuarios, sus preferencias y comportamientos, etc. Esta información puede guiarle en el desarrollo de características, mejoras de facilidad de uso y otras inversiones en sus aplicaciones y servicios.

Es fácil obtener acceso a estos datos en una sola base de datos multiinquilino, pero no es tan fácil cuando se distribuyen a escala en miles de bases de datos. Un enfoque es usar la consulta elástica, que permite consultas ad hoc en un conjunto distribuido de bases de datos con un esquema común. La consulta elástica usa una única base de datos *principal* en la que se definen tablas externas que reflejan tablas o vistas en las bases de datos (de inquilino) distribuidas. Las consultas que se envían a esta base de datos principal se compilan para producir un plan de consulta distribuido, con partes de la consulta aplicadas en las bases de datos de inquilino según sea necesario. La consulta elástica usa el mapa de particiones de la base de datos del catálogo para proporcionar la ubicación de las bases de datos de inquilino. La configuración y la consulta son sencillas y utilizan [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) estándar; además, admiten consultas ad hoc desde herramientas como Power BI y Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obtener scripts de la aplicación Wingtip

Los scripts SaaS de Wingtip y el código fuente de la aplicación están disponibles en el repositorio de GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Pasos para descargar los scripts SaaS de Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).


## <a name="explore-the-global-views-in-the-tenant-databases"></a>Exploración de las vistas globales en las bases de datos de inquilino

La aplicación SaaS de Wingtip se compila siguiendo un modelo de inquilino por cada base de datos, por lo que el esquema de base de datos de inquilino se define desde la perspectiva del inquilino único. Existe información específica del inquilino en una tabla, *Venue*, que siempre tiene una sola fila y, además, se ha diseñado como montón, sin clave principal.  No es necesario que otras tablas del esquema estén relacionadas con la tabla *Venue*, ya que, en el uso normal, no hay nunca duda de a qué inquilino pertenecen los datos.  Sin embargo, cuando se consulta en todas las bases de datos, la correlación de datos de tablas en la base de datos con un inquilino específico pasa a ser significativa. Para simplificar esto, se agrega a la base de datos de inquilino un conjunto de vistas que proporciona una vista "global" de cada inquilino. Estas vistas globales proyectan un id. de inquilino en cada tabla que se va a consultar a escala global. Esto facilita la identificación de los datos de cada inquilino. Para su comodidad, estas vistas ya se han creado en todas las bases de datos de inquilino (así como en la base de datos maestra), por lo que estas vistas están disponibles cuando se aprovisionan nuevos inquilinos.

1. Abra SSMS y [conéctese al servidor tenants1-&lt;USUARIO&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expanda **Bases de datos**, haga clic con el botón derecho en **contosoconcerthall** y seleccione **Nueva consulta**.
1. Ejecute las consultas siguientes para explorar las vistas globales:

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   -- In the sample database we calculated an integer id from a hash of the Venue name,
   -- but any approach could be used to introduce a unique value.
   -- This is similar to how we create the tenant key in the catalog,
   -- but there is no requirement that the catalog key and this id be the same.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Para examinar una vista y ver cómo se crea:

1. En **Explorador de objetos**, expanda **contosoconcerthall** > **Vistas**:

   ![vistas](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. Haga clic con el botón derecho en **dbo.Venues**.
1. Seleccione **Incluir vista como** > **CREATE To** > **Nueva ventana del Editor de consultas**.

Haga esto para cualquier *vista* para ver cómo se crea.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Implementar la base de datos usada para las consultas de análisis ad hoc

En este ejercicio se implementa la base de datos *adhocanalytics*. Esta base de datos contiene el esquema utilizado para realizar consultas en todas las bases de datos de inquilino. La base de datos se implementa en el servidor de catálogo existente, que es el servidor que contiene todas las bases de datos relacionadas con la administración.

1. Abra ...\\Learning Modules\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* en *PowerShell ISE* y establezca los valores siguientes:
   * **$DemoScenario** = 2, **Implementar una base de datos de análisis ad hoc**.

1. Baje en el script hasta el script SQL que contiene el esquema de la base de datos.  Revise el script y tenga en cuenta lo siguiente:

   1. La consulta elástica usa una credencial de ámbito de base de datos para acceder a cada una de las bases de datos de inquilino. Esta credencial debe estar disponible en todas las bases de datos y normalmente se le deberían conceder los derechos mínimos necesarios para permitir estas consultas ad hoc.
   1. El origen de datos externo, que se define para usar el mapa de particiones de inquilino en la base de datos de catálogo.  Al utilizar esto como origen de datos externo, las consultas se distribuirán a todas las bases de datos registradas en el catálogo en el momento en que se emita la consulta.
   1. Las tablas externas que hacen referencia a las vistas globales descritas en la sección anterior.
   1. La tabla local *VenueTypes* que se crea y rellena.  Como esta tabla de datos de referencia es común a todas las bases de datos de inquilino, se puede representar aquí como tabla local, lo que, para algunas consultas, puede reducir la cantidad de datos que se mueven entre las bases de datos de inquilino y la base de datos *adhocanalytics*.

1. Presione **F5** para ejecutar el script y crear la base de datos *adhocanalytics*.

   No pasa nada si hace caso omiso de las advertencias que indican que *El servidor RPC no está disponible*.


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

Ahora pruebe el [tutorial de análisis de inquilinos](sql-database-saas-tutorial-tenant-analytics.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación SaaS de Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Consulta elástica](sql-database-elastic-query-overview.md)

