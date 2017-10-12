---
title: "Ejemplo de aplicación multiinquilino de Azure SQL Database: SaaS de Wingtip | Microsoft Docs"
description: "Obtenga información mediante el uso de una aplicación multiinquilino que usa Azure SQL Database, el ejemplo de SaaS de Wingtip"
keywords: tutorial de SQL Database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introducción al ejemplo de una aplicación SaaS multiinquilino de SQL Database

*SaaS Wingtip* es una aplicación multiinquilino de ejemplo que muestra las ventajas únicas de SQL Database. La aplicación utiliza un patrón de aplicación SaaS de base de datos por inquilino para dar servicio a varios inquilinos. La aplicación está diseñada para presentar las características de Azure SQL Database que permiten escenarios de SaaS, incluidos los diversos patrones de diseño y administración de SaaS. Para ponerse rápidamente en marcha, la aplicación SaaS Wingtip se implementa en menos de cinco minutos.

El código fuente y los scripts de administración de la aplicación están disponibles en el repositorio [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) de GitHub. Para ejecutar los scripts, [descargue la carpeta Learning Modules](#download-and-unblock-the-wingtip-saas-scripts) en el equipo local.

## <a name="sql-database-wingtip-saas-tutorials"></a>Tutoriales de SaaS de Wingtip de SQL Database

Después de implementar la aplicación, explore los tutoriales siguientes que se basan en la implementación inicial. En estos tutoriales se exploran patrones comunes de SaaS que aprovechan las ventajas de las características integradas de SQL Database, SQL Data Warehouse y otros servicios de Azure. Cada tutorial incluye scripts de PowerShell, con explicaciones detalladas que simplifican considerablemente entenderlos, y la implementación de los mismos patrones de administración de SaaS en sus aplicaciones.


| Tutorial | Descripción |
|:--|:--|
|[Implementar y explorar la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md)| **COMIENCE AQUÍ.** Implemente y explore la aplicación SaaS de Wingtip en la suscripción de Azure. |
|[Aprovisionamiento y registro de inquilinos en el catálogo](sql-database-saas-tutorial-provision-and-catalog.md)| Obtenga información sobre cómo se conecta la aplicación a los inquilinos mediante un base de datos de catálogo, y cómo asigna el catálogo los inquilinos a sus datos. |
|[Supervisión y administración del rendimiento](sql-database-saas-tutorial-performance-monitoring.md)| Obtenga información sobre cómo usar las características de supervisión de SQL Database y cómo establecer alertas cuando se superan los umbrales de rendimiento. |
|[Supervisión con Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Obtenga información sobre el uso de [Log Analytics](../log-analytics/log-analytics-overview.md) para supervisar grandes cantidades de recursos en varios grupos. |
|[Restauración de un solo inquilino](sql-database-saas-tutorial-restore-single-tenant.md)| Obtenga información sobre cómo restaurar una base de datos de inquilino a un momento anterior en el tiempo. También se incluyen los pasos para restaurar una base de datos paralela, dejando la base de datos existente de inquilino en línea. |
|[Administración de esquemas de inquilino](sql-database-saas-tutorial-schema-management.md)| Obtenga información sobre cómo actualizar el esquema, y los datos de referencia, entre todos los inquilinos de SaaS Wingtip. |
|[Ejecución de análisis ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Cree una base de datos de análisis ad hoc y ejecute consultas distribuidas en tiempo real en todos los inquilinos.  |
|[Ejecución de análisis de inquilino](sql-database-saas-tutorial-tenant-analytics.md) | Extraiga datos de inquilino a una base de datos de análisis o un almacén de datos para ejecutar consultas analíticas sin conexión. |



## <a name="application-architecture"></a>Arquitectura de la aplicación

La aplicación SaaS Wingtip usa el modelo de base de datos por inquilino y grupos elásticos de SQL para maximizar la eficacia. Para aprovisionar y asignar inquilinos a sus datos, se usa una base de datos de catálogo. En la aplicación SaaS Wingtip principal se usa un grupo con tres inquilinos de ejemplo, además de una base de datos de catálogo. Al completar muchos de los tutoriales de SaaS de Wingtip se agregan complementos a la implementación inicial, mediante la introducción de bases de datos analíticas, administración de esquemas entre bases de datos, etc.


![Arquitectura de SaaS Wingtip](media/sql-database-wtp-overview/app-architecture.png)


Al realizar los tutoriales y trabajar con la aplicación, es importante centrarse en la relación de los patrones de SaaS con la capa de datos. En otras palabras, céntrese en la capa de datos y no analice demasiado la propia aplicación. Entender la implementación de estos patrones de SaaS es clave para implementarlos en las aplicaciones y considerar cualquier modificación necesaria para sus requisitos empresariales específicos.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Descargar y desbloquear los scripts SaaS de Wingtip

Es posible que Windows bloquee el contenido ejecutable (scripts, archivos DLL) cuando se descarguen y extraigan archivos ZIP desde un origen externo. Al extraer los scripts de un archivo ZIP, ***siga los pasos siguientes para desbloquear el archivo .zip antes de extraerlo***. Esto garantiza que se puedan ejecutar los scripts.

1. Vaya al [repositorio de SaaS de Wingtip en GitHub](https://github.com/Microsoft/WingtipSaaS).
1. Haga clic en **Clone or download** (Clonar o descargar).
1. Haga clic en **Download ZIP** (Descargar archivos ZIP) y guarde el archivo.
1. Haga clic con el botón derecho en el archivo **WingtipSaaS-master.zip** y seleccione **Propiedades**.
1. En la pestaña **General**, seleccione **Desbloquear**.
1. Haga clic en **Aceptar**.
1. Extraiga los archivos.

Los scripts se encuentran en la carpeta *..\\WingtipSaaS-master\\Learning Modules*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Trabajar con los scripts de PowerShell de SaaS Wingtip

Para sacar el máximo partido del ejemplo debe profundizar en los scripts proporcionados. Use puntos de interrupción y recorra los scripts, examinando los detalles de cómo se implementan los distintos patrones de SaaS. Para recorrer con facilidad los scripts proporcionados y los módulos para entenderlos mejor, se recomienda usar [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Actualizar el archivo de configuración para la implementación

Edite el archivo **UserConfig.psm1** con el valor de usuario y grupo de recursos que se estableció durante la implementación:

1. Abra *PowerShell ISE* y cargue ...\\Learning Modules\\*UserConfig.psm1* 
1. Actualice *ResourceGroupName* y *Name* con los valores específicos para la implementación (solo en las líneas 10 y 11).
1. Guarde los cambios.

Al establecer estos valores aquí simplemente evita tener que actualizar estos valores específicos de la implementación en todos los scripts.

### <a name="execute-scripts-by-pressing-f5"></a>Ejecución de scripts presionando F5

Varios scripts usan *$PSScriptRoot* para la navegación por las carpetas y *$PSScriptRoot* solo se evalúa cuando se ejecutan los scripts presionando **F5**.  Resaltar y ejecutar una selección (**F8**) puede dar lugar a errores, por tanto, presione **F5** al ejecutar los scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Siga paso a paso los scripts para examinar la implementación

La mejor manera de comprender los scripts es recorrerlos para ver lo que hacen. Consulte los scripts **Demo-** incluidos que presentan un flujo de trabajo general fácil de seguir. Los scripts **Demo-** muestran los pasos necesarios para realizar cada tarea, por tanto, establezca puntos de interrupción y profundice en las llamadas individuales para ver los detalles de implementación para los distintos patrones de SaaS.

Sugerencias para explorar y recorrer los scripts de PowerShell:

* Abra los scripts **Demo-** en PowerShell ISE.
* Ejecute o continúe con **F5** (no se recomienda el uso de **F8** porque *$PSScriptRoot* no se evalúa cuando se ejecutan las selecciones de un script).
* Para colocar puntos de interrupción, haga clic o seleccione una línea y presione **F9**.
* Salte una llamada de función o script con **F10**.
* Vaya a una llamada de función o script con **F11**.
* Salga de la llamada de función o script actual con **MAYÚS + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Exploración del esquema de base de datos y ejecución de consultas SQL con SSMS

Use [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) para conectarse y examinar las bases de datos y los servidores de la aplicación.

La implementación inicialmente tiene dos servidores de SQL Database para conectarse a los servidores *tenants1-&lt;Usuario&gt;* y *catalog-&lt;Usuario&gt;*. Para garantizar una conexión de demostración correcta, ambos servidores tienen una [regla de firewall](sql-database-firewall-configure.md) que permite todas las direcciones IP.


1. Abra *SSMS* y conéctese al servidor *tenants1-&lt;Usuario&gt;.database.windows.net*.
1. Haga clic en **Conectar** > **Motor de base de datos...**:

   ![Servidor de catálogo](media/sql-database-wtp-overview/connect.png)

1. Las credenciales para la demo son: inicio de sesión = *developer*, contraseña =*P@ssword1*.

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. Repita los pasos 2-3 y conéctese al servidor *catalog-&lt;Usuario&gt;.database.windows.net*.

Después de conectarse correctamente debería ver ambos servidores. La lista de bases de datos podría ser diferente, dependiendo de los inquilinos que haya aprovisionado:

![Explorador de objetos](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Pasos siguientes

[Implementar la aplicación SaaS de Wingtip](sql-database-saas-tutorial.md)