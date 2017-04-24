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
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: bb8fcd907a03350dc21106944e72e6f06109b5f6
ms.lasthandoff: 04/18/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Creación de una sola instancia de Azure SQL Database con PowerShell

PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se detalla el uso de PowerShell para implementar una instancia de Azure SQL Database en un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md), en un [servidor lógico de Azure SQL Database](sql-database-features.md).

Para completar este tutorial, asegúrese de que ha instalado la versión más reciente de [Azure PowerShell](/powershell/azureps-cmdlets-docs). 

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) y siga las instrucciones de la pantalla.

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
$endip = "0.0.0.1"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) con el comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran recursos de Azure como un grupo. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un [servidor lógico de Azure SQL Database](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) con el comando [New-AzureRmSqlServer](sql-database-features.md). Un servidor lógico contiene un conjunto de bases de datos administradas como un grupo. En el ejemplo siguiente se crea un servidor con nombre aleatorio en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Para crear una [regla de firewall de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md), ejecute el comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Database a través del firewall del servicio de SQL Database. En el ejemplo siguiente, el firewall está abierto solo para otros recursos de Azure. Para habilitar la conectividad externa, cambie la dirección IP a una dirección apropiada para su entorno. Para abrir todas las direcciones IP, utilice 0.0.0.0 como la dirección IP inicial y 255.255.255.255 como la dirección final.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. Si es así, no podrá conectarse al servidor de Azure SQL Database, a menos que el departamento de TI abra el puerto 1433.
>

## <a name="create-a-blank-database"></a>Crear una base de datos en blanco

Cree una instancia de SQL Database vacía con un [nivel de rendimiento S0](sql-database-service-tiers.md) en el servidor con el comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). En el ejemplo siguiente se crea una base de datos denominada `mySampleDatabase`. Cambie este valor predefinido por el que prefiera.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName databasename `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Otras guías de inicio rápido de esta colección se basan en los valores de esta. Si tiene previsto seguir trabajando con las siguientes guías de inicio rápido o tutoriales, no elimine los recursos creados en esta guía de inicio rápido. Si no va a continuar, use este comando para eliminar todos los recursos creados mediante esta guía de inicio rápido.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Pasos siguientes

- Para conectarse y consultar mediante SQL Server Management Studio, consulte el artículo de [Conexión y consultas con SSMS](sql-database-connect-query-ssms.md).
- Para conectarse y consultar con Visual Studio Code, vea [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectarse y consultar con .NET, vea [Conexión y consultas con .NET](sql-database-connect-query-dotnet.md).
- Para conectarse y consultar con PHP, vea [Conexión y consultas con PHP](sql-database-connect-query-php.md).
- Para conectarse y consultar con Node.js, vea [Conexión y consultas con Node.js](sql-database-connect-query-nodejs.md).
- Para conectarse y consultar con Java, vea [Conexión y consultas con Java](sql-database-connect-query-java.md).
- Para conectarse y consultar con Python, vea [Conexión y consultas con Python](sql-database-connect-query-python.md).
- Para conectarse y consultar con Ruby, vea [Conexión y consultas con Ruby](sql-database-connect-query-ruby.md).

