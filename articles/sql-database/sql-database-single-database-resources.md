---
title: Una sola base de datos de Azure SQL Database | Microsoft Docs
description: Administre el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento para una sola base de datos SQL de Azure.
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
ms.date: 12/14/2017
ms.author: carlrab
ms.openlocfilehash: 0f88b09c342c1849a5c61fdb5dc048d7cbadc83b
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Administración de recursos para una sola base de datos en Azure SQL Database

Con una sola base de datos, debe determinar la cantidad de recursos que esta requiere para controlar su carga de trabajo en el nivel de servicio, el nivel de rendimiento y la cantidad de almacenamiento que necesita. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Administración de una sola base de datos mediante Azure Portal

Para establecer o cambiar el nivel de servicio, el nivel de rendimiento o la cantidad de almacenamiento de una base de datos SQL de Azure nueva o existente mediante Azure Portal, abra la ventana **Configurar rendimiento** ventana de la base de datos; para ello, haga clic en **Plan de tarifa (escalar DTU)**, como se muestra en la siguiente captura de pantalla. 

- Establezca o cambie el nivel de servicio y seleccione el nivel de servicio de su carga de trabajo. 
- Establezca o cambie el nivel de rendimiento (**DTU**) dentro de un nivel de servicio mediante el control deslizante de **DTU**.
- Establezca o cambie la cantidad de almacenamiento para el nivel de rendimiento mediante el control deslizante **Almacenamiento**. 

![Configuración del nivel de servicio y rendimiento](./media/sql-database-single-database-resources/change-service-tier.png)

Haga clic en **Introducción** para supervisar o cancelar una operación en curso.

![Cancelar operación](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Revise las [limitaciones actuales de las bases de datos P11 y P15 con un tamaño máximo de 4 TB](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) al seleccionar un nivel de servicio P11 o P15.
>

## <a name="manage-single-database-resources-using-powershell"></a>Administración de los recursos de una sola base de datos mediante PowerShell

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos de Azure SQL, use los siguientes cmdlets de PowerShell. Si necesita instalar o actualizar PowerShell, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 

| Cmdlet | DESCRIPCIÓN |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea una base de datos. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtiene una o más bases de datos.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Establece las propiedades de una base de datos, o mueve una base de datos existente en un grupo elástico. Por ejemplo, use la propiedad **MaxSizeBytes** para establecer el tamaño máximo de una base de datos.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Obtiene el estado de las operaciones de base de datos. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Cancela la operación de actualización asincrónica en la base de datos.|


> [!TIP]
> Para ver un script de ejemplo de PowerShell que supervisa las métricas de rendimiento de una base de datos, la escala a un nivel de rendimiento superior y crea una regla de alerta sobre una de estas métricas, consulte [Supervisión y escalado de una instancia única de SQL Database mediante PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Administración de una sola base de datos mediante la CLI de Azure

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos Azure SQL mediante la CLI de Azure, use estos comandos de [SQL Database de la CLI de Azure](/cli/azure/sql/db). Use [Cloud Shell](/azure/cloud-shell/overview) para ejecutar la CLI en el explorador o [instálela](/cli/azure/install-azure-cli) en Windows, Linux o macOS. Para crear y administrar grupos elásticos de SQL, vea [Grupos elásticos](sql-database-elastic-pool.md).

| Cmdlet | DESCRIPCIÓN |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Crea una regla de firewall del servidor.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Enumera las reglas de firewall en un servidor.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Muestra los detalles de una regla de firewall.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Actualiza una regla de firewall.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina una regla de firewall.|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|Obtiene una lista de las operaciones realizadas en la base de datos.|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Cancela la operación asincrónica en la base de datos.|

> [!TIP]
> Para ver un script de ejemplo de la CLI de Azure que escala una única base de datos SQL de Azure a otro nivel de rendimiento después de consultar la información del tamaño de la base de datos, consulte [Uso de la CLI para supervisar y escalar una instancia de SQL Database](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Administración de una sola base de datos mediante Transact-SQL

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos Azure SQL con Transact-SQL, use estos comandos de T-SQL. Puede emitir estos comandos mediante Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o cualquier otro programa que pueda conectarse a un servidor de Azure SQL Database y pasar comandos de Transact-SQL. 

| Get-Help | DESCRIPCIÓN |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crear una base de datos. Debe estar conectado a la base de datos maestra para crear una base de datos.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica una base de datos SQL de Azure. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Devuelve la edición (nivel de servicio), el objetivo de servicio (plan de tarifa) y el nombre del grupo elástico, si existe, para una base de datos SQL de Azure o una instancia de Azure SQL Data Warehouse. Si inició sesión en la base de datos maestra en un servidor de Azure SQL Database, devuelve información sobre todas las bases de datos. Para Azure SQL Data Warehouse, debe estar conectado a la base de datos maestra.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Muestra el número, el tipo y la duración de las bases de datos en un servidor de Azure SQL Database.|

En el ejemplo siguiente se muestra cómo se cambia el tamaño máximo de una base de datos mediante el comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Administración de los recursos de una sola base de datos mediante la API de REST

Para establecer o cambiar los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento de las bases de datos de Azure SQL, use estas solicitudes de la API de REST.

| Get-Help | DESCRIPCIÓN |
| --- | --- |
|[Databases - Create Or Update](/rest/api/sql/databases/createorupdate)|Crea una nueva base de datos o actualiza una ya existente.|
|[Databases - Get](/rest/api/sql/databases/get)|Obtiene una base de datos.|
|[Databases - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|Obtiene una base de datos dentro de un grupo elástico.|
|[Databases - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtiene una base de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|Devuelve una lista de bases de datos de un grupo elástico.|
|[Databases - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Devuelve una lista de bases de datos dentro de un grupo elástico recomendado.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Devuelve una lista de bases de datos de un servidor.|
|[Databases - Update](/rest/api/sql/databases/update)|Actualiza una base de datos existente.|
|[Operaciones: lista](/rest/api/sql/Operations/List)|Enumera todas las operaciones de la API de REST de SQL disponibles.|



## <a name="next-steps"></a>pasos siguientes

- Más información acerca de los niveles de servicio, los niveles de rendimiento y la cantidad de almacenamiento, en [Niveles de servicio](sql-database-service-tiers.md).
- Más información sobre grupos elásticos en [este artículo](sql-database-elastic-pool.md).
- Información sobre [límites, cuotas y restricciones de suscripción y servicios de Azure](../azure-subscription-service-limits.md).
