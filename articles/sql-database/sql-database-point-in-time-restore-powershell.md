---
title: "PowerShell: Restauración de una base de datos de Azure SQL Database a un momento dado anterior | Microsoft Docs"
description: "Restauración de una base de datos de Azure SQL Database a un momento dado anterior mediante PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 5e5f0a474b6a34581be804b5d18d6ae03c99c14d
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Restauración de una Base de datos SQL de Azure a un momento dado anterior con PowerShell

En este artículo se muestra cómo restaurar la base de datos a un momento dado anterior a partir de las [copias de seguridad automatizadas de SQL Database](sql-database-automated-backups.md) mediante PowerShell. Esta tarea también se puede realizar también [mediante Azure Portal](sql-database-point-in-time-restore-portal.md).  

## <a name="restore-to-a-previous-point-in-time"></a>Restauración a un momento dado anterior 

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-powershell.md).
>


## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Restauración a un momento dado anterior con PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Restauración de su base de datos a un momento dado anterior como una base de datos única
1. Obtenga la base de datos que quiere restaurar con el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure la base de datos a un momento específico con el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Restauración de su base de datos a un momento dado en un grupo elástico
1. Obtenga la base de datos que quiere restaurar con el cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure la base de datos a un momento específico con el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Pasos siguientes
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md)
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md)


