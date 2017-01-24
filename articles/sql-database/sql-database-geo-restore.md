---
title: "Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad con redundancia geográfica | Microsoft Docs"
description: "Restaure una base de datos Azure SQL Database en un nuevo servidor a partir de una copia de seguridad con redundancia geográfica con Azure Portal o PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad con redundancia geográfica

En este artículo se muestra cómo restaurar la base de datos en un nuevo servidor mediante la georrestauración. Esto puede hacerse mediante Azure Portal o PowerShell.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Restauración de una base de datos Azure SQL Database a partir de una copia de seguridad con redundancia geográfica mediante Azure Portal

Para restaurar geográficamente una base de datos en Azure Portal, siga los pasos indicados a continuación:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. En el lado izquierdo de la pantalla, seleccione **+Nuevo** > **Bases de datos** > **SQL Database**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Seleccione **Copia de seguridad** como origen y, después, la copia de seguridad que desee restaurar. Especifique el nombre de la base de datos, el servidor en el que desee restaurar la base de datos y haga clic en **Crear**:
   
   ![Restauración de una base de datos SQL de Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Supervise el estado de la operación de restauración haciendo clic en el icono de notificación de la esquina superior derecha de la página.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restauración geográfica de una Base de datos SQL de Azure a partir de una copia de seguridad con redundancia geográfica mediante PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>Restauración geográfica de la base de datos en una base de datos independiente
1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar mediante el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>Restauración geográfica de la base de datos en un grupo elástico
1. Obtenga la copia de seguridad con redundancia geográfica de la base de datos que desee restaurar mediante el cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie la restauración de la copia de seguridad con redundancia geográfica mediante el cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Especifique el nombre del grupo que desea restaurar en la base de datos.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Información general sobre la continuidad empresarial](sql-database-business-continuity.md).
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md).
* Si quiere saber cómo usar las copias de seguridad automatizadas para procesos de recuperación, consulte [Recuperación de una base de datos a partir de copias de seguridad iniciadas por un servicio](sql-database-recovery-using-backups.md).
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte el procedimiento para [copiar una base de datos](sql-database-copy.md).




<!--HONumber=Dec16_HO3-->


