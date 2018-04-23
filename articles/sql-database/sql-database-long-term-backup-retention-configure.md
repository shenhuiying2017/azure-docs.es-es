---
title: Administración de la retención de copias de seguridad a largo plazo de Azure SQL Database | Microsoft Docs
description: Aprenda a almacenar copias de seguridad automatizadas en el almacenamiento de SQL Azure y, después, a restaurarlas
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 130b3ea0012c5fb21766b26ce2c3e589f0916736
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Administración de la retención de copias de seguridad a largo plazo de Azure SQL Database

Puede configurar Azure SQL Database con una directiva de [retención de copias de seguridad a largo plazo](sql-database-long-term-retention.md) (LTR) para conservar automáticamente las copias de seguridad en Azure Blob Storage hasta 10 años. Posteriormente, puede recuperar una base de datos mediante esas copias de seguridad con Azure Portal o PowerShell.

> [!NOTE]
> Como parte de la publicación inicial de la versión preliminar de esta característica en octubre de 2016, las copias de seguridad se almacenaban en el almacén del servicio Azure Recovery Services. En esta actualización se elimina esta dependencia pero por motivos de compatibilidad con versiones anteriores, se admite la API original hasta el 31 de mayo de 2018. Si necesita interactuar con copias de seguridad del almacén de Azure Recovery Services, consulte [Configuración y restauración de retención de copias de seguridad a largo plazo de Azure SQL Database mediante el almacén de Azure Recovery Services](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Uso de Azure Portal para configurar directivas de retención a largo plazo y restaurar las copias de seguridad

En las secciones siguientes se explica cómo usar Azure Portal para configurar la retención a largo plazo, ver las copias de seguridad incluidas en ella y restaurarlas.

### <a name="configure-long-term-retention-policies"></a>Configuración de directivas de retención a largo plazo

Puede configurar SQL Database para [ conservar las copias de seguridad automatizadas](sql-database-long-term-retention.md) durante un período superior al período de retención del nivel de servicio. 

1. En Azure Portal, seleccione el servidor SQL Server y, a continuación, haga clic en **Retención de copias de seguridad a largo plazo**.

   ![vínculo de retención de copia de seguridad a largo plazo](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. En la pestaña **Configurar directivas**, seleccione la base de datos en la que desea establecer o modificar las directivas de retención de copias de seguridad a largo plazo.

   ![seleccionar base de datos](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. En el panel **Configurar directivas**, seleccione si desea conservar las copias de seguridad semanales, mensuales o anuales y especifique el período de retención de cada una. 

   ![configurar directivas](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. Cuando haya terminado, haga clic en **Aplicar**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Visualización y restauración de copias de seguridad mediante Azure Portal

Vea las copias de seguridad que se han conservado para una base de datos concreta con una directiva LTR y realice la restauración a partir de esas copias de seguridad. 

1. En Azure Portal, seleccione el servidor SQL Server y, a continuación, haga clic en **Retención de copias de seguridad a largo plazo**.

   ![vínculo de retención de copia de seguridad a largo plazo](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. En la pestaña **Copias de seguridad disponibles**, seleccione la base de datos de la que desea ver las copias de seguridad disponibles.

   ![seleccionar base de datos](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. En el panel **Copias de seguridad disponibles**, revise las copias de seguridad disponibles. 

   ![ver copias de seguridad](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Seleccione la copia de seguridad desde la que desea restaurar y, a continuación, especifique el nuevo nombre de la base de datos.

   ![Restauración](./media/sql-database-long-term-retention/ltr-restore.png)

5. Haga clic en **Aceptar** para restaurar la base de datos de la copia de seguridad del almacenamiento de Azure SQL en la nueva base de datos.

6. En la barra de herramientas, haga clic en el icono de notificación para ver el estado del trabajo de restauración.

   ![progreso de trabajo de restauración del almacén](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Cuando se complete el trabajo de restauración, abra la página **Bases de datos SQL** para ver la base de datos recién restaurada.

> [!NOTE]
> Desde aquí, puede conectarse a la base de datos restaurada mediante SQL Server Management Studio para realizar las tareas necesarias, como [extraer un bit de datos de la base de datos restaurada para copiarlo en la base de datos existente o para eliminar la base de datos existente y cambiar el nombre de la base de datos restaurada por el nombre de la base de datos existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Uso de PowerShell para configurar directivas de retención a largo plazo y restaurar las copias de seguridad

En las siguientes secciones se explica cómo usar PowerShell para configurar la retención de copias de seguridad a largo plazo, ver las copias de seguridad en el almacén de Azure SQL y realizar una restauración a partir de una copia de seguridad del almacén de Azure SQL.

### <a name="create-an-ltr-policy"></a>Creación de una directiva LTR

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Visualización de directivas de LTR
Este ejemplo muestra cómo enumerar las directivas de LTR de un servidor

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Borrado de una directiva LTR
Este ejemplo muestra cómo borrar una directiva LTR desde una base de datos

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Visualización de copias de seguridad de LTR

Este ejemplo muestra cómo enumerar las copias de seguridad de LTR de un servidor. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Eliminación de copias de seguridad de LTR

En este ejemplo se muestra cómo eliminar una copia de seguridad de LTR desde la lista de copias de seguridad.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Restauración desde copias de seguridad de LTR
Este ejemplo muestra cómo restaurar desde una copia de seguridad de LTR. Observe que aunque esta interfaz no cambió, el parámetro ResourceId requiere ahora el identificador de recurso de la copia de seguridad de LTR. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Desde aquí, puede conectarse a la base de datos restaurada mediante SQL Server Management Studio para realizar las tareas necesarias, como extraer un bit de datos de la base de datos restaurada para copiarlo en la base de datos existente o para eliminar la base de datos existente y cambiar el nombre de la base de datos restaurada por el nombre de la base de datos existente. Consulte la [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Pasos siguientes

- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
