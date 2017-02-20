---
title: "PowerShell: Creación y administración de servidores de Azure SQL Database | Microsoft Docs"
description: "Referencia rápida sobre cómo crear y administrar servidores de Azure SQL Database con PowerShell."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 835702c8381fcd184b056c76054514348c675bbf
ms.openlocfilehash: 9f1905f7e5aad0e43d81f7095089b9f3492a82ea


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>Creación y administración de servidores de Azure SQL Database con PowerShell

Puede crear y administrar un servidor de Azure SQL Database mediante [Azure Portal](https://portal.azure.com/), PowerShell, la API de REST o C#. Este tema trata del uso de PowerShell. Para Azure Portal, consulte [Creación y administración de servidores con Azure Portal](sql-database-manage-servers-portal.md). 

## <a name="create-an-azure-sql-database-server-using-powershell"></a>Creación de un servidor de Azure SQL Database mediante PowerShell

Para crear un servidor de base de datos de SQL, use el cmdlet [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver). Reemplace *server1* por el nombre del servidor. Los nombres de servidores deben ser únicos en todos los servidores de base de datos de Azure SQL. Recibirá un mensaje de error si ya existe el nombre del servidor. Este comando puede tardar varios minutos en completarse. El grupo de recursos ya debe existir en la suscripción.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> Para ver un script de ejemplo, consulte [Creación de una instancia de SQL Database con un script de PowerShell](sql-database-get-started-powershell.md).
>

## <a name="additional-resources"></a>Recursos adicionales
* Para ver información general de las herramientas de administración, consulte el [artículo al respecto](sql-database-manage-overview.md).
* Para ver cómo realizar tareas de administración con Azure Portal, lea [Administración de instancias de Azure SQL Database mediante Azure Portal](sql-database-manage-portal.md).
* Para ver cómo realizar otras tareas de administración con PowerShell, visite [Administración de Azure SQL Database mediante PowerShell](sql-database-manage-powershell.md).
* Para ver cómo realizar otras tareas con SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obtener más información sobre el servicio SQL Database, lea el [artículo en el que se describe qué es SQL Database](sql-database-technical-overview.md). 
* Para obtener más información sobre las características y los servidores de bases de datos de Azure SQL Database, visite [el artículo al respecto](sql-database-features.md).



<!--HONumber=Feb17_HO1-->


