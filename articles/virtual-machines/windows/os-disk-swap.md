---
title: Intercambio de discos del sistema operativo en una máquina virtual de Azure con PowerShell | Microsoft Docs
description: Intercambie el disco del sistema operativo que se usa en una máquina virtual de Azure mediante PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195958"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Intercambio del disco del sistema operativo que se usa en una máquina virtual de Azure mediante PowerShell

Si ya tiene una máquina virtual, pero quiere intercambiar el disco por uno de copia de seguridad u otro disco del sistema operativo, puede usar Azure PowerShell para intercambiar los discos del sistema operativo. No es necesario eliminar y volver a crear la máquina virtual. Incluso puede utilizar un disco administrado de otro grupo de recursos, siempre y cuando no esté en uso.

Es necesario que la máquina virtual esté detenida o sin asignar; a continuación, el identificador de recurso del disco administrado se puede reemplazar por el identificador de recurso de otro disco administrado.

Asegúrese de que el tipo de almacenamiento y el tamaño de máquina virtual sean compatibles con el disco que quiere asociar. Por ejemplo, si el disco que quiere usar está en Premium Storage, la máquina virtual debe ser compatible con Premium Storage (por ejemplo, debe tener un tamaño de la serie DS). 

Obtenga una lista de discos de un grupo de recursos mediante [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk).

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Cuando tenga el nombre del disco que quiera usar, establézcalo como el disco del sistema operativo para la máquina virtual. En este ejemplo se detiene o desasigna la máquina virtual llamada *myVM* y se asigna el disco llamado *newDisk* como el nuevo disco del sistema operativo. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Pasos siguientes**

Para crear una copia de un disco, consulte [Instantánea de un disco](snapshot-copy-managed-disk.md).