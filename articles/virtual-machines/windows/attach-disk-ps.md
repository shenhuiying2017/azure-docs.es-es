---
title: "Conexión de un disco de datos a una VM con Windows en Azure mediante PowerShell | Microsoft Docs"
description: "Cómo conectar un disco de datos nuevo o existente a una VM con Windows mediante PowerShell con el modelo de implementación de Resource Manager."
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
ms.date: 02/07/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 9d16d16f0e57fab9f1827c37f181e579c627b3d9
ms.openlocfilehash: e259a9cf42719fb0426dce09b5526fa43585bb26
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Conexión de un disco a una VM con Windows mediante PowerShell

En este artículo se explica cómo conectar discos nuevos y existentes a una máquina virtual con Windows mediante PowerShell. Si la VM utiliza discos administrados, puede conectar discos de datos administrados adicionales. También puede conectar discos de datos no administrados a una VM que usa discos no administrados en una cuenta de almacenamiento.

Antes de hacerlo, revise estas sugerencias:
* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para usar Premium Storage, necesitará una VM con Premium Storage habilitado con un tamaño como el de las VM de las series DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](/powershell/azure/overview) (Control de versiones de Azure PowerShell).


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Incorporación de un disco de datos vacío a una máquina virtual

En este ejemplo se muestra cómo agregar un disco de datos vacío a una máquina virtual existente.

### <a name="using-managed-disks"></a>Uso de discos administrados

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Uso de discos administrados en una zona de disponibilidad
Para crear un disco en una zona de disponibilidad, use [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) con el parámetro `-Zone`. En el siguiente ejemplo se crea un disco en la zona *1*.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="using-unmanaged-disks-in-a-storage-account"></a>Uso de discos no administrados en una cuenta de almacenamiento

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```


### <a name="initialize-the-disk"></a>Initialize the disk

Después de agregar un disco vacío, debe inicializarlo. Para hacerlo, puede iniciar sesión en una VM y usar la administración de discos. Si habilitó WinRM y un certificado en la VM cuando la creó, puede usar PowerShell remoto para inicializar el disco. También puede utilizar una extensión de script personalizada: 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
El archivo de script puede tener un contenido parecido a este código para inicializar los discos:

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Incorporación de un disco de datos existente a una VM

También puede conectar un VHD existente como un disco de datos administrado a una máquina virtual. 

### <a name="using-managed-disks"></a>Uso de discos administrados

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.windows.net/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Pasos siguientes

Crear una [instantánea](snapshot-copy-managed-disk.md).

