<properties
	pageTitle="Administración de Base de datos SQL de Azure con PowerShell | Microsoft Azure"
	description="Administración de Base de datos SQL de Azure con PowerShell."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="sstein"/>

# Administración de Base de datos SQL de Azure con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

En este tema se muestran los cmdlets de PowerShell que se utilizan para realizar numerosas tareas de Azure SQL Database. Para ver la lista completa, consulte [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/mt574084.aspx) (Cmdlets de Azure SQL Database).


## Crear un grupo de recursos

Cree un grupo de recursos para nuestra instancia de SQL Database y los recursos relacionados de Azure con el cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obtener más información, vea [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md). Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## Creación de un servidor de SQL Database

Cree un servidor de SQL Database con el cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Reemplace *server1* por el nombre del servidor. Los nombres de servidores deben ser únicos en todos los servidores de Azure SQL Database. Recibirá un mensaje de error si ya existe el nombre del servidor. Este comando puede tardar varios minutos en completarse. El grupo de recursos ya debe existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Para obtener más información, consulte el artículo de [introducción a SQL Database](sql-database-technical-overview.md). Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Creación de una regla de firewall de servidor de SQL Database

Cree una regla de firewall para acceder al servidor con el cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el cliente. El grupo de recursos y el servidor ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir que otros servicios de Azure accedan al servidor, cree una regla de firewall y establezca `-StartIpAddress` y `-EndIpAddress` en **0.0.0.0**. Con esta acción se agrega una regla de firewall especial que permite el acceso a todo el tráfico de Azure.

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Creación de una base de datos de SQL Database (vacía)

Cree una base de datos con el cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). El grupo de recursos y el servidor ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obtener más información, consulte el artículo de [introducción a SQL Database](sql-database-technical-overview.md). Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## Cambio del nivel de rendimiento de una base de datos SQL

Escale o reduzca verticalmente la base de datos con el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción. Establezca `-RequestedServiceObjectiveName` en un único espacio (como en el siguiente fragmento) para el nivel Básico. Para otros niveles, establézcalo en *S0*, *S1*, *P1*, *P6*, etc., como en el ejemplo anterior.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Si quiere obtener más información, consulte el artículo sobre el [rendimiento y las opciones de SQL Database para comprender lo que hay disponible en cada nivel de servicio](sql-database-service-tiers.md). Para ver un script de ejemplo, consulte [Script de PowerShell de ejemplo para cambiar el nivel de servicio y el nivel de rendimiento de su SQL Database](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## Copiar una base de datos SQL en el mismo servidor

Copie una SQL Database en el mismo servidor con el cmdlet [AzureRmSqlDatabaseCopy nuevo](https://msdn.microsoft.com/library/azure/mt603644.aspx). Establezca `-CopyServerName` y `-CopyResourceGroupName` en los mismos valores que el grupo de recursos y el servidor de bases de datos de origen.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para más información, vea [Copiar una base de datos SQL de Azure](sql-database-copy.md). Para ver un script de ejemplo, consulte[Copia de una SQL Database con PowerShell](sql-database-copy-powershell.md#example-powershell-script).


## Eliminación de una Base de datos SQL

Elimine una SQL Database con el cmdlet [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Eliminación de un servidor de SQL Database

Elimine un servidor con el cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## Creación y administración de grupos de bases de datos elásticas mediante PowerShell

Para obtener información sobre cómo crear grupos de bases de datos elásticas mediante PowerShell, consulte [Creación de un nuevo grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md).

Si quiere consultar más información sobre cómo administrar grupos de bases de datos elásticas mediante PowerShell, consulte el artículo sobre [supervisión y administración de grupos de bases de datos elásticas con PowerShell](sql-database-elastic-pool-manage-powershell.md).



## Información relacionada

- [Cmdlets de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)

<!---HONumber=AcomDC_0914_2016-->