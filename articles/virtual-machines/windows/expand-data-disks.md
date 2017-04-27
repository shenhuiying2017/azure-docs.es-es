---
title: "Ampliación de un disco de datos conectado a una máquina virtual Windows en Azure | Microsoft Docs"
description: "Amplíe el tamaño de un disco de datos conectado a una máquina virtual Windows con PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 620a28f4fb4421179c0ba030c10acba861760adf
ms.lasthandoff: 04/21/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Aumento del tamaño de un disco de datos conectado a una máquina virtual Windows

Si necesita aumentar el tamaño del disco de datos conectado a la máquina virtual, puede hacerlo PowerShell. Después de aumentar el tamaño del disco de datos en la configuración de máquinas virtuales de Azure, debe asignar el nuevo espacio en disco en la máquina virtual.


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Uso de PowerShell para aumentar el tamaño de un disco de datos administrado

Para aumentar el tamaño de un disco de datos administrado, use los siguientes cmdlets de PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                        | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                    | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |
<br>

El script siguiente lo guiará a través del proceso de obtener la información de la máquina virtual, seleccionar el disco de datos y especificar el nuevo tamaño.

```powershell
# Select resource group

    $rg = Get-AzureRMReseourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru

    $rgName = $rg.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk

    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru

# Specify a larger size for the data disk

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)

    $vm.StorageProfile.DataDisks

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM

    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Uso de PowerShell para aumentar el tamaño de un disco de datos no administrado

Para aumentar el tamaño de los discos de datos no administrados en una cuenta de almacenamiento, use los siguientes cmdlets de PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm)                 |
| [Stop-AzureRMVM](/powershell/module/azurerm.compute/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/module/azurerm.compute/set-azurermvmdatadisk) |
| [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm)                   | [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)             |

<br>

El script siguiente lo guiará a través del proceso de obtener la información de la máquina virtual y la cuenta de almacenamiento, seleccionar el disco de datos y especificar el nuevo tamaño.

```powershell

# Select Azure Storage Account

    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru

    $rgName = $storageAccount.ResourceGroupName

# Select the VM

    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize

    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru


# Specify a larger data disk size

    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk

    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure

    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name

```

## <a name="allocate-the-unallocated-disk-space"></a>Asignación del espacio en disco sin asignar

Una vez haya creado la unidad más grande, debe asignar el nuevo espacio sin asignar de la máquina virtual. Para asignar el espacio, puede conectarse a la máquina virtual con Administración de discos (diskmgmt.msc). Si habilitó WinRM y un certificado en la máquina virtual cuando la creó, puede usar PowerShell remoto para inicializar el disco. También puede utilizar una extensión de script personalizada:

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

El archivo de script puede contener algo parecido a este código para aumentar la asignación de unidad al tamaño máximo de los discos:

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>Pasos siguientes
- [Más información sobre los discos y VHD](../../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

