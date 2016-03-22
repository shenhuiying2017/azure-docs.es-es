<properties 
    pageTitle="Creación de un grupo de bases de datos elásticas (PowerShell) | Microsoft Azure" 
    description="Aprenda a usar PowerShell para escalar horizontalmente los recursos de la Base de datos SQL de Azure mediante la creación de un grupo de bases de datos elásticas para administrar varias bases de datos." 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Creación de un grupo de bases de datos elásticas (PowerShell) 

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Obtenga información sobre cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) mediante cmdlets de PowerShell.

> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-portal.md) en un solo paso.


Debe ejecutar Azure PowerShell 1.0 o superior. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).



## Creación de un grupo de bases de datos elásticas

El cmdlet [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) creará un grupo de bases de datos elásticas.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## Creación de una nueva base de datos elástica en un grupo de bases de datos elásticas

Para crear una nueva base de datos directamente dentro de un grupo, use el cmdlet [AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) y establezca el parámetro **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Desplazamiento de una base de datos independiente a un grupo de bases de datos elásticas

Para mover una base de datos existente a un grupo, use el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) y establezca el parámetro **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## Creación de un grupo de bases de datos elásticas de ejemplo de PowerShell

Este script creará un nuevo servidor, por lo que cuando se le solicite un nombre de usuario y una contraseña, escriba un inicio de sesión y una contraseña del administrador para el nuevo servidor (y no las credenciales de Azure).

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
- [Creación de trabajos elásticos](sql-database-elastic-jobs-overview.md): los trabajos elásticos facilitan la ejecución de scripts de T-SQL con cualquier número de bases de datos en el grupo.


## Referencia de bases de datos elásticas

Para más información acerca de grupos y bases de datos elásticas, consulte [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0316_2016-->