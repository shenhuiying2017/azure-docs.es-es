---
title: "Azure PowerShell: Creación de una instancia de SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos en Azure Portal."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/17/2017
ms.author: carlrab
ms.openlocfilehash: 68f7ea0f5255602629a0b0960726c30dd508a655
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>Creación de una sola instancia de Azure SQL Database con PowerShell

PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se detalla el uso de PowerShell para implementar una instancia de Azure SQL Database en un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md), en un [servidor lógico de Azure SQL Database](sql-database-features.md).

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

Para realizar este tutorial es necesaria la versión 4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) y siga las instrucciones de la pantalla.

```powershell
Add-AzureRmAccount
```

## <a name="create-variables"></a>Creación de variables

Defina variables para su uso en los scripts con esta guía de inicio rápido.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un [servidor lógico de Azure SQL Database](/powershell/module/azurerm.sql/new-azurermsqlserver) con el comando [New-AzureRmSqlServer](sql-database-features.md). Un servidor lógico contiene un conjunto de bases de datos administradas como un grupo. En el ejemplo siguiente se crea un servidor con nombre aleatorio en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Para crear una [regla de firewall de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md), ejecute el comando [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Database a través del firewall del servicio de SQL Database. En el ejemplo siguiente, el firewall está abierto solo para otros recursos de Azure. Para habilitar la conectividad externa, cambie la dirección IP a una dirección apropiada para su entorno. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor de Azure SQL Database, a menos que el departamento de TI abra el puerto 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Creación de una base de datos en el servidor con datos de ejemplo

Cree una base de datos vacía con un [nivel de rendimiento S0](sql-database-service-tiers.md) en el servidor mediante el comando [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase). En el ejemplo siguiente se crea una base de datos llamada `mySampleDatabase` y se cargan en ella los datos del ejemplo AdventureWorksLT. Estos valores predefinidos se pueden reemplazar si se desea (otras guías de inicio rápido de esta colección se basan en los valores de esta).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. 

> [!TIP]
> Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido, no elimine los recursos creados en esta guía de inicio rápido. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos creados por esta guía de inicio rápido en Azure Portal.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una base de datos, puede conectarse y realizar consultas con las herramientas que desee. Para más información, seleccione una de las herramientas siguientes:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [código de Visual Studio](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)

