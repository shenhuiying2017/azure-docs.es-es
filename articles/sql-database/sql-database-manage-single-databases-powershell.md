---
title: "PowerShell: Creación y administración de instancias de Azure SQL Database únicas | Microsoft Docs"
description: "Referencia rápida sobre cómo crear y administrar una instancia de Azure SQL Database única con Azure Portal"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>Creación y administración de instancias de Azure SQL Database únicas con PowerShell

Puede crear y administrar instancias de Azure SQL Database únicas con [Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, la API de REST o C#. Este tema trata del uso de PowerShell. Para Azure Portal, consulte [Creación y administración de bases de datos únicas con Azure Portal](sql-database-manage-single-databases-powershell.md). Para Transact-SQL, consulte [Creación y administración de bases de datos únicas con Transact-SQL](sql-database-manage-single-databases-tsql.md). 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>Creación de una base de datos SQL de Azure con PowerShell

Para crear una base de datos SQL, use el cmdlet [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). El grupo de recursos y el servidor ya deben existir en la suscripción. 

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

> [!TIP]
> Para ver un script de ejemplo, consulte [Creación de un script de PowerShell de SQL Database](sql-database-get-started-powershell.md).
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Cambio del nivel de servicio y el nivel de rendimiento de una base de datos única

Ejecute el cmdlet [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) y establezca el elemento **-RequestedServiceObjectiveName** en el nivel de rendimiento del plan de tarifa que quiera; por ejemplo: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*...

Reemplace ```{variables}``` con sus valores (no incluya las llaves de cierre).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver información general de las herramientas de administración, consulte el [artículo al respecto](sql-database-manage-overview.md).
* Para ver cómo realizar tareas de administración con Azure Portal, lea [Administración de instancias de Azure SQL Database mediante Azure Portal](sql-database-manage-portal.md).
* Para ver cómo realizar otras tareas de administración con PowerShell, visite [Administración de Azure SQL Database mediante PowerShell](sql-database-manage-powershell.md).
* Para ver cómo realizar tareas de administración con SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obtener más información sobre el servicio SQL Database, lea el [artículo en el que se describe qué es SQL Database](sql-database-technical-overview.md). 
* Para obtener más información sobre las características y los servidores de bases de datos de Azure SQL Database, visite [el artículo al respecto](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


