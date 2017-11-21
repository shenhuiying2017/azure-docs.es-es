---
title: "Creación y administración de servidores y bases de datos SQL de Azure | Microsoft Docs"
description: "Aprenda sobre los conceptos relativos a las bases de datos y los servidores de Transact-SQL, y sobre la creación y administración de bases de datos y servidores."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/11/2017
ms.author: carlrab
ms.openlocfilehash: 469db4f3faf12cbd778f18b7bc74ec6b86b412c7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Creación y administración de servidores y bases de datos de Azure SQL Database

Una base de datos SQL de Azure es una base de datos administrada de Microsoft Azure creada dentro de un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con un conjunto definido de [recursos de proceso y de almacenamiento para diferentes cargas de trabajo](sql-database-service-tiers.md). Una base de datos SQL de Azure está asociada con un servidor lógico de Azure SQL Database, que se crea dentro de una región específica de Azure. 

## <a name="an-azure-sql-database-can-be-a-single-pooled-or-partitioned-database"></a>Una base de datos SQL de Azure puede ser una base de datos única, agrupada o con particiones

Una base de datos SQL de Azure puede ser:

- Una [base de datos única](sql-database-single-database-resources.md) con su propio conjunto de recursos
- Parte de un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) que comparte un conjunto de recursos
- Parte de un [conjunto escalado horizontalmente de bases de datos particionadas](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling), que pueden ser simples o agrupadas.
- Parte de un conjunto de bases de datos que participan en un [modelo de diseño de SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) y cuyas bases de datos pueden ser simples o agrupadas (o ambas). 

> [!TIP]
> Para conocer los nombres de base de datos válidos, consulte [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificadores de base de datos). 
>
 
- La intercalación de bases de datos predeterminada que utiliza Microsoft Azure SQL Database es **SQL_LATIN1_GENERAL_CP1_CI_AS**, donde **LATIN1_GENERAL** corresponde a inglés (Estados Unidos); **CP1** es la página de códigos 1252; **CI** indica que no se distingue mayúsculas de minúsculas y **AS** especifica que se tienen en cuenta los acentos. Para obtener más información acerca de cómo establecer la intercalación, consulte [COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).
- Microsoft Azure SQL Database admite el cliente de protocolo de secuencia de datos tabular (TDS) en la versión 7.3 o posterior.
- Se permiten únicamente las conexiones TCP/IP.

## <a name="what-is-an-azure-sql-logical-server"></a>¿Qué es un servidor lógico de Azure SQL?

Un servidor lógico actúa como punto administrativo central para varias bases de datos, incluidos los [grupos de bases de datos elásticas](sql-database-elastic-pool.md), los [inicios de sesión](sql-database-manage-logins.md), las [reglas de firewall](sql-database-firewall-configure.md), las [reglas de auditoría](sql-database-auditing.md), las [directivas de detección de amenazas](sql-database-threat-detection.md) y los [grupos de conmutación por error](sql-database-geo-replication-overview.md). Un servidor lógico puede estar en una región distinta a la de su grupo de recursos. El servidor lógico debe existir antes de crear la base de datos SQL de Azure. Todas las bases de datos de un servidor se crean dentro de la misma región que el servidor lógico. 


> [!IMPORTANT]
> En SQL Database, un servidor es una construcción lógica distinta de una instancia de SQL Server, con la que puede estar familiarizado en el mundo de implementaciones locales. En concreto, el servicio SQL Database no ofrece ninguna garantía con respecto a la ubicación de las bases de datos con respecto a sus servidores lógicos y no expone ningún acceso de nivel de instancia ni características.
> 

Al crear un servidor lógico, proporciona una cuenta y una contraseña para iniciar sesión en el servidor con derechos administrativos para la base de datos maestra de dicho servidor y todas las bases de datos creadas en dicho servidor. Esta cuenta inicial es una cuenta de inicio de sesión de SQL. Azure SQL Database admite la autenticación de SQL y la autenticación de Azure Active Directory. Para obtener información sobre inicios de sesión y autenticación, vea [Administrar bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md). La autenticación de Windows no es compatible. 

