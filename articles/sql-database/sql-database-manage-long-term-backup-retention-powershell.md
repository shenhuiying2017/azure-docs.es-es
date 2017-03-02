---
title: "PowerShell: administración de la retención de copias de seguridad de bases de datos a largo plazo (Azure) | Microsoft Docs"
description: "Referencia rápida sobre cómo configurar, administrar y restaurar desde la retención a largo plazo de copias de seguridad automatizadas de base de datos de Azure SQL Database en un almacén de Azure Recovery Services con PowerShell"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: carlrab; sstein
translationtype: Human Translation
ms.sourcegitcommit: f234e50d194c1b8b8deed8282a6ab1250b53d075
ms.openlocfilehash: c0f073578ecf067d115a143edf4e797c4e8b5200
ms.lasthandoff: 02/16/2017


---
# <a name="configure-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-powershell"></a>Configure la retención a largo plazo de copias de seguridad de bases de datos en un almacén de Azure Recovery Services mediante PowerShell
En este tema, descubrirá cómo configurar, administrar y restaurar la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services mediante PowerShell. También puede llevar a cabo esta tarea con [Azure Portal](sql-database-manage-long-term-backup-retention-portal.md).

Para obtener más información sobre la retención de copias de seguridad a largo plazo, consulte [este artículo sobre la retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).

> [!TIP]
> Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-powershell.md).
>

## <a name="configure-long-term-backup-retention-using-powershell"></a>Configuración de la retención de copia de seguridad a largo plazo mediante PowerShell

Para configurar la retención a largo plazo, se requiere la versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) para llevar a cabo los pasos siguientes:

1. Crear un almacén de Azure Recovery Services en la misma región, suscripción y grupo de recursos que el servidor de SQL Database. ([New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault), [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties))
2. Registrar el servidor SQL de Azure en el almacén ([Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault))
3. Crear una directiva de retención ([New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy))
4. Aplicar la directiva de protección a las bases de datos que requieren la retención de copia de seguridad a largo plazo ([Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy))

Para más información, consulte [Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo](sql-database-long-term-retention.md).

Para ver un tutorial, consulte [Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell](sql-database-get-started-backup-recovery-powershell.md).

```
# Configure long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseToBackup = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location


# Create an Azure recovery services vault
#########################################
$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault


# Register your Azure SQL server to the vault
#############################################
Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Create a retention policy 
###########################
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values to 1 year (set to anytime between 1 week and 10 years)
#################################################################################
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 1

$retentionPolicyName = "myOneYearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
####################################################################
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
#######################
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy


# Apply the retention policy to the database to backup 
######################################################
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseToBackup -State "enabled" -ResourceId $policy.Id
```

## <a name="restore-from-long-term-backup-retention-using-powershell"></a>Restauración desde la retención de copias de seguridad a largo plazo mediante PowerShell

Para restaurar una base de datos desde una copia de seguridad de retención a largo plazo, se requiere la versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/). Se usan los siguientes cmdlets:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)
- [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase)


```
# Restore a database from long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseNeedingRestore = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$restoredDatabaseName = "{restored-db-name}"
$edition = "{restored-db-edition}"
$performanceLevel = "{restored-db-slo}"


# Set the vault context to the vault we want to restore from
############################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
##########################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters for specific backups
#############################################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups

if (!$availableBackups)
{
    Write-Host "No backups available"
}
else
{
    # Restore the most recent available backup: $availableBackups[0]
    ################################################################
    $restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName ` 
     -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
    $restoredDb
}
```

## <a name="view-long-term-backup-retention-information-using-the-powershell"></a>Visualización de la información sobre la retención de copias de seguridad a largo plazo mediante PowerShell

Para ver las copias de seguridad en retención a largo plazo, se requiere la versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) y se usan los siguientes cmdlets:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)

Para más información, consulte [Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo](sql-database-long-term-retention.md).

```
# View the available backups in long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$recoveryServiceVaultName = "{vault-name}"

# Set the vault context to the vault we want to query
#####################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with the container
####################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase


# Get all available backups
# Optionally, set the -StartDate and -EndDate parameters
########################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

## <a name="delete-long-term-retention-backups-using-powershell"></a>Eliminación de copias de seguridad de retención a largo plazo mediante PowerShell

>[!Warning]
>Compruebe que desea eliminar las copias de seguridad de retención a largo plazo antes de ejecutar este código. Para evitar cualquier cargo no deseado, este fragmento de código sirve para eliminar toda copia de seguridad y almacén que se haya creado cuando se aprendió a usar la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md) o las copias de seguridad y los almacenes que ya no necesita.

Para eliminar las copias de seguridad en retención a largo plazo, se requiere la versión más reciente de [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) y se usan los siguientes cmdlets:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Disable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/disable-azurermrecoveryservicesbackupprotection)
- [Unregister-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/unregister-azurermrecoveryservicesbackupcontainer)
- [Remove-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/remove-azurermrecoveryservicesvault)


Para más información, consulte [Almacenamiento de copias de seguridad de Azure SQL Database durante diez años como máximo](sql-database-long-term-retention.md).

```
# Delete long-term backups
##########################

$resourceGroupName = "{resource-group-name}"
$recoveryServiceVaultName = "{vault-name}"

$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

ForEach ($container in $containers)
{
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase

   ForEach ($item in $items)
   {
      # Remove the backups from the vault
      ###################################
      Disable-AzureRmRecoveryServicesBackupProtection -item $item -RemoveRecoveryPoints -ea SilentlyContinue
   }
   
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
# Delete the recovery services vault
####################################
Remove-AzureRmRecoveryServicesVault -Vault $vault
```

## <a name="next-steps"></a>Pasos siguientes

- Para administrar las copias de seguridad de retención de copias de seguridad a largo plazo mediante Azure Portal, consulte [Administración de la retención de copias de seguridad a largo plazo mediante Azure Portal](sql-database-manage-long-term-backup-retention-portal.md).
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
