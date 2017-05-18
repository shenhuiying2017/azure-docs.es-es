---
title: "SaaS en una caja (ejemplo de aplicación SaaS que usa Azure SQL Database) | Microsoft Docs"
description: "Compilación de aplicaciones SaaS con SQL Database"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Introducción a la aplicación SaaS de ejemplo Wingtip Tickets Platform (WTP)

La aplicación SaaS Wingtip Tickets Platform (WTP) es una aplicación multiinquilino de ejemplo que muestra las ventajas únicas de SQL Database. La aplicación utiliza un patrón de aplicación SaaS de base de datos por inquilino para dar servicio a varios inquilinos. La aplicación WTP está diseñada para presentar las características de Azure SQL Database que permiten escenarios de SaaS, incluidos los patrones de diseño y administración de SaaS. Para ponerse rápidamente en marcha, [la aplicación WTP se implementa en menos de cinco minutos](sql-database-saas-tutorial.md).

Después de implementar la aplicación WTP, explore la [colección de tutoriales](#sql-database-saas-tutorials) que se basan en la implementación inicial. Cada tutorial se centra en las tareas habituales que se implementan en las aplicaciones SaaS. Las tareas se implementan de acuerdo con los patrones de SaaS que aprovechan las ventajas de las características integradas de SQL Database. Entre los patrones descritos se incluyen aprovisionar nuevos inquilinos, restaurar bases de datos de inquilino, ejecutar consultas distribuidas entre todos los inquilinos e implementar cambios de esquema entre todas las bases de datos de inquilino. Cada tutorial incluye scripts reutilizables, con explicaciones detalladas que simplifican considerablemente entenderlos, y la implementación de los mismos patrones de administración de SaaS en sus aplicaciones.

Si bien la aplicación WTP está prácticamente completa y es atractiva como aplicación de ejemplo, es importante centrarse en los principales patrones de SaaS, ya que afectan a la capa de datos. En otras palabras, céntrese en la capa de datos y no analice demasiado la propia aplicación. Entender la implementación de los principales patrones de SaaS es clave para implementarlos en las aplicaciones y considerar cualquier modificación necesaria para sus requisitos empresariales específicos.



## <a name="application-architecture"></a>Arquitectura de la aplicación

La aplicación WTP usa el modelo de base de datos por inquilino y utiliza grupos elásticos de SQL para maximizar la eficiencia.
Uso de un catálogo de inquilinos para aprovisionar la administración y la conectividad.
Aplicación integrada, grupo y supervisión y alertas (OMS) de la base de datos.
Esquema entre inquilinos y administración de datos de referencia (trabajos de base de datos elástica).
Consulta entre inquilinos, análisis operativos (consulta elástica).
Uso de datos distribuidos geográficamente para un mayor alcance.
Recuperación de único inquilino para continuidad del negocio (PITR). Recuperación ante desastres a escala (restauración geográfica, replicación geográfica, autorecuperación ante desastres). Administración de autoservicio de inquilinos (mediante las API de administración). PITR para recuperación de problemas autoinflingidos.

La aplicación Wingtip principal utiliza un grupo con tres de inquilinos de ejemplo, además de una base de datos de catálogos.

![Arquitectura WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Tutoriales de SaaS de WTP de SQL Database

Los siguientes tutoriales se basan en la implementación inicial de la [aplicación de SaaS de ejemplo Wingtip Tickets Platform](sql-database-saas-tutorial.md):

| Ámbito | Descripción | Ubicación de los scripts |
|:--|:--|:--|
|[Tutorial de aprovisionamiento y en inclusión en el catálogo de inquilinos](sql-database-saas-tutorial-provision-and-catalog.md)| Aprovisionamiento y registro de nuevos inquilinos en el catálogo | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Tutorial de supervisión y administración del rendimiento](sql-database-saas-tutorial-performance-monitoring.md)| Supervisa y administra el rendimiento de la base de datos y los grupos | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Tutorial sobre la restauración de un único inquilino](sql-database-saas-tutorial-restore-single-tenant.md)| Restaura bases de datos de inquilino | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Tutorial de administración del esquema de inquilinos](sql-database-saas-tutorial-schema-management.md)| Ejecuta consultas en todos los inquilinos  | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Tutorial de ejecución de análisis ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Crea una base de datos de análisis ad hoc y ejecuta consultas en todos los inquilinos  | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Tutorial de administración con Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Configura y explora Log Analytics | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Tutorial de ejecución de análisis de inquilinos](sql-database-saas-tutorial-tenant-analytics.md) | Configura y ejecuta consultas de análisis de inquilinos | [Scripts en github](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="working-with-the-wtp-powershell-scripts"></a>Uso de los scripts de PowerShell de WTP

Las ventajas de trabajar con la aplicación WTP se derivan de profundizar en los scripts proporcionados y examinar cómo se implementan los distintos patrones de SaaS.

Para ver los módulos y los scripts proporcionados y facilitar ejecutarlos paso a paso para una mejor comprensión, use [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Dado que la mayoría de los scripts con el prefijo *Demo-* contienen variables que se pueden modificar antes de la ejecución, el uso de PowerShell ISE simplifica el trabajo con ellos.

Para cada implementación de la aplicación WTP, hay un archivo **UserConfig.psm1** que contiene dos parámetros para configurar el grupo de recursos y los valores de nombre de usuario que ha definido durante la implementación. Una vez completada la implementación, modifique la configuración del módulo **UserConfig.psm1** mediante los parámetros _ResourceGroupName_ y _Name_. Otros scripts usan estos valores para ejecutarse correctamente, por lo que se recomienda establecerlos cuando se haya completado la implementación.



### <a name="execute-scripts-by-pressing-f5"></a>Ejecución de scripts presionando F5

Varios scripts utilizan *$PSScriptRoot* permitir la navegación por las carpetas, y esta variable solo se evalúa cuando se ejecuta el script presionando **F5**.  Resaltar y ejecutar una selección (**F8**) puede dar lugar a errores, por tanto, presione **F5** al ejecutar scripts de WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Siga paso a paso los scripts para examinar la implementación

El valor real para explorar los scripts proviene de seguirlos paso a paso para ver lo que hacen. compruebe los scripts _Demo-_ de primer nivel que proporcionan un flujo de trabajo de alto nivel fácil de leer y que muestra los pasos necesarios para realizar cada tarea. Profundice en las llamadas individuales para ver los detalles de implementación para los distintos patrones de SaaS.

Sugerencias para trabajar con [scripts de PowerShell y depurarlos](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise):

* Abra y configure los scripts demo- en PowerShell ISE.
* Ejecútelos o continúe con **F5**. No se recomienda el uso de **F8** porque *$PSScriptRoot* no se evalúa cuando se ejecutan las selecciones de un script.
* Para colocar puntos de interrupción, haga clic o seleccione una línea y presione **F9**.
* Salte una llamada de función o script con **F10**.
* Vaya a una llamada de función o script con **F11**.
* Salga de la llamada de función o script actual con **MAYÚS + F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Exploración del esquema de base de datos y ejecución de consultas SQL con SSMS

Use [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para conectarse y examinar las bases de datos y los servidores de WTP.

La aplicación de ejemplo WTP inicialmente tiene dos servidores de SQL Database para conectarse a los servidores *tenants1* y *catalog*:


1. Abra *SSMS* y conéctese al servidor *tenants1-&lt;Usuario&gt;.database.windows.net*.
2. Haga clic en **Conectar** > **Motor de base de datos...**:

   ![Servidor de catálogo](media/sql-database-wtp-overview/connect.png)

1. Las credenciales para la demo son: inicio de sesión = *developer*, contraseña =*P@ssword1*.

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita los pasos 2-3 y conéctese al servidor *catalog-&lt;Usuario&gt;.database.windows.net*.

Después de conectarse correctamente debería ver ambos servidores. Puede ver más o menos bases de datos dependiendo de cuántos inquilinos aprovisione:

![Explorador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Pasos siguientes

[Implementación de la aplicación SaaS Wingtip Tickets Platform](sql-database-saas-tutorial.md)