> [!TIP]
> Para conocer cuáles son los nombres de servidor y de grupo de recursos válidos, vea las [reglas y restricciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Un servidor lógico de Azure Database:

- Se crea en una suscripción de Azure, pero puede transferirse a otra suscripción con los recursos que contiene.
- Es el recurso primario para bases de datos, grupos elásticos y almacenamientos de datos.
- Proporciona un espacio de nombres para bases de datos, grupos elásticos y almacenamientos de datos.
- Es un contenedor lógico con semántica de duración segura. Si se elimina un servidor, se eliminan las bases de datos independientes, los grupos elásticos y los almacenamientos de datos.
- Participa en el [control de acceso basado en rol (RBAC) de Azure](/active-directory/role-based-access-control-what-is). Las bases de datos, los grupos elásticos y los almacenamientos de datos dentro de un servidor heredan los derechos de acceso del servidor.
- Es un elemento de orden superior de la identidad de las bases de datos, los grupos elásticos y los almacenamientos de datos con fines de administración de recursos de Azure (vea el esquema de direcciones URL para bases de datos y grupos).
- Coloca recursos en una región.
- Proporciona un punto de conexión para el acceso a la base de datos (<serverName>. database.windows.net).
- Proporciona acceso a los metadatos de recursos contenidos a través de las DMV conectándose a una base de datos maestra. 
- Proporciona el ámbito de las directivas de administración que se aplican a sus bases de datos: inicios de sesión, firewall, auditoría, detección de amenazas, etc. 
- Está restringido por una cuota dentro de la suscripción primaria (seis servidores por suscripción de forma predeterminada; [consulte el artículo sobre los límites de la suscripción aquí](../azure-subscription-service-limits.md)).
- Proporciona el ámbito de la cuota de la base de datos y la cuota de DTU para los recursos que contiene (por ejemplo, 45000 DTU).
- Es el ámbito de control de versiones para funciones que se habilitan en los recursos contenidos. 
- Los inicios de sesión de la entidad de seguridad en el nivel de servidor pueden administrar todas las bases de datos en un servidor.
- Puede contener inicios de sesión similares a los de las instancias de SQL Server en sus instalaciones que tienen acceso a una o más bases de datos en el servidor y puede ser concesionario de derechos administrativos limitados. Para obtener más información, consulte el artículo sobre [inicios de sesión](sql-database-manage-logins.md).

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Protección de bases de datos SQL de Azure con el firewall de SQL Database

Para ayudar a proteger los datos, un [firewall de SQL Database](sql-database-firewall-configure.md) impide cualquier acceso al servidor de base de datos o a cualquiera de sus bases de datos desde fuera de la conexión al servidor directamente a través de la conexión a la suscripción de Azure. Para habilitar la conectividad adicional, debe [crear una o varias reglas de firewall](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Para crear y administrar grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Administración de servidores, bases de datos y firewalls de Azure SQL en Azure Portal

Puede crear el grupo de recursos de la base de datos SQL de Azure con antelación o mientras crea el propio servidor. Existen varios métodos para obtener un nuevo formulario de servidor SQL, bien mediante la creación de un nuevo servidor SQL o como parte de la creación de una base de datos nueva. 

### <a name="create-a-blank-sql-server-logical-server"></a>Creación de un servidor SQL en blanco (servidor lógico)

Para crear un servidor de Azure SQL Database (sin una base de datos) en [Azure Portal](https://portal.azure.com), vaya a un formulario de SQL Server (servidor lógico).  

### <a name="create-a-blank-or-sample-sql-database"></a>Creación de una base de datos SQL de ejemplo o en blanco

Para crear una base de datos de Azure SQL Database en [Azure Portal](https://portal.azure.com), vaya al formulario en blanco de SQL Database y facilite la información requerida. Puede crear un servidor lógico y un grupo de recursos de Azure SQL Database con antelación o mientras crea la propia base de datos. Puede crear una base de datos en blanco o de ejemplo basada en Adventure Works LT. 

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Para obtener información sobre cómo seleccionar el plan de tarifa para la base de datos, consulte los [niveles de servicio](sql-database-service-tiers.md).
>

### <a name="manage-an-existing-sql-server"></a>Administración de un servidor SQL Server existente

Para administrar un servidor existente, vaya al servidor mediante una serie de métodos, como la página específica de SQL Database, la página de **Servidores SQL Server** o la página de **Todos los recursos**. 

Para administrar una base de datos existente, vaya a la página de **SQL Database** y haga clic en la base de datos que desea administrar. En la captura de pantalla siguiente se muestra cómo empezar a configurar un firewall de nivel de servidor para una base de datos desde la página de **información general** de una base de datos. 

   ![regla de firewall del servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Para configurar las propiedades de rendimiento de una base de datos, vea los [niveles de servicio](sql-database-service-tiers.md).
>

> [!TIP]
> Para acceder a un tutorial de inicio rápido de Azure Portal, vea [Creación de una instancia de Azure SQL Database en Azure Portal](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Administración de servidores, bases de datos y firewalls de Azure SQL con PowerShell

Para crear y administrar servidores, bases de datos y firewalls SQL de Azure con Azure PowerShell, use los siguientes cmdlets de PowerShell. Si necesita instalar o actualizar PowerShell, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). Para crear y administrar grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descripción |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea una base de datos. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtiene una o más bases de datos.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Establece las propiedades de una base de datos, o mueve una base de datos existente en un grupo elástico.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Quita una base de datos.|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Crea un grupo de recursos.
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Crea un servidor.|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Devuelve información sobre los servidores.|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifica las propiedades de un servidor.|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Quita un servidor.|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Crea una regla de firewall de nivel de servidor. |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Obtiene reglas de firewall para un servidor|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifica una regla de firewall en un servidor.|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Obtiene una regla de firewall de un servidor.|
| New-AzureRmSqlServerVirtualNetworkRule | Permite crear una [*regla de red virtual*](sql-database-vnet-service-endpoint-rule-overview.md) basada en una subred que es un punto de conexión de servicio de red virtual. |

> [!TIP]
> Para obtener un tutorial de inicio rápido de PowerShell, vea [Creación de una sola instancia de Azure SQL Database con PowerShell](sql-database-get-started-portal.md). Para información sobre los scripts de ejemplo de PowerShell, consulte [Uso de PowerShell para crear una instancia única de Azure SQL Database y configurar una regla de firewall](scripts/sql-database-create-and-configure-database-powershell.md) y [Supervisión y escalado de una instancia única de SQL Database mediante PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Administración de servidores, bases de datos y firewalls de Azure SQL con la CLI de Azure

Para crear y administrar servidores, bases de datos y firewalls SQL de Azure con la [CLI de Azure](/cli/azure/overview), use los siguientes comandos de la [CLI de Azure para SQL Database](/cli/azure/sql/db). Use [Cloud Shell](/azure/cloud-shell/overview) para ejecutar la CLI en el explorador o [instálela](/cli/azure/install-azure-cli) en Windows, Linux o macOS. Para crear y administrar grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).

| Cmdlet | Descripción |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Crea una base de datos.|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Enumera todas las bases de datos y almacenes de datos de un servidor, o todas las bases de datos de un grupo elástico.|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Enumera los objetivos de servicio y los límites de almacenamiento disponibles.|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Devuelve los usos de la base de datos.|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Obtiene una base de datos o un almacenamiento de datos.|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Actualiza una base de datos.|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Quita una base de datos.|
|[az group create](/cli/azure/group#az_group_create)|Crea un grupo de recursos.|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Crea un servidor.|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Enumera los servidores.|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Devuelve los usos del servidor.|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Obtiene un servidor.|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Actualiza un servidor.|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Permite eliminar un servidor.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Crea una regla de firewall del servidor.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Enumera las reglas de firewall en un servidor.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Muestra los detalles de una regla de firewall.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Actualiza una regla de firewall.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina una regla de firewall.|

> [!TIP]
> Para obtener un tutorial de inicio rápido de la CLI de Azure, vea [Creación de una sola instancia de Azure SQL Database con la CLI de Azure](sql-database-get-started-cli.md). Para información sobre los scripts de ejemplo de la CLI de Azure, consulte [Uso de la CLI para crear una instancia única de Azure SQL Database y configurar una regla de firewall](scripts/sql-database-create-and-configure-database-cli.md) y [Uso de la CLI para supervisar y escalar una instancia de SQL Database](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Administración de servidores, bases de datos y firewalls de Azure SQL con Transact-SQL

Para crear y administrar servidores, bases de datos y firewalls SQL de Azure con Transact-SQL, use los siguientes comandos de Transact-SQL. Puede emitir estos comandos mediante Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o cualquier otro programa que pueda conectarse a un servidor de Azure SQL Database y pasar comandos de Transact-SQL. Para administrar grupos de bases de datos elásticas, consulte [Grupos elásticos](sql-database-elastic-pool.md).

> [!IMPORTANT]
> No se puede crear ni eliminar un servidor mediante Transact-SQL.
>

| Comando | Descripción |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crear una base de datos. Debe estar conectado a la base de datos maestra para crear una base de datos.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica una base de datos SQL de Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifica una instancia de Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Permite eliminar una base de datos.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devuelve la edición (nivel de servicio), el objetivo de servicio (plan de tarifa) y el nombre del grupo elástico, si existe, para una base de datos SQL de Azure o una instancia de Azure SQL Data Warehouse. Si inició sesión en la base de datos maestra en un servidor de Azure SQL Database, devuelve información sobre todas las bases de datos. Para Azure SQL Data Warehouse, debe estar conectado a la base de datos maestra.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Devuelve el consumo de CPU, E/S y memoria para una base de datos de Azure SQL Database. Hay una fila para cada 15 segundos, incluso si no hay ninguna actividad en la base de datos.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Devuelve datos de almacenamiento y uso de CPU para una instancia de Azure SQL Database. Los datos se recopilan y agregan en intervalos de cinco minutos.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contiene estadísticas de eventos de conectividad de base de datos de SQL Database, que proporcionan una visión general de los aciertos y errores de conexión a la base de datos. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Devuelve las conexiones realizadas correctamente a la base de datos de Azure SQL Database, los errores de conexión y los interbloqueos. Puede usar esta información para realizar el seguimiento de la actividad de base de datos o solucionar problemas con SQL Database.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crea o actualiza la configuración del firewall de nivel de servidor del servidor de SQL Database. Este procedimiento almacenado solo está disponible en la base de datos maestra para el inicio de sesión de entidad de seguridad de nivel de servidor. Solo se puede crear una regla de firewall de nivel de servidor mediante Transact-SQL después de que un usuario con permisos a nivel de Azure haya creado la primera regla de firewall de nivel de servidor.|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Devuelve información sobre la configuración del firewall de nivel de servidor asociada a Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Quita la configuración del firewall de nivel de servidor del servidor de SQL Database. Este procedimiento almacenado solo está disponible en la base de datos maestra para el inicio de sesión de entidad de seguridad de nivel de servidor.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crea o actualiza las reglas de firewall de nivel de base de datos para la instancia de Azure SQL Database o SQL Data Warehouse. Las reglas de firewall de base de datos pueden configurarse para la base de datos maestra y las bases de datos de usuario de SQL Database. Las reglas de firewall de base de datos son útiles cuando se usan usuarios de base de datos independientes. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Devuelve información sobre la configuración del firewall de nivel de base de datos asociada a Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Quita las reglas de firewall de nivel de base de datos de la instancia de Azure SQL Database o SQL Data Warehouse. |


> [!TIP]
> Para acceder a un tutorial de inicio rápido sobre el uso de SQL Server Management Studio en Microsoft Windows, vea [Azure SQL Database: use SQL Server Management Studio para conectarse a los datos y realizar consultas en ellos](sql-database-connect-query-ssms.md). Para acceder a un tutorial de inicio rápido sobre el uso de Visual Studio Code en macOS, Linux o Windows, vea [Azure SQL Database: uso de Visual Studio Code para conectar y consultar datos](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Administración de servidores, bases de datos y firewalls de Azure SQL con la API de REST

Para crear y administrar servidores, bases de datos y firewalls de Azure SQL, use estas solicitudes de la API de REST.

| Comando | Descripción |
| --- | --- |
|[Servers - Create Or Update](/rest/api/sql/servers/createorupdate)|Crea o actualiza un nuevo servidor.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Elimina un servidor de SQL Server.|
|[Servers - Get](/rest/api/sql/servers/get)|Obtiene un servidor.|
|[Servers - List](/rest/api/sql/servers/list)|Devuelve una lista de servidores.|
|[Servers - List By Resource Group](/rest/api/sql/servers/listbyresourcegroup)|Devuelve una lista de servidores en un grupo de recursos.|
|[Servers - Update](/rest/api/sql/servers/update)|Actualiza un servidor existente.|
|[Servers - Sql](/rest/api/sql/servers%20-%20sql)|Determina si se puede crear un recurso con el nombre especificado.|
|[Databases - Create Or Update](/rest/api/sql/databases/createorupdate)|Crea una nueva base de datos o actualiza una ya existente.|
|[Databases - Get](/rest/api/sql/databases/get)|Obtiene una base de datos.|
|[Databases - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|Obtiene una base de datos dentro de un grupo elástico.|
|[Databases - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtiene una base de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|Devuelve una lista de bases de datos de un grupo elástico.|
|[Databases - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devuelve una lista de bases de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Devuelve una lista de bases de datos de un servidor.|
|[Databases - Update](/rest/api/sql/databases/update)|Actualiza una base de datos existente.|
|[Firewall Rules - Create Or Update](/rest/api/sql/firewallrules/createorupdate)|Crea o actualiza una regla de firewall.|
|[Firewall Rules - Delete](/rest/api/sql/firewallrules/delete)|Elimina una regla de firewall.|
|[Firewall Rules - Get](/rest/api/sql/firewallrules/get)|Obtiene una regla de firewall.|
|[Firewall Rules - List By Server](/rest/api/sql/firewallrules/listbyserver)|Devuelve una lista de reglas de firewall.|

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre la agrupación de bases de datos mediante grupos elásticos, consulte [Grupos elásticos](sql-database-elastic-pool.md).
- Para más información sobre el servicio Azure SQL Database, vea [¿Qué es el servicio Azure SQL Database?](sql-database-technical-overview.md).
- Para obtener información sobre cómo migrar una base de datos de SQL Server a Azure, vea [Migración a Azure SQL Database](sql-database-cloud-migrate.md).
- Para obtener información sobre las características admitidas, consulte el [artículo que trata sobre dicho tema](sql-database-features.md).
