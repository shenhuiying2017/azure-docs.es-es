---
title: "Administración del esquema de Azure SQL Database en una aplicación multiinquilino | Microsoft Docs"
description: "Administración del esquema para varios inquilinos en una aplicación multiinquilino que usa Azure SQL Database"
keywords: tutorial de SQL Database
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Administración del esquema para varios inquilinos en una aplicación multiinquilino que usa Azure SQL Database

En este tutorial se examinan los desafíos de mantenimiento de una potencialmente enorme cantidad de bases de datos en una aplicación de software como servicio (SaaS) en la nube. Se muestran soluciones para administrar las mejoras de esquema que se desarrollan e implementan durante la vida útil de una aplicación.

A medida que cualquier aplicación evoluciona, pueden generarse cambios en las columnas de sus tablas o en otros esquemas, o en sus datos de referencia, o bien en los elementos relacionados con el rendimiento. Con una aplicación de SaaS, estos cambios se deben implementar de manera coordinada en diversas bases de datos de inquilino existentes. Además, estos cambios se deben incluir en futuras bases de datos de inquilino que se agregarán en la aplicación. Por lo tanto, los cambios también se deben incorporar al proceso que aprovisiona las bases de datos nuevas.

#### <a name="two-scenarios"></a>Dos escenarios

En este tutorial se exploran estos dos escenarios:
- La implementación de actualizaciones de los datos de referencia para todos los inquilinos.
- La reoptimización de un índice de la tabla que contiene los datos de referencia.

La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) de Azure SQL Database se usa para ejecutar estas operaciones entre distintas bases de datos de inquilino. Los trabajos también funcionan en la base de datos de inquilino de la plantilla golden. Esta plantilla se usa cuando se aprovisionan bases de datos nuevas.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta de trabajo.
> * Crear consultas entre distintos inquilinos.
> * Actualizar datos en todas las bases de datos de inquilino.
> * Crear un índice en una tabla en todas las bases de datos de inquilino.

## <a name="prerequisites"></a>Requisitos previos

