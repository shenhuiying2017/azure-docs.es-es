---
title: "Ejemplo de aplicación multiinquilino de Azure SQL Database: SaaS de Wingtip | Microsoft Docs"
description: "Obtenga información mediante el uso de una aplicación multiinquilino que usa Azure SQL Database, el ejemplo de SaaS de Wingtip"
keywords: tutorial de SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 2871d2b1208013808958e8a5b0c62fce31af86ec
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introducción a una aplicación SaaS multiinquilino que usa el patrón de base de datos por inquilino con SQL Database

La aplicación *Wingtip SaaS* es una aplicación multiinquilino de ejemplo. La aplicación utiliza el patrón de aplicación SaaS de base de datos por inquilino para dar servicio a varios inquilinos. La aplicación presenta las características de Azure SQL Database que permiten escenarios de SaaS, con los diversos patrones de diseño y administración de SaaS. Para ponerse rápidamente en marcha, la aplicación SaaS Wingtip se implementa en menos de cinco minutos.

El código fuente y los scripts de administración de la aplicación están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Antes de comenzar, consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts de administración de Wingtip Tickets.

## <a name="application-architecture"></a>Arquitectura de la aplicación

La aplicación SaaS Wingtip usa el modelo de base de datos por inquilino y grupos elásticos de SQL para maximizar la eficacia. Para aprovisionar y asignar inquilinos a sus datos, se usa una base de datos de catálogo. En la aplicación SaaS Wingtip principal se usa un grupo con tres inquilinos de ejemplo, además de una base de datos de catálogo. Al completar muchos de los tutoriales de SaaS de Wingtip se agregan complementos a la implementación inicial, mediante la introducción de bases de datos analíticas, administración de esquemas entre bases de datos, etc.


![Arquitectura de SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Al realizar los tutoriales y trabajar con la aplicación, es importante centrarse en la relación de los patrones de SaaS con la capa de datos. En otras palabras, céntrese en la capa de datos y no analice demasiado la propia aplicación. Entender la implementación de estos patrones de SaaS es clave para implementarlos en las aplicaciones y considerar cualquier modificación necesaria para sus requisitos empresariales específicos.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriales de SaaS de Wingtip de SQL Database

Después de implementar la aplicación, explore los tutoriales siguientes que se basan en la implementación inicial. En estos tutoriales se exploran patrones comunes de SaaS que aprovechan las ventajas de las características integradas de SQL Database, SQL Data Warehouse y otros servicios de Azure. Cada tutorial incluye scripts de PowerShell, con explicaciones detalladas que simplifican considerablemente entenderlos, y la implementación de los mismos patrones de administración de SaaS en sus aplicaciones.


| Tutorial | DESCRIPCIÓN |
|:--|:--|
| [Guía y sugerencias para el ejemplo de una aplicación SaaS multiinquilino de Azure SQL Database](saas-tenancy-wingtip-app-guidance-tips.md) | **COMIENCE AQUÍ.** Descargue y ejecute scripts de PowerShell para preparar las partes de la aplicación. |
|[Implementar y explorar la aplicación SaaS de Wingtip](saas-dbpertenant-get-started-deploy.md)|  Implemente y explore la aplicación SaaS de Wingtip en la suscripción de Azure. |
|[Aprovisionamiento y registro de inquilinos en el catálogo](saas-dbpertenant-provision-and-catalog.md)| Obtenga información sobre cómo se conecta la aplicación a los inquilinos mediante un base de datos de catálogo, y cómo asigna el catálogo los inquilinos a sus datos. |
|[Supervisión y administración del rendimiento](saas-dbpertenant-performance-monitoring.md)| Obtenga información sobre cómo usar las características de supervisión de SQL Database y cómo establecer alertas cuando se superan los umbrales de rendimiento. |
|[Supervisión con Log Analytics (OMS)](saas-dbpertenant-log-analytics.md) | Obtenga información sobre el uso de [Log Analytics](../log-analytics/log-analytics-overview.md) para supervisar grandes cantidades de recursos en varios grupos. |
|[Restauración de un solo inquilino](saas-dbpertenant-restore-single-tenant.md)| Obtenga información sobre cómo restaurar una base de datos de inquilino a un momento anterior en el tiempo. También se incluyen los pasos para restaurar una base de datos paralela, dejando la base de datos existente de inquilino en línea. |
|[Administración de un esquema de base de datos de inquilino](saas-tenancy-schema-management.md)| Obtenga información sobre cómo actualizar el esquema, y los datos de referencia, entre todas las bases de datos de inquilinos. |
|[Ejecutar consultas distribuidas entre inquilinos](saas-tenancy-cross-tenant-reporting.md) | Cree una base de datos de análisis ad hoc y ejecute consultas distribuidas en tiempo real en todos los inquilinos.  |
|[Ejecutar análisis en los datos extraídos de los inquilinos](saas-tenancy-tenant-analytics.md) | Extraiga datos de los inquilinos a una base de datos de análisis o un almacén de datos para consultas analíticas sin conexión. |


## <a name="next-steps"></a>pasos siguientes

- [Instrucciones generales y consejos al implementar y usar el ejemplo de aplicación SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)

- [Implementar la aplicación SaaS de Wingtip](saas-dbpertenant-get-started-deploy.md)
