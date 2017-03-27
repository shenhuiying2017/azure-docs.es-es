---
title: "Migración de una VM única desde el modelo de implementación clásico a Azure Managed Disks | Microsoft Docs"
description: "Migre una VM de Azure única desde el modelo de implementación clásico a Managed Disks en el modelo de implementación de Resource Manager."
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
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 42d9e68c3c18d04c02ab818d84a653ece811fc52
ms.lasthandoff: 03/15/2017


---

# <a name="migrate-a-single-azure-vm-from-classic-to-managed-disks"></a>Migración de una VM de Azure única desde el modelo clásico a Managed Disks 


Esta sección lo ayudará a migrar las máquinas virtuales de Azure existentes desde el modelo de implementación clásico a [Managed Disks](../storage/storage-managed-disks-overview.md) en el modelo de implementación de Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planeación de la migración a Managed Disks

Esta sección puede ayudarlo a tomar la mejor decisión sobre los tipos de discos y VM.


### <a name="location"></a>Ubicación

Elija una ubicación donde Azure Managed Disks esté disponible. Si va a migrar a Managed Disks Premium, asegúrese que Premium Storage también está disponible en la región a la que planea migrar. Consulte [Servicios de Azure por región](https://azure.microsoft.com/regions/#services) para información actualizada sobre las ubicaciones disponibles.

### <a name="vm-sizes"></a>Tamaños de VM

Si va a migrar a Managed Disks Premium, debe actualizar el tamaño de la VM a un tamaño compatible con Premium Storage disponible en la región donde se ubica la VM. Revise los tamaños de VM compatibles con Premium Storage. Las especificaciones de tamaño de las máquinas virtuales de Azure se muestran en [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).
Repase las características de rendimiento de las máquinas virtuales que trabajan con Almacenamiento premium y elija el tamaño de máquina virtual que se mejor se ajuste a su carga de trabajo. Procure que haya suficiente ancho de banda disponible en la máquina virtual para dirigir el tráfico de disco.

### <a name="disk-sizes"></a>Tamaños de disco

**Managed Disks Premium**

Hay tres tipos de Managed Disks Premium que se pueden usar con la VM y cada uno de ellos tiene sus límites específicos de rendimiento y E/S por segundo. Considere estos límites a la hora de elegir el tipo de disco Premium para la VM según las necesidades de capacidad, rendimiento, escalabilidad y cargas máximas de la aplicación.

| Tipo de discos Premium  | P10               | P20               | P30               |
|---------------------|-------------------|-------------------|-------------------|
| Tamaño del disco           | 128 GB            | 512 GB            | 1.024 GB (1 TB)    |
| IOPS por disco       | 500               | 2300              | 5000              |
| Rendimiento de disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

**Discos administrados Estándar**

Hay cinco tipos de discos administrados Estándar que se pueden usar con la VM. Cada uno de ellos tiene una capacidad distinta, pero los mismos límites de rendimiento y E/S por segundo. Elija el tipo de disco administrado Estándar según las necesidades de capacidad de la aplicación.

| Tipo de disco Estándar  | S4               | S6               | S10              | S20              | S30              |
|---------------------|------------------|------------------|------------------|------------------|------------------|
| Tamaño del disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1.024 GB (1 TB)   |
| IOPS por disco       | 500              | 500              | 500              | 500              | 500              |
| Rendimiento de disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo |

### <a name="disk-caching-policy"></a>Directiva de almacenamiento en caché de disco 

**Managed Disks Premium**

De forma predeterminada, la directiva de almacenamiento en caché de los discos es *Solo lectura* para todos los discos de datos Premium y *Lectura y Escritura* para el disco de sistema operativo Premium conectado a la máquina virtual. Recomendamos esta opción de configuración para lograr el rendimiento óptimo de E/S de la aplicación. Para discos de datos de solo escritura o de gran cantidad de escritura (por ejemplo, archivos de registro de SQL Server), deshabilite el almacenamiento en caché de disco para lograr un mejor rendimiento de la aplicación.

### <a name="pricing"></a>Precios

Revise el [precio de Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Los precios de Managed Disks Premium son iguales que los de Unmanaged Disks Premium. Sin embargo, los precios de Managed Disks Estándar son distintos a los de los Unmanaged Disks Estándar.


## <a name="checklist"></a>Lista de comprobación

1.  Si va a migrar a Managed Disks Premium, asegúrese de que esté disponible en la región a la que migra.

2.  Decida la nueva serie de máquinas virtuales que se va a usar. Debe ser compatible con Premium Storage si migra a Managed Disks Premium.

3.  Decida el tamaño de VM exacto que usará, el que debe estar disponible en la región a la que migra. El tamaño de la máquina virtual debe ser suficientemente grande para admitir el número de discos de datos que tiene. Por ejemplo, si tiene cuatro discos de datos, la VM debe tener dos o más núcleos. Considere también las necesidades de potencia de procesamiento, memoria y ancho de banda de red.

4.  Tenga a mano los detalles de la máquina virtual, incluida la lista de discos y los blobs de disco duro virtual correspondientes.

Prepare la aplicación para el tiempo de inactividad. Para realizar una migración limpia, tendrá que detener todo el procesamiento del sistema actual. Solo entonces se puede obtener un estado coherente que se puede migrar a la nueva plataforma. La duración del tiempo de inactividad depende de la cantidad de datos en los discos que se van a migrar.


## <a name="migrate-the-vm"></a>Migración de la VM

Prepare la aplicación para el tiempo de inactividad. Para realizar una migración limpia, tendrá que detener todo el procesamiento del sistema actual. Solo entonces se puede obtener un estado coherente que se puede migrar a la nueva plataforma. La duración del tiempo de inactividad depende de la cantidad de datos en los discos que se van a migrar.


1.  En primer lugar, establezca los parámetros comunes:

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Cree un disco administrado de SO mediante el VHD de la VM clásica.

    Asegúrese de que proporcionó el URI completo del VHD del SO al parámetro $osVhdUri. Además, escriba **-AccountType** como **PremiumLRS** o **StandardLRS** según el tipo de discos al que migra (Premium o Estándar).

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Conecte el disco de SO a la nueva VM.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Cree un disco de datos administrado desde el archivo de VHD de datos y agréguelo a la VM nueva.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Establezca la dirección IP pública, la red virtual y la NIC para crear la VM nueva.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Puede que en esta guía no se contemplen otros pasos necesarios para admitir su aplicación.
>
>

## <a name="next-steps"></a>Pasos siguientes

- Conexión a una máquina virtual. Para obtener instrucciones, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


