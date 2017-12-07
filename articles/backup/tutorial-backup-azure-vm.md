---
title: "Copia de seguridad de máquinas virtuales de Azure en Azure a escala | Microsoft Docs"
description: "En este tutorial se detalla la copia de seguridad de varias máquinas virtuales de Azure en un almacén de Recovery Services."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "copia de seguridad de máquina virtual; realizar copia de seguridad de máquina virtual; copia de seguridad y recuperación ante desastres"
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Copia de seguridad de máquinas virtuales de Azure en Azure a escala

En este tutorial se detalla cómo realizar copias de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services. La mayor parte del trabajo de copia de seguridad de las máquinas virtuales es la preparación. Para poder realizar una copia de seguridad de una máquina virtual, o protegerla, es necesario reunir los [requisitos previos](backup-azure-arm-vms-prepare.md) a fin de preparar el entorno para la protección de las máquinas virtuales. 

> [!IMPORTANT]
> En este tutorial se da por supuesto que ya ha creado un grupo de recursos y una máquina virtual de Azure.

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

Un [almacén de Recovery Services](backup-azure-recovery-services-vault-overview.md) es un contenedor que incluye los puntos de recuperación de los elementos de los que se está realizando la copia de seguridad. Un almacén de Recovery Services es un recurso de Azure que se puede implementar y administrar como parte de un grupo de recursos de Azure. En este tutorial, creará un almacén de Recovery Services en el mismo grupo de recursos de la máquina virtual que se está protegiendo.


La primera vez que use Azure Backup, debe registrar el proveedor de Azure Recovery Services con su suscripción. Si ya ha registrado el proveedor con la suscripción, vaya al paso siguiente.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Cree un almacén de Recovery Services con el cmdlet **New-AzureRmRecoveryServicesVault**. Asegúrese de especificar el nombre del grupo de recursos y la ubicación usada para configurar la máquina virtual de la que quiere realizar una copia de seguridad. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Muchos de los cmdlets de Azure Backup requieren el objeto de almacén de Recovery Services como entrada. Por este motivo, es conveniente almacenar el objeto de almacén de Recovery Services de Backup en una variable. A continuación, use el cmdlet **Set-AzureRmRecoveryServicesBackupProperties** para establecer la opción **-BackupStorageRedundancy** en [Almacenamiento con redundancia geográfica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Copia de seguridad de máquinas virtuales de Azure

Para poder ejecutar la copia de seguridad inicial, debe establecer el contexto de almacén. El contexto de almacén es el tipo de los datos protegidos en el almacén. Cuando se crea un almacén de Recovery Services, este incluye las directivas de retención y protección predeterminadas. La directiva de protección predeterminada activa un trabajo de copia de seguridad cada día a la hora indicada. La directiva de retención predeterminada conserva el punto de recuperación diario durante 30 días. Para este tutorial, acepte la directiva predeterminada. 

Use el cmdlet **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** para establecer el contexto de almacén. Una vez que se haya establecido el contexto de almacén, se aplica a todos los cmdlets posteriores. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Use el cmdlet **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** para desencadenar el trabajo de copia de seguridad. El trabajo de copia de seguridad crea un punto de recuperación. Si se trata de la copia de seguridad inicial, el punto de recuperación es una copia de seguridad completa. Las sucesivas copias de seguridad crean una copia incremental.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Eliminación del almacén de Recovery Services

Para eliminar un almacén de Recovery Services, debe eliminar primero los puntos de recuperación en el almacén y, a continuación, anular el registro del almacén. Los comandos siguientes detallan estos pasos. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Solución de errores
Si se encuentra con problemas mientras realiza la copia de seguridad de la máquina virtual, consulte el artículo [Solución de problemas de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-troubleshoot.md) para obtener ayuda.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha protegido sus máquinas virtuales, consulte los siguientes artículos para obtener información sobre las tareas de administración de las máquinas virtuales y sobre cómo restaurarlas desde un punto de recuperación.

* Para modificar la directiva de copia de seguridad, consulte [Uso de los cmdlets AzureRM.RecoveryServices.Backup para realizar copias de seguridad de máquinas virtuales](backup-azure-vms-automation.md#create-a-protection-policy).
* [Administración y supervisión de las máquinas virtuales](backup-azure-manage-vms.md)
* [Restauración de máquinas virtuales](backup-azure-arm-restore-vms.md)
