---
title: Copia de una base de datos de Azure SQL con PowerShell | Microsoft Docs
description: Crear copia de una base de datos SQL de Azure con PowerShell
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5916e527e078fbd6c6ee6ce0da7707611b20eafc


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>Copia de una Base de datos SQL de Azure con PowerShell
> [!div class="op_single_selector"]
> * [Información general](sql-database-copy.md)
> * [Portal de Azure](sql-database-copy-portal.md)
> * [PowerShell](sql-database-copy-powershell.md)
> * [T-SQL](sql-database-copy-transact-sql.md)
> 
> 

En este artículo se muestra cómo copiar una base de datos SQL con PowerShell en el mismo servidor, en uno diferente, además de cómo copiar una base de datos en un [grupo de bases de datos elásticas](sql-database-elastic-pool.md). La operación de copia de bases de datos utiliza el cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) . 

Para completar este artículo, necesitará lo siguiente:

* Una base de datos SQL de Azure (una base de datos para copiar). Si no tiene una base de datos SQL, cree una siguiendo los pasos de este artículo: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
* La versión más reciente de Azure PowerShell. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Muchas de las nuevas características de SQL Database solo se admiten cuando se utiliza el [modelo de implementación de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), por lo que los ejemplos emplean los [cmdlets de PowerShell de Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) para Resource Manager. Se admiten los [cmdlets de Base de datos SQL de Azure (clásicos)](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) del modelo de implementación clásica existentes por compatibilidad con versiones anteriores, pero se recomienda usar los cmdlets de Resource Manager.

> [!NOTE]
> Según el tamaño de su base de datos, la operación de copia puede tardar unos minutos en completarse.
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar una base de datos SQL en el mismo servidor
Para crear la copia en el mismo servidor, omita el parámetro `-CopyServerName` (o establézcalo en el mismo servidor).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiar una base de datos SQL en un servidor diferente
Para crear la copia en otro servidor, incluya el parámetro `-CopyServerName` y establézcalo en un servidor diferente. El servidor donde se realizará la *copia* ya debe existir. Si está en otro grupo de recursos, también debe incluir el parámetro `-CopyResourceGroupName` .

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copia de una base de datos SQL en un grupo de bases de datos elásticas
Para crear una copia de una base de datos SQL en un grupo, establezca el parámetro `-ElasticPoolName` en un grupo que ya exista.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Resolución de inicios de sesión
Para resolver los inicios de sesión una vez completada la operación de copia, consulte [Resolución de inicios de sesión](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>Script de PowerShell de ejemplo
El siguiente script da por hecho que el grupo y todos los grupos de recursos y servidores ya existen (reemplace los valores de variable por los recursos existentes). Todos deben estar creados, excepto la copia de la base de datos.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>Pasos siguientes
* Consulte [Copiar una base de datos SQL de Azure](sql-database-copy.md) para obtener información sobre cómo copiar una base de datos SQL de Azure.
* Consulte el artículo sobre cómo [copiar una base de datos SQL de Azure mediante Azure Portal](sql-database-copy-portal.md) para realizar una copia de una base de datos a través de Azure Portal.
* Consulte [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
* Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.

## <a name="additional-resources"></a>Recursos adicionales
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [Administración de inicios de sesión](sql-database-manage-logins.md)
* [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
* [Exportar la base de datos a un BACPAC](sql-database-export.md)
* [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
* [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Referencia para cmdlets de PowerShell de Azure SQL Database](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)




<!--HONumber=Nov16_HO3-->


