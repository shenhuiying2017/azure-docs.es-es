---
title: "Introducción a la copia de seguridad y la restauración de bases de datos SQL de Azure para la protección y la recuperación de los datos mediante Azure PowerShell | Microsoft Docs"
description: "En este tutorial se muestra cómo restaurar copias de seguridad automatizadas a un momento en el tiempo y cómo almacenar copias de seguridad automatizadas en el almacén de Azure Recovery Services y restaurarlas de él mediante PowerShell."
keywords: tutorial de base de datos sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 68a4ed7aad946dda644a0f085c48fd33f453e018
ms.openlocfilehash: 15d5cb803332133c8015a8ba23ca5751b8abc29a


---


# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery-using-powershell"></a>Introducción a la copia de seguridad y la restauración para la protección de datos y la recuperación mediante PowerShell

En este tutorial de introducción, aprenderá a usar Azure PowerShell para:

- Ver copias de seguridad existentes de una base de datos
- Restaurar una base de datos a un momento anterior en el tiempo
- Configurar la retención a largo plazo de un archivo de copia de seguridad de base de datos en el almacén de Azure Recovery Services
- Restaurar una base de datos del almacén de Azure Recovery Services

**Estimación del tiempo**: este tutorial se realiza en 30 minutos (suponiendo que ya se hayan cumplido los requisitos previos).


## <a name="prerequisites"></a>Requisitos previos

