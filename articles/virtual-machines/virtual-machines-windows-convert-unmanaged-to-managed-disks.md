---
title: "Conversión de una máquina virtual de discos no administrados a discos administrados (Azure) | Microsoft Docs"
description: "Conversión de una máquina virtual de discos no administrados a discos administrados mediante PowerShell en el modelo de implementación de Resource Manager"
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
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: e25eaee75b1637447447ace88c2bf1d9aed83880
ms.openlocfilehash: 484cc6419150b84ee6ed7d2c92960a4d0202e10b
ms.lasthandoff: 02/27/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>Conversión de una máquina virtual de discos no administrados a discos administrados

Si tiene máquinas virtuales en Azure que usan discos no administrados en las cuentas de almacenamiento y desea que esas máquinas virtuales puedan beneficiarse de las ventajas de los [discos administrados](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), puede convertir las máquinas virtuales. El proceso convierte el disco de sistema operativo y los discos de datos conectados para que dejen de usar discos no administrados en una cuenta de almacenamiento y usen discos administrados. Las máquinas virtuales se apagan y se desasignan; después, puede usar Powershell para convertir la máquina virtual para que use discos administrados. Después de la conversión, reinicie la máquina virtual para que use discos administrados.

Antes de comenzar, asegúrese de revisar la información de [Plan for the migration to Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Planeación de la migración a Managed Disks).
Pruebe el proceso de migración con la migración de una máquina virtual de prueba antes de realizar la migración en producción, porque este proceso no es reversible.


> [!IMPORTANT] 
> Durante la conversión, desasignará la máquina virtual. Desasignar la máquina virtual significa que tendrá una nueva dirección IP cuando se inicie después de la conversión. Si depende de una dirección IP fija, use una dirección IP reservada.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Managed Disks y Cifrado del servicio Azure Storage (SSE)

No se puede convertir una máquina virtual no administrada creada en el modelo de implementación de Resource Manager en discos administrados si alguno de los discos no administrados conectados está en una cuenta de almacenamiento que está cifrada o ha sido cifrada en algún momento con [Cifrado del servicio Azure Storage (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Los siguientes pasos explican con detalle cómo convertir discos no administrados que están, o han estado, en una cuenta de almacenamiento cifrada:

**Discos de datos**:
1.    Desconecte el disco de datos de la máquina virtual.
2.    Copie el disco duro virtual a una cuenta de almacenamiento que nunca se haya habilitado para SSE. Para copiar el disco a otra cuenta de almacenamiento, use [AzCopy](../storage/storage-use-azcopy.md): `AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    Conecte el disco copiado a la máquina virtual y convierta la máquina virtual.

**Disco de sistema operativo**:
1.    Detenga (desasigne) la máquina virtual. Guarde la configuración de la máquina virtual si es necesario.
2.    Copie el disco duro virtual de sistema operativo a una cuenta de almacenamiento que nunca se haya habilitado para SSE. Para copiar el disco a otra cuenta de almacenamiento, use [AzCopy](../storage/storage-use-azcopy.md): `AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    Cree una máquina virtual que use discos administrados y conecte ese archivo VHD como disco de sistema operativo durante la creación.


## <a name="before-you-begin"></a>Antes de empezar
Si usa PowerShell, asegúrese de que tiene la versión más reciente del módulo de PowerShell AzureRM.Compute. Ejecute el siguiente comando para instalarla.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para más información, consulte [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Control de versiones de Azure PowerShell).



## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>Conversión de las máquinas virtuales de un conjunto de disponibilidad en discos administrados en un conjunto de disponibilidad administrado

Si las máquinas virtuales que desea convertir en discos administrados se encuentran en un conjunto de disponibilidad, primero debe convertir el conjunto de disponibilidad en un conjunto de disponibilidad administrado.

El siguiente script actualiza el conjunto de disponibilidad para que sea un conjunto de disponibilidad administrado; después, realiza la desasignación, convierte los discos y, por último, se reinicia cada máquina virtual en el conjunto de disponibilidad.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>Conversión de máquinas virtuales de Azure existentes en discos administrados del mismo tipo de almacenamiento

> [!IMPORTANT]
> Después de realizar el procedimiento siguiente, hay un blob en bloques único que permanece en el contenedor /vhds predeterminado. El nombre del archivo es "VMName.xxxxxxx.status". No elimine este objeto de estado de mantenimiento restante. En futuros trabajos se abordará este problema.

En esta sección se explica cómo transformar las máquinas virtuales de Azure existentes de discos no administrados en cuentas de almacenamiento a discos administrados si va a usar el mismo tipo de almacenamiento. Puede usar este proceso para convertir discos no administrados (SSD) premium en discos administrados premium, o bien discos no administrados (HDD) estándar en discos administrados estándar. 

1. Cree las variables y desasigne la máquina virtual. En este ejemplo, se establece el nombre del grupo de recursos en **myResourceGroup** y el nombre de la máquina virtual en **myVM**.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    El *estado* de la máquina virtual en Azure Portal cambia de **Detenido** a **Detenido (desasignado)**.
    
2. Convierta todos los discos asociados a la máquina virtual, incluido el disco de sistema operativo, y todos los discos de datos.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Migración de máquinas virtuales de Azure existentes mediante discos no administrados estándar a discos administrados premium

Esta sección le mostrará cómo convertir las máquinas virtuales de Azure existentes en discos no administrados estándar en discos administrados premium. Para poder usar discos administrados premium, la máquina virtual debe usar un [Tamaño de máquina virtual](virtual-machines-windows-sizes.md) que admita Premium Storage.


1.  En primer lugar, establezca los parámetros comunes. Asegúrese de que el [Tamaño de máquina virtual](virtual-machines-windows-sizes.md) que seleccione admita Premium Storage.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  Obtenga la máquina virtual con discos no administrados.

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  Detenga (desasigne) la máquina virtual.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  Actualice el tamaño de la VM a un tamaño compatible con Premium Storage disponible en la región donde está ubicada la máquina virtual.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  Convierta la máquina virtual con discos no administrados en discos administrados. 

    Si recibe un error interno del servidor, vuelva a intentarlo 2 o 3 veces antes de ponerse en contacto con nuestro equipo de soporte técnico.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. Detenga (desasigne) la máquina virtual.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -VMName $vmName -Force
    ```
2.  Actualice todos los discos a Premium Storage.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. Inicie la máquina virtual.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
    
También puede tener una mezcla de discos que usan almacenamiento premium y estándar.
    

## <a name="next-steps"></a>Pasos siguientes

Realizar una copia de solo lectura de una máquina virtual mediante [instantáneas](virtual-machines-windows-snapshot-copy-managed-disk.md).


