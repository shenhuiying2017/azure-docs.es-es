---
title: "Pausa, reanudación y escalado con T-SQL en Azure SQL Data Warehouse | Microsoft Docs"
description: Tareas de Transact-SQL (T-SQL) para el escalado horizontal del rendimiento ajustando DWU. Ahorre costes reduciendo el escalado fuera de horas punta.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 9221d72ecf8ab2ba8b04e4bc97eeef7157817cca
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Administración de la potencia de proceso en Azure SQL Data Warehouse (T-SQL)
> [!div class="op_single_selector"]
> * [Información general](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Ver la configuración de DWU actual
Para ver la configuración actual de DWU para las bases de datos:

1. Abra el Explorador de objetos de SQL Server en Visual Studio.
2. Conéctese a la base de datos maestra asociada al servidor lógico de SQL Database.
3. Seleccione en la vista de administración dinámica sys.database_service_objectives. Este es un ejemplo: 

```sql
SELECT
    db.name [Database]
,   ds.edition [Edition]
,   ds.service_objective [Service Objective]
FROM
    sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Escalado de proceso
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar las DWU:

1. Conéctese a la base de datos maestra asociada al servidor lógico de SQL Database.
2. Use la instrucción TSQL [ALTER DATABASE][ALTER DATABASE]. En el ejemplo siguiente se establece el objetivo de nivel de servicio en DW1000 para la base de datos MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>Comprobación del estado de la base de datos y del progreso de la operación

1. Conéctese a la base de datos maestra asociada al servidor lógico de SQL Database.
2. Envío de una consulta para comprobar el estado de la base de datos

```sql
SELECT *
FROM
sys.databases
```

3. Envío de una consulta para comprobar el estado de una operación

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

Esta DMV devolverá información sobre varias operaciones de administración en Azure SQL Data Warehouse, como la operación y el estado de esta, que será IN_PROGRESS o COMPLETED.



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Para otras tareas de administración, consulte [Información general de administración][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
