---
title: "Conversión de una VM con Windows de Unmanaged Disks a Managed Disks (Azure) | Microsoft Docs"
description: "Conversión de una VM con Windows de Unmanaged Disks a Azure Managed Disks mediante PowerShell en el modelo de implementación de Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Conversión de una VM con Windows de Unmanaged Disks a Azure Managed Disks

Si ya dispone de máquinas virtuales (VM) Windows que usan Unmanaged Disks, puede convertirlas para usar [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Este proceso convierte el disco del SO y los discos de datos conectados.

En este artículo se explica cómo convertir máquinas virtuales con Azure PowerShell. Si necesita instalarlas o actualizarlas, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps.md) (Instalación y configuración de Azure PowerShell).

## <a name="before-you-begin"></a>Antes de empezar


* Revise [Planeación de la migración a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Conversión de máquinas virtuales de instancia única
En esta sección se explica cómo convertir máquinas virtuales de Azure de instancia única de Unmanaged Disks a Managed Disks. (Vea la sección siguiente si las máquinas virtuales se encuentran en un conjunto de disponibilidad). 

1. Desasigne la máquina virtual con el cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). En el ejemplo siguiente se desasigna la VM `myVM` en el grupo de recursos denominado `myResourceGroup`: 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convierta la VM a Managed Disks con el cmdlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). El proceso siguiente convierte la VM anterior, incluidos el disco del SO y todos los discos de datos:

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Inicie la VM después de realizar la conversión a Managed Disks con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). En el ejemplo siguiente se reinicia el VM anterior:

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Conversión de VM de un conjunto de disponibilidad

Si las VM que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

1. Convierta el conjunto de disponibilidad con el cmdlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). En el ejemplo siguiente se actualiza el conjunto de disponibilidad denominado `myAvailabilitySet` en el grupo de recursos con nombre `myResourceGroup`:

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Si la región en que se encuentra el conjunto de disponibilidad solo tiene 2 dominios de error administrados, pero el número de dominios de error no administrados es 3, este comando muestra un error similar a "El número de dominios de error especificado 3 debe estar en el intervalo de 1 a 2". Para resolver el error, actualice el dominio predeterminado a 2 y actualice `Sku` a `Aligned` como sigue:

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Desasigne y convierta las máquinas virtuales del conjunto de disponibilidad. El siguiente script desasigna cada VM con el cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la convierte con [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) y la reinicia con [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Conversión de Managed Disks estándar a premium
Después de haber convertido la VM a Managed Disks, también puede alternar entre los tipos de almacenamiento. También puede tener una mezcla de discos que usan almacenamiento premium y estándar. En el ejemplo siguiente, se muestra cómo alternar del almacenamiento estándar al premium. Para usar Managed Disks premium, la máquina virtual debe usar un [Tamaño de máquina virtual](sizes.md) que admita Premium Storage. En este ejemplo también se pasa a un tamaño que admite Premium Storage.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>Solución de problemas

Si se produce un error durante la conversión, o si una VM presenta un estado de error debido a errores en una conversión anterior, ejecute el cmdlet `ConvertTo-AzureRmVMManagedDisk` de nuevo. Normalmente, un simple reintento desbloquea la situación.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks y Azure Storage Service Encryption

No puede usar los pasos anteriores para convertir un disco no administrado en uno administrado si el disco no administrado se encuentra en una cuenta de Storage cifrada con [Azure Storage Service Encryption](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Los siguientes pasos explican cómo copiar y usar Unmanaged Disks que han estado en una cuenta de almacenamiento cifrada:

1. Copie el disco duro virtual (VHD) con [AzCopy](../../storage/storage-use-azcopy.md) en una cuenta de Storage que nunca se ha habilitado para Azure Storage Service Encryption.

2. Use la VM copiada de alguna de las formas siguientes:

  * Cree una VM que use Unmanaged Disks y especifique el archivo de VHD durante la creación con `New-AzureRmVm`.

  * Conecte el VHD copiado con `Add-AzureRmVmDataDisk` a una VM en ejecución con Managed Disks.


## <a name="next-steps"></a>Pasos siguientes

Realizar una copia de solo lectura de una máquina virtual mediante [instantáneas](snapshot-copy-managed-disk.md).


