---
title: "Conversión de almacenamiento de Azure Managed Disks de estándar a premium, y viceversa | Microsoft Docs"
description: "Conversión de almacenamiento de Azure Managed Disks de estándar a premium, y viceversa, mediante la CLI de Azure."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Conversión de almacenamiento de Azure Managed Disks de estándar a premium, y viceversa

Managed Disks ofrece dos opciones de almacenamiento: [Premium](../windows/premium-storage.md) (basado en SSD) y [Estándar](../windows/standard-storage.md) (basado en HDD). Permite cambiar fácilmente entre las dos opciones en función de sus necesidades de rendimiento, con el mínimo tiempo de inactividad. Esta funcionalidad no está disponible con discos no administrados. Pero resulta muy sencillo realizar la [conversión a Managed Disks](convert-unmanaged-to-managed-disks.md) para cambiar fácilmente entre las dos opciones.

En este artículo se muestra cómo convertir Managed Disks de estándar a premium, y viceversa, mediante la CLI de Azure. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Antes de empezar

* La conversión requiere reiniciar la máquina virtual, por lo que debe programar la migración del almacenamiento de discos durante una ventana de mantenimiento existente previamente. 
* Si usa discos no administrados, debe primero realizar la [conversión a Managed Disks](convert-unmanaged-to-managed-disks.md) para usar este artículo para cambiar entre las dos opciones de almacenamiento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Conversión de todos los discos Managed Disks de una máquina virtual de estándar a premium, y viceversa

En el ejemplo siguiente se muestra cómo cambiar todos los discos de una máquina virtual del almacenamiento estándar al premium. Para usar discos administrados premium, la máquina virtual debe usar un [Tamaño de máquina virtual](sizes.md) que admita Premium Storage. En este ejemplo también se pasa a un tamaño que admite Premium Storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Conversión de un disco de Managed Disks de estándar a premium, y viceversa

Puede que, en el caso de la carga de trabajo de desarrollo y pruebas, prefiera tener una combinación de discos estándar y premium para reducir el costo. Para ello, puede actualizar a Premium Storage solo los discos que requieren mejor rendimiento. En el ejemplo siguiente se muestra cómo cambiar un solo disco de una máquina virtual del almacenamiento estándar al premium, y viceversa. Para usar discos administrados premium, la máquina virtual debe usar un [Tamaño de máquina virtual](sizes.md) que admita Premium Storage. En este ejemplo también se pasa a un tamaño que admite Premium Storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Pasos siguientes

Realice una copia de solo lectura de una máquina virtual mediante [instantáneas](snapshot-copy-managed-disk.md).