- Ya debe estar implementada la aplicación Wingtip Tickets:
    - Si desea ver las instrucciones, consulte el primer tutorial, en el que se presenta la aplicación de base de datos multiinquilino *Wingtip Tickets* de SaaS:<br />[Implementación y exploración de una aplicación multiinquilino con particiones que usa Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - El proceso de implementación se ejecuta durante menos de cinco minutos.
    - Debe tener instalada la versión *multiinquilino con particiones* de Wingtip. Las versiones para *Independiente* y *Base de datos por inquilino* no admiten este tutorial.

- Debe estar instalada la versión más reciente de SQL Server Management Studio (SSMS). [Descarga e instalación de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell debe estar instalado. Para más información, consulte [Introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> En este tutorial se usan características del servicio Azure SQL Database que se encuentran en una versión preliminar limitada ([trabajos de Elastic Database](sql-database-elastic-database-client-library.md)). Si desea seguir este tutorial, envíe su identificador de suscripción a *SaaSFeedback@microsoft.com* con el asunto Elastic Jobs Preview. Después de recibir la confirmación de que se ha habilitado su suscripción, [descargue e instale los cmdlets de trabajos de versión preliminar más recientes](https://github.com/jaredmoo/azure-powershell/releases). Esta versión preliminar es limitada, por lo que debe ponerse en contacto con *SaaSFeedback@microsoft.com* para realizar preguntas u obtener soporte técnico relacionados.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introducción a los patrones de administración de esquema de SaaS

El modelo de base de datos multiinquilino con particiones utilizado en este ejemplo habilita una base de datos de inquilino para que contenga uno o más inquilinos. En este ejemplo se analiza la posibilidad de usar una combinación de bases de datos multiinquilino o de un solo inquilino, lo cual permitiría habilitar un modelo *híbrido* de administración de inquilinos. Administrar estas bases de datos resulta complicado. La característica [Trabajos elásticos](sql-database-elastic-jobs-overview.md) facilita la administración de la capa de datos SQL. Los trabajos le permiten ejecutar de manera segura y confiable los scripts de Transact-SQL como tareas en un grupo de bases de datos de inquilino. Las tareas son independientes de la interacción o la intervención del usuario. Este método se puede usar para implementar cambios en el esquema o en los datos de referencia comunes en todos los inquilinos de una aplicación. También puede usar Trabajos elásticos para mantener una copia de la base de datos de la plantilla golden. La plantilla se usa para crear inquilinos nuevos, asegurándose siempre de que se usen los datos de referencia y el esquema más recientes.

![pantalla](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Versión preliminar limitada de Trabajos elásticos

Hay una nueva versión de Trabajos elásticos que ahora es una característica integrada de Azure SQL Database. Esto significa que no requiere servicios ni componentes adicionales. Esta nueva versión de Trabajos elásticos ofrece actualmente una versión preliminar limitada. La versión preliminar limitada admite PowerShell para crear cuentas de trabajo y T-SQL para crear y administrar trabajos.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtención del código fuente y los scripts de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets

Los scripts y el código fuente de la aplicación SaaS de base de datos multiinquilino Wingtip Tickets están disponibles en el repositorio [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) de GitHub. Consulte las [instrucciones generales](saas-tenancy-wingtip-app-guidance-tips.md) para saber cuáles son los pasos para descargar y desbloquear los scripts SaaS de Wingtip Tickets. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Creación de una base de datos de cuentas de trabajo y una nueva cuenta de trabajo

Para este tutorial, es necesario usar PowerShell para crear la base de datos de cuentas de trabajo y una nueva cuenta de trabajo. Al igual que la base de datos MSDB usada por el Agente SQL, Trabajos Elásticos usa una instancia de Azure SQL Database para almacenar definiciones de trabajos, estados de trabajo y el historial. Una vez creada la cuenta de trabajo, puede crear y supervisar trabajos inmediatamente.

1. En **PowerShell ISE**, abra *...\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*.
2. Presione **F5** para ejecutar el script.

El script *Demo-SchemaManagement.ps1* llama al script *Deploy-SchemaManagement.ps1* para crear una base de datos de nivel *S2* denominada **jobaccount** en el servidor de catálogo. El script luego crea la cuenta de trabajo, transfiriendo la base de datos jobaccount como un parámetro a la llamada de creación de la cuenta de trabajo.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creación de un trabajo para implementar nuevos datos de referencia en todos los inquilinos

#### <a name="prepare"></a>Preparación

Cada base de datos de inquilinos incluye un conjunto de los tipos de ubicación en la tabla **VenueTypes**. Los tipos de ubicación definen la clase de eventos que se realizan en una ubicación. En este ejercicio, se implementa una actualización en todas las bases de datos para agregar dos tipos de ubicación adicionales: *Motorcycle Racing* (motociclismo) y *Swimming Club* (club de natación). Estos tipos de ubicación se corresponden con la imagen de fondo que se ve en la aplicación de eventos de inquilino.

Antes de que implemente los datos de referencia nuevos, anote la cantidad de tipos de ubicación que ya existe, que puede ser 10. Para hacerlo, haga clic en el siguiente vínculo a la UI web de Wingtip y, luego, en el menú desplegable **Tipo de ubicación**:
- http://events.wingtip-mt.<USER>.trafficmanager.net

Ahora puede contar la cantidad de tipos de ubicación originales. En concreto, observe que todavía no aparecen ni *Motorcycle Racing* ni *Swimming Club*.

#### <a name="steps"></a>Pasos

Ahora cree un trabajo para actualizar la tabla **VenueTypes** en cada base de datos de inquilinos. Para ello, agregue los dos tipos de ubicación nuevos.

Para crear un trabajo nuevo, puede usar el conjunto de procedimientos almacenamientos en el sistema de trabajos que se crearon en la base de datos *jobaccount*. Los procedimientos se crearon cuando se creó la cuenta de trabajo.

1. En SSMS, conéctese también al servidor de inquilino: tenants1-mt-\<user\>.database.windows.net

2. Vaya a la base de datos *tenants1* en el servidor *tenants1-mt-\<user\>.database.windows.net*.

3. Consulte la tabla *VenueTypes* para confirmar que ni *Motorcycle Racing* ni *Swimming Club* están todavía en la lista de resultados.

4. Conéctese al servidor de catálogo, que es *catalog-mt-\<user\>.database.windows.net*.

5. Conéctese a la base de datos *jobaccount* en el servidor de catálogo.

6. En SSMS, abra el archivo *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Modifique la instrucción: set @User = &lt;user&gt; y sustituya el valor de usuario utilizado al implementar la aplicación SaaS de base de datos multiinquilino Wingtip Tickets.

8. Presione **F5** para ejecutar el script.

#### <a name="observe"></a>Observación

Observe los elementos siguientes en el script *DeployReferenceData.sql*:

- **sp\_add\_target\_group** crea el nombre del grupo de destino *DemoServerGroup* y agrega miembros de destino al grupo.

- **sp\_add\_target\_group\_member** agrega los elementos siguientes:
    - Un tipo de miembro de destino de *servidor*.
        - Se trata del servidor *tenants1-mt-&lt;user&gt;* que contiene las bases de datos de inquilinos.
        - Por lo tanto, todas las bases de datos del servidor se incluyen en el trabajo cuando este se ejecuta.
    - Un tipo de miembro de destino de *base de datos* para la base de datos golden (*basetenantdb*) que reside en el servidor on *catalog-mt-&lt;user&gt;*.
    - Un tipo de miembro de destino de *base de datos* para incluir la base de datos *adhocreporting* que se usa en un tutorial posterior.

- **sp\_add\_job** crea un trabajo denominado *Reference Data Deployment*.

- **sp\_add\_jobstep** crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar la tabla de referencia VenueTypes.

- En las demás vistas del script se muestran los objetos existentes y se supervisa la ejecución de los trabajos. Use estas consultas para revisar el valor de estado en la columna **ciclo de vida** a fin de determinar si el trabajo finalizó correctamente. El trabajo actualiza la base de datos de inquilinos y actualiza las dos bases de datos adicionales que contienen la tabla de referencia.

En SSMS, vaya a la base de datos de inquilino del servidor *tenants1-mt-&lt;user&gt;*. Consulte la tabla *VenueTypes* para confirmar que ni *Motorcycle Racing* ni *Swimming Club* ahora se agregaron a la tabla. El número total de tipos de ubicación debe haber aumentado en dos.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creación de un trabajo para administrar el índice de tabla de referencia

Este ejercicio es similar al ejercicio anterior. Este ejercicio crea un trabajo para recompilar el índice en la clave principal de la tabla de referencia. La recompilación de un índice es una operación de administración de bases de datos típica que un administrador puede ejecutar después de cargar datos de gran tamaño en una tabla, a fin de mejorar el rendimiento.

1. En SSMS, conéctese a la base de datos *jobaccount* en el servidor *catalog-mt-&lt;User&gt;.database.windows.net*.

2. En SSMS, abra *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Presione **F5** para ejecutar el script.

#### <a name="observe"></a>Observación

Observe los elementos siguientes en el script *OnlineReindex.sql*:

* **sp\_add\_job** crea un trabajo denominado *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** crea el paso de trabajo que contiene el texto del comando T-SQL para actualizar el índice.

* Las demás vistas del script permiten supervisar la ejecución del trabajo. Use estas consultas para revisar el valor de estado en la columna **ciclo de vida** a fin de determinar si el trabajo ha finalizado correctamente en todos los miembros del grupo de destino.

## <a name="additional-resources"></a>Recursos adicionales

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Administración de bases de datos escaladas horizontalmente en la nube](sql-database-elastic-jobs-overview.md)
* [Creación y administración de bases de datos escaladas horizontalmente](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> - Crear una cuenta de trabajo para consultar en varios inquilinos.
> - Actualizar datos en todas las bases de datos de inquilino.
> - Crear un índice en una tabla en todas las bases de datos de inquilino.

A continuación, pruebe el [tutorial de creación de informes ad hoc](saas-multitenantdb-adhoc-reporting.md).

