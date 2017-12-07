---
title: "Administración del esquema de Azure SQL Database en una aplicación multiinquilino | Microsoft Docs"
description: "Administración del esquema para varios inquilinos en una aplicación multiinquilino que usa Azure SQL Database"
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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: e4b8e38d20ec408869f2228597afdf2f9620515b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Administración del esquema para varios inquilinos en una aplicación multiinquilino que usa Azure SQL Database

En el [primer tutorial sobre la aplicación SaaS de base de datos multiinquilino Wingtip Tickets](saas-multitenantdb-get-started-deploy.md) se muestra cómo la aplicación aprovisiona una base de datos multiinquilino con particiones y la registra en el catálogo. Como cualquier otra aplicación, la aplicación SaaS de Wingtip Tickets evolucionará con el tiempo y, en ocasiones, requerirá que se hagan cambios en la base de datos. Estos cambios pueden incluir un esquema nuevo o modificado, datos de referencia nuevos o modificados y tareas de mantenimiento de la base de datos rutinarias para garantizar un rendimiento óptimo de la aplicación. Con una aplicación SaaS, estos cambios deben implementarse de manera coordinada a lo largo de una línea potencialmente masiva de bases de datos de inquilino. Para que estos cambios estén presentes en futuras bases de datos de inquilino, deben incorporarse en el proceso de aprovisionamiento.

Este tutorial explora dos escenarios: la implementación de actualizaciones de datos de referencia para todos los inquilinos y el reajuste de un índice en la tabla que contiene los datos de referencia. La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) se usa para ejecutar estas operaciones en las bases de datos de los inquilinos y la base de datos de inquilino *maestra* se utiliza como plantilla para nuevas bases de datos.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Crear una cuenta de trabajo
> * Consultas entre varios inquilinos
> * Actualizar datos en todas las bases de datos de inquilino
> * Crear un índice en una tabla en todas las bases de datos de inquilino


Para completar este tutorial, asegúrese de cumplir estos requisitos previos:

