---
title: "Guía de inicio rápido de Azure: copia de seguridad una máquina virtual con PowerShell | Microsoft Docs"
description: "Aprenda a realizar copias de seguridad de sus máquinas virtuales con Azure PowerShell"
services: virtual-machines-windows, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-windows, azure-backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 98a86652b13f62ef7acade9eb69e81852b2fc091
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Copia de seguridad de una máquina virtual en Azure con PowerShell
El módulo Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Para proteger sus datos realice copias de seguridad a intervalos regulares. Azure Backup crea puntos de recuperación que se guardan en almacenes de recuperación con redundancia geográfica. En este artículo se explica cómo realizar una copia de seguridad de una máquina virtual (VM) con el módulo Azure PowerShell. Estos pasos también se pueden llevar a cabo estos pasos con la [CLI de Azure](quick-backup-vm-cli.md) o [Azure Portal](quick-backup-vm-portal.md).

Este inicio rápido permite realizar copias de seguridad en una máquina virtual de Azure existente. Si necesita crear una máquina virtual, puede [crearla con Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Para realizar este tutorial de inicio rápido se requiere la versión 4.4 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).


## <a name="log-in-to-azure"></a>Inicie sesión en Azure.
Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

La primera vez que use Azure Backup, debe registrar el proveedor de Azure Recovery Services en su suscripción con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación
Un almacén de Recovery Services es un contenedor lógico que almacena los datos de copia de seguridad de los recursos protegidos, como las máquinas virtuales de Azure. Cuando se ejecuta el trabajo de copia de seguridad para un recurso protegido, crea un punto de recuperación en el almacén de Recovery Services. Posteriormente, se puede usar uno de estos puntos de recuperación para restaurar los datos a un momento dado en el tiempo.

Cree un almacén de Recovery Services con el cmdlet [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Especifique el mismo grupo de recursos y ubicación que tenga la máquina virtual que desea proteger. Si ha usado el [script de ejemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para crear la máquina virtual, el grupo de recursos se denomina *myResourceGroup*, la máquina virtual se denomina *myVM* y los recursos están en la ubicación *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

De forma predeterminada, el almacén se establece para el almacenamiento con redundancia geográfica. Para proteger aún más los datos, este nivel de redundancia del almacenamiento garantiza que los datos de las copia de seguridad se replican en una región de Azure secundaria que se encuentra a cientos de kilómetros de la región primaria.

Para usar este almacén con los pasos restantes, establezca el contexto del almacén con [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext)

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Habilitación de la copia de seguridad de una máquina virtual de Azure
Cree y use directivas para definir cuándo se ejecuta un trabajo de copia de seguridad y durante cuánto tiempo se almacenan los puntos de recuperación. La directiva de protección predeterminada ejecuta un trabajo de copia de seguridad cada día y conserva los puntos de seguridad durante 30 días. Estos valores de la directiva predeterminada se pueden usar para proteger rápidamente la máquina virtual. En primer lugar, establezca la directiva predeterminada con [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Para habilitar la protección mediante copia de seguridad para una máquina virtual, use [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Especifique la directiva que se va a usar y, después, el grupo de recursos y la máquina virtual que se van a proteger:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Inicio de un trabajo de copia de seguridad
Para iniciar una copia de seguridad ahora, en lugar de esperar a que la directiva predeterminada ejecute el trabajo en el momento programado, utilice [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). El primer trabajo de copia de seguridad crea un punto de recuperación completa. Cada uno de los trabajo de copia de seguridad posteriores a esta copia de seguridad inicial crea puntos de recuperación incremental. Los puntos de recuperación incremental ahorran tiempo y espacio de almacenamiento, ya que solo transfieren los cambios realizados desde la última copia de seguridad.

En el siguiente conjunto de comandos, especifique un contenedor en el almacén de Recovery Services que contenga los datos de copia de seguridad con [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Cada máquina virtual de la que se va a realizar una copia se trata como un elemento. Para iniciar un trabajo de copia de seguridad, obtenga información sobre el elemento de máquina virtual con [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Como este primer trabajo de copia de seguridad crea un punto de recuperación completa, el proceso puede tardar hasta 20 minutos.


## <a name="monitor-the-backup-job"></a>Supervisión del trabajo de copia de seguridad
Para supervisar el estado de los trabajos de copia de seguridad, utilice [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

La salida es similar al ejemplo siguiente, que muestra que el estado del trabajo de copia de seguridad es **InProgress**:

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Cuando el apartado *Status* (Estado) del trabajo de copia de seguridad muestre *Completed* (Completado), la máquina virtual estará protegida con Recovery Services y tendrá almacenado un punto de recuperación completa.


## <a name="clean-up-deployment"></a>Limpieza de la implementación
Cuando deje de ser necesaria, puede deshabilitar la protección en la máquina virtual, quitar los puntos de restauración y el almacén de Recovery Services, y eliminar tanto el grupo de recursos como los recursos de la máquina virtual asociados. Si ha usado una máquina virtual existente, puede omitir el último cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para dejar tanto el grupo de recursos como la máquina virtual en su lugar.

Si va a continuar con un tutorial de Backup en el que se explique cómo restaurar los datos en una máquina virtual, omita los pasos de esta sección y vaya a [Pasos siguientes](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Pasos siguientes
En esta guía de inicio rápido, ha creado un almacén de Recovery Services, habilitado la protección en una máquina virtual y creado el punto de recuperación inicial. Para más información acerca de Azure Backup, y Recovery Services, continúe con los tutoriales.

> [!div class="nextstepaction"]
> [Copia de seguridad de varias máquinas virtuales de Azure](./tutorial-backup-vm-at-scale.md)