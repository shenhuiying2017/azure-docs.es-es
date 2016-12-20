---
title: "Restauración de una base de datos Azure SQL a partir de una copia de seguridad con redundancia geográfica (PowerShell) | Microsoft Docs"
description: "Restauración de una Base de datos SQL de Azure en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4fe8bc103856f891d3b29fa77f5c43dd0c403e8f


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell
> [!div class="op_single_selector"]
> * [Información general](sql-database-recovery-using-backups.md)
> * [Restauración geográfica: Portal de Azure](sql-database-geo-restore-portal.md)
> 
> 

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la georrestauración. Esto puede hacerse a través de PowerShell.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Restauración geográfica de la base de datos en una base de datos independiente
1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar mediante el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Restauración geográfica de la base de datos en un grupo de bases de datos elásticas
1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar mediante el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Especifique el nombre del grupo que desea restaurar en la base de datos.
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para obtener información sobre las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recover an Azure SQL database using automated database backups](sql-database-recovery-using-backups.md)(Recuperación de una base de datos SQL de Azure mediante copias de seguridad de base de datos automatizadas).
* Para conocer las opciones de recuperación más rápidas, consulte el artículo sobre [replicación geográfica activa](sql-database-geo-replication-overview.md).  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el artículo de [copia de bases de datos](sql-database-copy.md).




<!--HONumber=Nov16_HO3-->