* La aplicación SaaS de base de datos multiinquilino Wingtip Tickets está instalada. Para implementarla en menos de cinco minutos, consulte el artículo sobre la [implementación y exploración de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell está instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La versión más reciente de SQL Server Management Studio (SSMS) está instalada. [Descarga e instalación de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> En este tutorial se usan características del servicio SQL Database que se encuentran en una versión preliminar limitada (trabajos de Elastic Database). Si desea seguir este tutorial, envíe su identificador de suscripción a SaaSFeedback@microsoft.com con el asunto Elastic Jobs Preview. Después de recibir la confirmación de que se ha habilitado su suscripción, [descargue e instale los cmdlets de trabajos de versión preliminar más recientes](https://github.com/jaredmoo/azure-powershell/releases). Esta versión preliminar es limitada, por lo que debe ponerse en contacto con SaaSFeedback@microsoft.com para realizar preguntas u obtener soporte técnico relacionados.


## <a name="introduction-to-saas-schema-management-patterns"></a>Introducción a los patrones de administración de esquema de SaaS

El modelo de base de datos multiinquilino con particiones utilizado en este ejemplo habilita una base de datos de inquilinos que puede contener cualquier número de inquilinos. En este ejemplo se analiza la posibilidad de usar una combinación de bases de datos multiinquilino o de un solo inquilino, lo cual permitiría habilitar un modelo "híbrido" de administración de inquilinos. Mantener y administrar estas bases de datos resulta complicado. La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) facilita la administración de la capa de datos SQL. Los trabajos permiten ejecutar tareas (scripts T-SQL) en un grupo de bases de datos de manera segura y confiable independientemente de las interacciones o entradas del usuario. Este método puede utilizarse para implementar cambios en el esquema y los datos de referencia comunes en todos los inquilinos de una aplicación. Trabajos elásticos también se puede utilizar para mantener una copia *golden* de la base de datos utilizada para crear nuevos inquilinos, garantizando así que siempre tenga el esquema y los datos de referencia más recientes.

![pantalla](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Versión preliminar limitada de Trabajos elásticos

Hay una nueva versión de Trabajos elásticos que ahora es una característica integrada de Azure SQL Database (no requiere servicios ni componentes adicionales). Esta nueva versión de Trabajos elásticos ofrece actualmente una versión preliminar limitada. Esta versión preliminar limitada admite PowerShell para crear cuentas de trabajo y T-SQL para crear y administrar trabajos.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtención del código fuente y los scripts de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio de GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB). Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Creación de una base de datos de cuentas de trabajo y una nueva cuenta de trabajo

Para este tutorial, es necesario usar PowerShell para crear la base de datos de cuentas de trabajo y una nueva cuenta de trabajo. Al igual que MSDB y el Agente SQL, Trabajos Elásticos utiliza una instancia de Azure SQL Database para almacenar definiciones de trabajos, estados de trabajo y el historial. Una vez creada la cuenta de trabajo, puede crear y supervisar trabajos inmediatamente.

1. Abra …**Learning Modules**Schema Management*\\Demo-SchemaManagement.ps1\\ en \\PowerShell ISE*.
1. Presione **F5** para ejecutar el script.

El script *Demo-SchemaManagement.ps1* llama al script *Deploy-SchemaManagement.ps1* para crear una base de datos *S2* llamada **jobaccount** en el servidor de catálogo. A continuación, crea la cuenta de trabajo, transfiriendo la base de datos jobaccount como un parámetro a la llamada de creación de la cuenta de trabajo.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creación de un trabajo para implementar nuevos datos de referencia en todos los inquilinos

Cada base de datos de inquilinos incluye un conjunto de tipos de ubicación en la tabla **VenueTypes** que definen la clase de eventos que se celebran en una ubicación. En este ejercicio, se implementa una actualización en todas las bases de datos para agregar dos tipos de ubicación adicionales: *Motorcycle Racing* (motociclismo) y *Swimming Club* (club de natación). Estos tipos de ubicación se corresponden con la imagen de fondo que se ve en la aplicación de eventos de inquilino.

Haga clic en el menú desplegable Tipo de lugar de celebración y asegúrese de que solo haya 10 opciones de tipo de ubicación disponibles y, en concreto, que "Motorcycle Racing" y "Swimming Club" no se encuentren en la lista.

Ahora vamos a crear un trabajo para actualizar la tabla **VenueTypes** en todas las bases de datos de inquilino y agregar los nuevos tipos de ubicación.

Para crear un nuevo trabajo, utilizamos un conjunto de procedimientos almacenados en el sistema de trabajos creados en la base de datos jobaccount cuando se creó la cuenta de trabajo.

1. En SSMS, conéctese también al servidor de inquilino: tenants1-mt-\<user\>.database.windows.net
2. Vaya a la base de datos *tenants1* del servidor *tenants1-mt-\<user\>.database.windows.net* y consulte la tabla *VenueTypes* para confirmar que *Motorcycle Racing* y *Swimming Club* **no** figuran en la lista de resultados.
3. Conéctese al servidor de catálogo: catalog-mt-\<user\>.database.windows.net
4. Conéctese a la base de datos jobaccount en el servidor de catálogo.
5. En SSMS, abra el archivo …\\Learning Modules\\Schema Management\\DeployReferenceData.sql.
6. Modifique la instrucción: set @User = &lt;user&gt; y sustituya el valor de usuario utilizado al implementar la aplicación SaaS de base de datos multiinquilino Wingtip Tickets.
7. Presione **F5** para ejecutar el script.

Observe lo siguiente en el script *DeployReferenceData.sql*:
* **sp\_add\_target\_group** crea el nombre del grupo de destino DemoServerGroup. Ahora hay que agregar miembros de destino al grupo.
* **sp\_add\_target\_group\_member** agrega un tipo de miembro de destino *servidor* que considera que todas las bases de datos de ese servidor (tenga en cuenta que se trata del servidor tenants1-mt-&lt;user&gt; que contiene las bases de datos de inquilino) en el momento de la ejecución del trabajo deben incluirse en el trabajo. A continuación, se agrega un tipo de miembro de destino *base de datos* para la base de datos "maestra" (basetenantdb) que reside en el servidor catalog-mt-&lt;user&gt;. Por último, se agrega otro tipo de miembro de destino *base de datos* para incluir la base de datos adhocreporting que se utiliza en un tutorial posterior.
* **sp\_add\_job** crea un trabajo llamado “Reference Data Deployment”.
* **sp\_add\_jobstep** crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar la tabla de referencia VenueTypes.
* En las demás vistas del script se muestran los objetos existentes y se supervisa la ejecución de los trabajos. Use estas consultas para revisar el valor de estado en la columna **ciclo de vida** a fin de determinar si el trabajo ha finalizado correctamente en las bases de datos de inquilino y las dos bases de datos adicionales que contienen la tabla de referencia.

En SSMS, vaya a la base de datos de inquilinos del servidor *tenants1-mt-&lt;user&gt;* y consulte la tabla *VenueTypes* para confirmar que *Motorcycle Racing* y *Swimming Club* se han **agregado* ya a la tabla.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creación de un trabajo para administrar el índice de tabla de referencia

Al igual que en el ejercicio anterior, en este ejercicio se crea un trabajo para volver a crear el índice en la clave principal de la tabla de referencia, una operación de administración de bases de datos habitual que puede realizar un administrador después de cargar una gran cantidad de datos en una tabla.


1. En SSMS, conéctese a las base de datos jobaccount en el servidor catalog-mt-&lt;User&gt;.database.windows.net.
2. En SSMS, abra …\\Learning Modules\\Schema Management\\OnlineReindex.sql.
3. Presione **F5** para ejecutar el script.

Observe lo siguiente en el script *OnlineReindex.sql*:
* **sp\_add\_job** crea un nuevo trabajo denominado “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”.
* **sp\_add\_jobstep** crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar el índice.
* Las demás vistas del script permiten supervisar la ejecución del trabajo. Use estas consultas para revisar el valor de estado en la columna **ciclo de vida** a fin de determinar si el trabajo ha finalizado correctamente en todos los miembros del grupo de destino.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]

> * Crear una cuenta de trabajo para consultar en varios inquilinos
> * Actualizar datos en todas las bases de datos de inquilino
> * Crear un índice en una tabla en todas las bases de datos de inquilino

A continuación, pruebe el [tutorial de creación de informes ad hoc](saas-multitenantdb-adhoc-reporting.md).


## <a name="additional-resources"></a>Recursos adicionales

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Administración de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-jobs-overview.md)
* [Creación y administración de bases de datos escaladas horizontalmente](sql-database-elastic-jobs-create-and-manage.md)
