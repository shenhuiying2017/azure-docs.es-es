---
title: "Eliminación de copias de seguridad de retención a largo plazo y el almacén de Azure Recovery Services | Microsoft Docs"
description: "Referencia rápida sobre cómo eliminar copias de seguridad de retención a largo plazo y el almacén de Azure Recovery Services"
services: sql-database
documentationcenter: 
author: stevestein
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
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 5e5a868ece1fc661b0bf1a83de5a9a30e9852fb1


---
# <a name="delete-long-term-retention-backups"></a>Eliminación de copias de seguridad de retención a largo plazo

En este tema, aprenderá cómo eliminar copias de seguridad de retención a largo plazo y el almacén de Azure Recovery Services.

>[!Warning]
>Compruebe que desea eliminar las copias de seguridad de retención a largo plazo antes de ejecutar este código. Para evitar cualquier cargo no deseado, este fragmento de código sirve para eliminar toda copia de seguridad y almacén que se haya creado cuando se aprendió a usar la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md) o las copias de seguridad y los almacenes que ya no necesita.

## <a name="delete-long-term-retention-backups-using-powershell"></a>Eliminación de copias de seguridad de retención a largo plazo mediante PowerShell

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

- Para aprender sobre las copias de seguridad automáticas generadas por el servicio, consulte [copias de seguridad automáticas](sql-database-automated-backups.md)
- Para más información sobre la retención de copia de seguridad a largo plazo, consulte sobre la [retención de copia de seguridad a largo plazo](sql-database-long-term-retention.md).
- Para aprender sobre la restauración a partir de copias de seguridad, consulte sobre la [restauración desde una copia de seguridad](sql-database-recovery-using-backups.md).


<!--HONumber=Jan17_HO2-->


