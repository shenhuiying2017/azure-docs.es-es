---
title: "Administración de esquema para varios inquilinos (ejemplo de aplicación SaaS que usa Azure SQL Database) | Microsoft Docs"
description: "Administración de esquema para varios inquilinos en una aplicación SaaS que utiliza Azure SQL Database"
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
ms.openlocfilehash: 226cda254934fae30410e54148d5cc527e1c7881
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>Administración de esquema para varios inquilinos en la aplicación SaaS WTP

El tutorial Introducción a la aplicación WTP muestra cómo la aplicación WTP puede aprovisionar una base de datos de inquilino con su esquema inicial y registrarla en el catálogo. Como cualquier otra aplicación, la aplicación WTP evolucionará con el tiempo y, en ocasiones, requerirá cambios en la base de datos. Estos cambios pueden incluir un esquema nuevo o modificado, datos de referencia nuevos o modificados y tareas de mantenimiento de la base de datos rutinarias para garantizar un rendimiento óptimo de la aplicación. Con una aplicación SaaS, estos cambios deben implementarse de manera coordinada a lo largo de una línea potencialmente masiva de bases de datos de inquilino. Los cambios también deben incorporarse en el proceso de aprovisionamiento para futuras bases de datos de inquilino.

Este tutorial explora dos escenarios: la implementación de actualizaciones de datos de referencia para todos los inquilinos y el reajuste de un índice en la tabla que contiene los datos de referencia. La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) se usa para ejecutar estas operaciones en todos los inquilinos y una base de datos de inquilino *golden* se utiliza como plantilla para nuevas bases de datos.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Crear una cuenta de trabajo para consultar en varios inquilinos
> * Actualizar datos en todas las bases de datos de inquilino
> * Crear un índice en una tabla en todas las bases de datos de inquilino


Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* La aplicación WTP está implementada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [Implementación y exploración de la aplicación SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La versión más reciente de SQL Server Management Studio (SSMS) está instalada. [Descarga e instalación de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*En este tutorial se usan características del servicio SQL Database que se encuentran en una versión preliminar limitada (trabajos de Elastic Database). Si desea seguir este tutorial, envíe su identificador de suscripción a SaaSFeedback@microsoft.com con el asunto Elastic Jobs Preview. Después de recibir la confirmación de que se ha habilitado su suscripción, [descargue e instale los cmdlets de trabajos de versión preliminar más recientes](https://github.com/jaredmoo/azure-powershell/releases). Al tratarse de una versión preliminar limitada, debe ponerse en contacto con SaaSFeedback@microsoft.com si necesita asistencia.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introducción a los patrones de administración de esquema de SaaS

El patrón de inquilino único por base de datos de SaaS se aprovecha de varias maneras del aislamiento de los datos resultante, pero al mismo tiempo introduce la complejidad adicional de mantener y administrar muchas bases de datos. La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) facilita la administración de la capa de datos SQL. Los trabajos permiten ejecutar tareas (scripts T-SQL) en un grupo de bases de datos de manera segura y confiable independientemente de las interacciones o entradas del usuario. Este método puede utilizarse para implementar cambios en el esquema y los datos de referencia comunes en todos los inquilinos de una aplicación. Trabajos elásticos también se puede utilizar para mantener una copia *golden* de la base de datos utilizada para crear nuevos inquilinos, garantizando así que siempre tenga el esquema y los datos de referencia más recientes.

![pantalla](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Versión preliminar limitada de Trabajos elásticos

Hay una nueva versión de Trabajos elásticos que ahora es una característica integrada de Azure SQL Database (no requiere servicios ni componentes adicionales). Esta nueva versión de Trabajos elásticos ofrece actualmente una versión preliminar limitada. Esta versión preliminar limitada admite PowerShell para crear cuentas de trabajo y T-SQL para crear y administrar trabajos.

> [!NOTE]
> *En este tutorial se usan características del servicio SQL Database que se encuentran en una versión preliminar limitada (trabajos de Elastic Database). Si desea seguir este tutorial, envíe su identificador de suscripción a SaaSFeedback@microsoft.com con el asunto Elastic Jobs Preview. Después de recibir la confirmación de que se ha habilitado su suscripción, [descargue e instale los cmdlets de trabajos de versión preliminar más recientes](https://github.com/jaredmoo/azure-powershell/releases). Al tratarse de una versión preliminar limitada, debe ponerse en contacto con SaaSFeedback@microsoft.com si necesita asistencia.*

## <a name="get-the-wingtip-application-scripts"></a>Obtención de los scripts de la aplicación Wingtip

Los scripts de Wingtip Tickets y el código fuente de la aplicación están disponibles en el repositorio de github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Los archivos de los scripts se encuentran en la [carpeta Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Descargue la carpeta **Learning Modules** en el equipo local, conservando su estructura de carpetas.

## <a name="create-a-job-account-database-and-new-job-account"></a>Creación de una base de datos de cuentas de trabajo y una nueva cuenta de trabajo

Para este tutorial, es necesario usar PowerShell para crear la base de datos de cuentas de trabajo y una nueva cuenta de trabajo. Al igual que MSDB y el Agente SQL, Trabajos Elásticos utiliza una instancia de Azure SQL Database para almacenar definiciones de trabajos, estados de trabajo y el historial. Una vez creada la cuenta de trabajo, puede crear y supervisar trabajos inmediatamente.

1. Abra …\\Learning Modules\\Schema Management\\*Demo-SchemaManagement.ps1* en **PowerShell ISE**.
1. Presione **F5** para ejecutar el script.

El script *Demo-SchemaManagement.ps1* llama al script *Deploy-SchemaManagement.ps1* para crear una base de datos *S2* llamada **jobaccount** en el servidor de catálogo. A continuación, crea la cuenta de trabajo, transfiriendo la base de datos jobaccount como un parámetro a la llamada de creación de la cuenta de trabajo.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creación de un trabajo para implementar nuevos datos de referencia en todos los inquilinos

Cada base de datos de inquilinos incluye un conjunto de tipos de ubicación que definen la clase de eventos que se celebran en una ubicación. En este ejercicio, se implementa una actualización en todas las bases de datos de inquilinos para agregar dos tipos de ubicación adicionales: *Motorcycle Racing* (motociclismo) y *Swimming Club* (club de natación). Estos tipos de ubicación se corresponden con la imagen de fondo que se ve en la aplicación de eventos de inquilino.

Haga clic en el menú desplegable Tipo de lugar de celebración y asegúrese de que solo haya 10 opciones de tipo de ubicación disponibles y, en concreto, que "Motorcycle Racing" y "Swimming Club" no se encuentren en la lista.

Ahora vamos a crear un trabajo para actualizar la tabla *VenueTypes* en todas las bases de datos de inquilino y agregar los nuevos tipos de ubicación.

Para crear un nuevo trabajo, utilizamos un conjunto de procedimientos almacenados en el sistema de trabajos creados en la base de datos jobaccount cuando se creó la cuenta de trabajo.

1. Abra SSMS y conéctese al servidor de catálogo: catalog-\<usuario\>.database.windows.net server.
1. Conéctese también al servidor de inquilino: tenants1-\<usuario\>.database.windows.net.
1. Vaya a la base de datos *contosoconcerthall* del servidor *tenants1* y consulte la tabla *VenueTypes* para confirmar que *Motorcycle Racing* y *Swimming Club* **no figuran** en la lista de resultados.
1. Abra el archivo …\\Learning Modules\\Schema Management\\DeployReferenceData.sql.
1. Modifique \<usuario\> por el nombre de usuario utilizado al implementar la aplicación WTP en las tres ubicaciones del script.
1. Asegúrese de que está conectado a la base de datos jobaccount y presione **F5** para ejecutar el script.

* **sp\_add\_target\_group** crea el nombre del grupo de destino DemoServerGroup. Ahora hay que agregar miembros de destino.
* **sp\_add\_target\_group\_member** agrega un tipo de miembro de destino *servidor* que considera que todas las bases de datos de ese servidor (tenga en cuenta que se trata del servidor customer1-&lt;WtpUser&gt; que contiene las bases de datos de inquilino) en el momento de la ejecución del trabajo deben incluirse en el trabajo. A continuación, se agrega un tipo de miembro de destino *base de datos*, en concreto la base de datos "golden", baseTenantDB, que reside en el servidor catalog-&lt;WtpUser&gt;. Por último, se agrega otro tipo de miembro de destino *base de datos* para incluir la base de datos adhocanalytics que se utiliza en un tutorial posterior.
* **sp\_add\_job** crea un trabajo llamado “Reference Data Deployment”.
* **sp\_add\_jobstep** crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar la tabla de referencia VenueTypes.
* En las demás vistas del script se muestran los objetos existentes y se supervisa la ejecución de los trabajos. Revise el valor de estado de la columna **Ciclo de vida**. El trabajo ha finalizado correctamente en todas las bases de datos de inquilino y las dos bases de datos adicionales que contienen la tabla de referencia.

1. En SSMS, vaya a la base de datos *contosoconcerthall* del servidor *tenants1* y consulte la tabla *VenueTypes* para confirmar que *Motorcycle Racing* y *Swimming Club* **figuran** ahora en la lista de resultados.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creación de un trabajo para administrar el índice de tabla de referencia

Al igual que en el ejercicio anterior, en este ejercicio se crea un trabajo para volver a crear el índice en la clave principal de la tabla de referencia, una operación de administración de bases de datos habitual que puede realizar un administrador después de cargar una gran cantidad de datos en una tabla.

Cree un trabajo con los mismos procedimientos almacenados del "sistema" de trabajos.

1. Abra SSMS y conéctese al servidor catalog-&lt;WtpUser&gt;.database.windows.net.
1. Abra el archivo …\\Learning Modules\\Schema Management\\OnlineReindex.sql.
1. Haga clic con el botón derecho, seleccione Conexión y conéctese al servidor catalog-&lt;WtpUser&gt;.database.windows.net server, en caso de que todavía no esté conectado.
1. Asegúrese de que está conectado a la base de datos jobaccount y presione F5 para ejecutar el script.

* sp\_add\_job crea un nuevo trabajo denominado “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”.
* sp\_add\_jobstep crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar el índice.




## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Crear una cuenta de trabajo para consultar en varios inquilinos
> * Actualizar datos en todas las bases de datos de inquilino
> * Crear un índice en una tabla en todas las bases de datos de inquilino

[Tutorial de análisis ad hoc](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>Recursos adicionales

* [Otros tutoriales basados en la implementación inicial de la aplicación Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Administración de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-jobs-overview.md)
* [Creación y administración de bases de datos escaladas horizontalmente](sql-database-elastic-jobs-create-and-manage.md)