* Necesitará una cuenta de Azure. Puede [abrir una cuenta gratuita de Azure](/pricing/free-trial/?WT.mc_id=A261C142F) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Debe conectarse a Azure mediante una cuenta que sea miembro del rol de colaborador o propietario de la suscripción. Para más información sobre el acceso basado en roles (RBAC), consulte [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

* Debe disponer de la versión más reciente de Azure PowerShell instalada y ejecutándose. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

* Ha finalizado la [introducción a los servidores, las bases de datos y las reglas de firewalls de Azure SQL Database mediante Azure Portal y SQL Server Management Studio](sql-database-get-started.md) o la [versión de PowerShell](sql-database-get-started-powershell.md) equivalente. Si no es así, realice el tutorial que es requisito previo o ejecute el script de PowerShell al final de la [versión de PowerShell](sql-database-get-started-powershell.md) antes de continuar.

> [!TIP]
> Puede realizar las mismas tareas del tutorial de introducción con [Azure Portal](sql-database-get-started-backup-recovery.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Visualización del punto de restauración más antiguo de las copias de seguridad generadas por el servicio de una base de datos

En esta sección del tutorial, verá información sobre el punto de restauración más antiguo de las [copias de seguridad automatizadas generadas por el servicio](sql-database-automated-backups.md) de su base de datos. 

Puede restaurar una base de datos a un momento dado comprendido entre el punto de restauración más antiguo y la última copia de seguridad disponible (6 minutos antes de la hora actual). 

El fragmento de código siguiente usa el cmdlet [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) para obtener el punto de restauración más antiguo de la base de datos que desea restaurar. Se devuelve la hora como hora UTC, pero los fragmentos de código siguientes muestran cómo trabajar en hora local. El punto de restauración disponible más reciente de una base de datos suele ser aproximadamente de hace seis minutos, por lo que simplemente se establece el punto de restauración más reciente en la hora actual menos seis minutos. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restauración de una base de datos a un momento anterior en el tiempo

En esta sección del tutorial, restaurará la base de datos en una nueva base de datos a partir de un momento específico en el tiempo. 

>[!NOTE]
>No se puede cambiar el servidor en el que va a restaurar a un momento específico en el tiempo. Para restaurar en un servidor diferente, use [Georestauración](sql-database-disaster-recovery.md#recover-using-geo-restore). También, observe que la restauración se puede realizar en un [grupo de bases de datos elásticas](sql-database-elastic-jobs-overview.md) o en un plan de tarifa diferente. 

El fragmento de código siguiente utiliza el cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) para restaurar $databaseToRestore que se estableció en el fragmento de código anterior. El parámetro **-PointInTime** requiere un valor de hora UTC con formato similar a: *12/09/2016 20:00:00*. El fragmento de código convierte la hora local en su lugar.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> Desde aquí, puede conectarse a la base de datos restaurada mediante [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) para realizar las tareas necesarias, como [extraer un bit de datos de la base de datos restaurada para copiarlo en la base de datos existente o para eliminar la base de datos existente y cambiar el nombre de la base de datos restaurada por el nombre de la base de datos existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configuración de la retención a largo plazo de copias de seguridad automatizadas en un almacén de Azure Recovery Services 

En esta sección del tutorial, [configurará un almacén de Azure Recovery Services para conservar copias de seguridad automatizadas](sql-database-long-term-retention.md) durante un período más largo que el período de retención del nivel de servicio (hasta 10 años). 


> [!TIP]
> Para eliminar las copias de seguridad de retención a largo plazo, consulte [Delete long-term retention backups](sql-database-long-term-retention-delete.md) (Eliminación de copias de seguridad de retención a largo plazo).


### <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación

> [!IMPORTANT]
> El almacén debe estar ubicado en la misma región que el servidor lógico de Azure SQL y debe usar el mismo grupo de recursos que el servidor lógico.

El fragmento de código siguiente utiliza los cmdlets [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) y [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) para crear un nuevo almacén de Recovery Services y establecer el nivel de redundancia para las copias de seguridad en el almacén. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Establecimiento del servidor para que use el almacén de recuperación que acaba de crear para sus copias de seguridad de retención a largo plazo

El fragmento de código siguiente utiliza el cmdlet [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) para asociar el almacén de Recovery Services creado previamente a un servidor SQL específico de Azure.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Creación de una directiva de retención

Una directiva de retención permite establecer cuánto tiempo desea conservar una copia de seguridad de una base de datos. 

El fragmento de código siguiente utiliza el cmdlet [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) para obtener la directiva de retención predeterminada que se utiliza como plantilla para crear nuevas directivas. Establecemos nuestra plantilla para conservar la copia de seguridad durante dos años y, a continuación, se ejecuta el cmdlet [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) para crear finalmente la directiva. 

Tenga en cuenta que algunos cmdlets requieren que se establezca el contexto de almacén antes de ejecutarlos ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)) por lo que verá este cmdlet en algunos fragmentos de código relacionados. Establecemos el contexto porque la directiva forma parte del almacén. Puede crear varias directivas de retención para cada almacén y, a continuación, aplicar la directiva que desee a bases de datos específicas. 


```
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Configuración de una base de datos para que use la directiva de retención definida anteriormente

El fragmento de código siguiente utiliza el cmdlet [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) para aplicar la nueva directiva a una base de datos específica.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> Una vez configurado, las copias de seguridad se mostrarán en el almacén en los próximos siete días. Continúe con este tutorial una vez que las copias de seguridad se muestren en el almacén.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Visualización de la información sobre copias de seguridad y copias de seguridad con retención a largo plazo

En esta sección del tutorial, verá información sobre las copias de seguridad de base de datos con [retención a largo plazo](sql-database-long-term-retention.md). 

Los siguientes fragmentos de código realizan consultas sobre la información del almacén mediante los cmdlets [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) y [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint).

```
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restauración de una base de datos de una copia de seguridad con retención a largo plazo

En esta sección del tutorial, restaurará la base de datos en una nueva desde una copia de seguridad del almacén de Azure Recovery Services.

Al igual que sucedía con los fragmentos de código para un momento dado descritos anteriormente en este tutorial, la restauración a partir de directivas de retención de copias de seguridad a largo plazo usará el cmdlet [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) pero, en esta ocasión, utilizará el parámetro **-FromLongTermRetentionBackup** (en lugar del parámetro **-FromPointInTimeBackup** usado anteriormente).

```
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> Desde aquí, puede conectarse a la base de datos restaurada mediante SQL Server Management Studio para realizar las tareas necesarias, como [extraer un bit de datos de la base de datos restaurada para copiarlo en la base de datos existente o para eliminar la base de datos existente y cambiar el nombre de la base de datos restaurada por el nombre de la base de datos existente](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Script de Azure PowerShell completo para restaurar desde un momento dado anterior y para configurar y restaurar a partir de una directiva de retención de copia de seguridad a largo plazo mediante PowerShell

> [!NOTE]
> Al intentar restaurar la copia de seguridad más reciente al final de este script, obtendrá la excepción *No se puede indizar en una matriz nula* si no hay ninguna copia de seguridad en el almacén.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Pasos siguientes

- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).


<!--HONumber=Dec16_HO4-->


