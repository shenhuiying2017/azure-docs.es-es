---
title: "Administración de Azure SQL Database con PowerShell | Microsoft Docs"
description: "Administración de Base de datos SQL de Azure con PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: a91b60f20394c236d64bfae242f820e56dd3ed79
ms.openlocfilehash: 83ff32bb99ba0cf08f61ba4f2a97dee74bd6e1c5


---
# <a name="managing-azure-sql-database-using-powershell"></a>Administración de Azure SQL Database mediante PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

En este tema se muestran los cmdlets de PowerShell que se usan para realizar numerosas tareas de Azure SQL Database. Para ver la lista completa, consulte [Azure SQL Database Cmdlets](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)(Cmdlets de Azure SQL Database).

## <a name="how-do-i-create-a-resource-group"></a>¿Cómo se crea un grupo de recursos?
Para crear un grupo de recursos para la base de datos SQL y los recursos de Azure relacionados, use el cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para obtener más información, vea [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="how-do-i-create-a-sql-database-server"></a>¿Cómo se crea un servidor de base de datos SQL?
Para crear un servidor de base de datos de SQL, use el cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx). Reemplace *server1* por el nombre del servidor. Los nombres de servidores deben ser únicos en todos los servidores de base de datos de Azure SQL. Recibirá un mensaje de error si ya existe el nombre del servidor. Este comando puede tardar varios minutos en completarse. El grupo de recursos ya debe existir en la suscripción.

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

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>¿Cómo se crea una regla de firewall de servidor de base de datos SQL?
Para crear una regla de firewall para acceder al servidor, use el cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Ejecute el comando siguiente, reemplazando las direcciones IP inicial y final con los valores válidos para el cliente. El grupo de recursos y el servidor ya deben existir en la suscripción.

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

Para permitir que otros servicios de Azure accedan al servidor, cree una regla de firewall y establezca `-StartIpAddress` y `-EndIpAddress` en**0.0.0.0**. Con esta acción se agrega una regla de firewall especial que permite el acceso a todo el tráfico de Azure.

Para obtener más información, consulte [Firewall de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="how-do-i-create-a-sql-database"></a>¿Cómo se crea una base de datos SQL?
Para crear una base de datos SQL, use el cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). El grupo de recursos y el servidor ya deben existir en la suscripción. 

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

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>¿Cómo se cambia el nivel de rendimiento de una base de datos SQL?
Para cambiar el nivel de rendimiento, escale o reduzca la base de datos verticalmente con el cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción. Establezca `-RequestedServiceObjectiveName` en un único espacio (como en el siguiente fragmento) para el nivel Básico. Para otros niveles, establézcalo en *S0*, *S1*, *P1*, *P6*, etc., como en el ejemplo anterior.

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

Si quiere obtener más información, consulte el artículo sobre el [rendimiento y las opciones de SQL Database para comprender lo que hay disponible en cada nivel de servicio](sql-database-service-tiers.md). Para ver un script de ejemplo, consulte [Script de PowerShell de ejemplo para cambiar el nivel de servicio y el nivel de rendimiento de su base de datos SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>¿Cómo se copia una base de datos SQL en el mismo servidor?
Para copiar una base de datos SQL en el mismo servidor, use el cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Establezca `-CopyServerName` y `-CopyResourceGroupName` en los mismos valores que el grupo de recursos y el servidor de bases de datos de origen.

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

Para más información, vea [Copiar una base de datos SQL de Azure](sql-database-copy.md). Para ver un script de ejemplo, consulte [Copia de una base de datos SQL con PowerShell](sql-database-copy-powershell.md#example-powershell-script).

## <a name="how-do-i-delete-a-sql-database"></a>¿Cómo se elimina una base de datos SQL?
Para eliminar una base de datos SQL, use el cmdlet [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). El grupo de recursos, el servidor y la base de datos ya deben existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>¿Cómo se elimina un servidor de base de datos SQL?
Para eliminar un servidor de base de datos SQL, use el cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-database-pools-using-powershell"></a>¿Cómo se crean y administran grupos de bases de datos elásticas mediante PowerShell?
Para obtener información sobre cómo crear grupos de bases de datos elásticas mediante PowerShell, consulte [Creación de un nuevo grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md).

Si quiere consultar más información sobre cómo administrar grupos de bases de datos elásticas mediante PowerShell, consulte el artículo sobre [supervisión y administración de grupos de bases de datos elásticas con PowerShell](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Información relacionada
* [Cmdlets de la Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Referencia de cmdlets de Azure](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Nov16_HO3-->


