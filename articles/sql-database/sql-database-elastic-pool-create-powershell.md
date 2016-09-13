<properties
    pageTitle="Creación de un nuevo grupo de bases de datos elásticas con PowerShell | Microsoft Azure"
    description="Aprenda a usar PowerShell para escalar horizontalmente los recursos de la Base de datos SQL de Azure mediante la creación de un grupo de bases de datos elásticas escalable para administrar varias bases de datos."
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Creación de un nuevo grupo de bases de datos elásticas con PowerShell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Obtenga información sobre cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) mediante cmdlets de PowerShell.

Para ver los códigos de error comunes, consulte [Códigos de error para las aplicaciones cliente de la Base de datos SQL: error de conexión de base de datos y otros problemas](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Los grupos elásticos están disponibles con carácter general (GA) en todas las regiones de Azure excepto centro-norte de EE. UU. y oeste de la India, en donde actualmente se encuentran en versión preliminar. La disponibilidad general de los grupos elásticos en estas regiones se proporcionarán tan pronto como sea posible. Además, los grupos elásticos no admiten actualmente las bases de datos mediante [OLTP en memoria o análisis en memoria](sql-database-in-memory.md).


Debe ejecutar Azure PowerShell 1.0 o superior. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

## Creación de un nuevo grupo

El cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) crea un nuevo grupo. Los valores de eDTU por grupo, DTU mín. y máx. están limitados por el valor de nivel de servicio (basic, standard o premium). Consulte [Límites de almacenamiento y de eDTU para grupos de bases de datos elásticas y bases de datos elásticas](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Creación de una nueva base de datos elástica en un grupo

Use el cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) y establezca el parámetro **ElasticPoolName** en el grupo de destino. Para mover una base de datos existente a un grupo, consulte [Movimiento de una base de datos a un grupo elástico](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Creación de un grupo e incorporación al mismo de varias bases de datos nuevas 

La creación de un gran número de bases de datos en un grupo puede tardar tiempo cuando se realiza mediante el portal o los cmdlets de PowerShell que crean una sola base de datos cada vez. Para automatizar la creación de un grupo nuevo, consulte [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## Ejemplo: creación de un grupo mediante PowerShell 

Este script crea un nuevo grupo de recursos de Azure y un servidor nuevo. Cuando se le solicite, proporcione un nombre de usuario y una contraseña de administrador para el nuevo servidor (no las credenciales de Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## Pasos siguientes

- [Administración del grupo](sql-database-elastic-pool-manage-powershell.md)
- [Creación de trabajos elásticos](sql-database-elastic-jobs-overview.md): los trabajos elásticos le permiten ejecutar scripts de T-SQL en cualquier cantidad de bases de datos del grupo.
- [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md): utilice las herramientas de la base de datos elástica para realizar un escalado horizontal.

<!---HONumber=AcomDC_0907_2016-->