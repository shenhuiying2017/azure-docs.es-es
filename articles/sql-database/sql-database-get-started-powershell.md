---
title: "Azure PowerShell: creación de instancias únicas de SQL Database | Microsoft Docs"
description: "Aprenda a crear un servidor lógico de SQL Database, una regla de firewall de nivel de servidor y bases de datos en Azure Portal."
keywords: "tutorial de base de datos SQL, creación de una base de datos SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fe527f7de573b87fbc644cb6d71ae13816bc284b
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>Creación de una sola instancia de Azure SQL Database con PowerShell

PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. En esta guía se detalla el uso de PowerShell para implementar una instancia de Azure SQL Database en un grupo de recursos de Azure de un servidor lógico de SQL Database.

Antes de empezar, asegúrese de tener instalada la versión más reciente de PowerShell. Se ha instalado la CLI de Azure. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando [Add-AzureRmAccount](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) y siga las instrucciones de la pantalla.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westeurope`.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>un servidor lógico

Cree un servidor lógico de SQL Database con el comando [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver). En el ejemplo siguiente se crea un servidor denominado aleatoriamente en el grupo de recursos con un inicio de sesión de administrador denominado `ServerAdmin` y una contraseña `ChangeYourAdminPassword1`. Cambie estos valores predefinidos por los que prefiera.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Configuración de una regla de firewall del servidor

Para crear una regla de firewall de nivel de servidor de SQL Database, ejecute el comando [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule). Una regla de firewall de nivel de servidor permite a una aplicación externa, como SQL Server Management Studio o la utilidad SQLCMD conectarse a una instancia de SQL Database a través del firewall del servicio de SQL Database. En el ejemplo siguiente se crea una regla de firewall para un intervalo de direcciones predefinidas que, en este ejemplo, es todo el intervalo de direcciones IP posible. Cambie estos valores predefinidos por los valores de su dirección IP o intervalo de direcciones IP externas. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Crear una base de datos en blanco

Cree una instancia de SQL Database en blanco con un nivel de rendimiento S0 en el servidor con el comando [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase). En el ejemplo siguiente se crea una base de datos denominada `mySampleDatabase`. Cambie este valor predefinido por el que prefiera.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Para quitar todos los recursos creados por este tutorial de inicio rápido, ejecute el siguiente comando:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Pasos siguientes

- Para conectarse y consultar mediante SQL Server Management Studio, consulte el artículo de [Conexión y consultas con SSMS](sql-database-connect-query-ssms.md).
- Para conectarse con Visual Studio, consulte el artículo de [Conexión y consultas con Visual Studio](sql-database-connect-query.md).
* Para información técnica general de SQL Database, consulte el artículo [Acerca del servicio SQL Database](sql-database-technical-overview.md).

