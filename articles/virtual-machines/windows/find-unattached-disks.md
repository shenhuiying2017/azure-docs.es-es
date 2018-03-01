---
title: "Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados | Microsoft Docs"
description: "Cómo buscar y eliminar discos administrados y no administrados (VHD o blobs en páginas) de Azure no asociados mediante Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 15c2550472156d5c1f680af77df2fe771edf3444
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados
Cuando se elimina una máquina virtual (VM) en Azure, de forma predeterminada, no se elimina ningún disco asociado a la máquina virtual. Esta característica ayuda a evitar la pérdida de datos debido a la eliminación accidental de máquinas virtuales. Después de eliminar una máquina virtual, seguirá pagando por los discos no asociados. En este artículo se muestra cómo buscar y eliminar los discos no asociados y reducir costos innecesarios. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos administrados: búsqueda y eliminación de discos no asociados 

El siguiente script busca los [discos administrados](managed-disks-overview.md) no asociados examinando el valor de la propiedad **ManagedBy**. Cuando un disco administrado está asociado a una máquina virtual, la propiedad **ManagedBy** contiene el identificador de recurso de la máquina virtual. Cuando se desasocia, le propiedad **ManagedBy** tiene un valor null. El script examina todos los discos administrados de una suscripción de Azure. Cuando el script localiza un disco administrad con la propiedad **ManagedBy** establecida en null, se determina que el disco no está asociado.

>[!IMPORTANT]
>Primero, ejecute el script y establezca la variable **deleteUnattachedDisks** en 0. Esta acción le permite buscar y ver todos los discos administrados no asociados.
>
>Después de revisar todos los discos no asociados, vuelva a ejecutar el script y establezca la variable **deleteUnattachedDisks** en 1. Esta acción le permite eliminar todos los discos administrados no asociados.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos no administrados: búsqueda y eliminación de discos no asociados 

Los discos no administrados son archivos VHD almacenados como [blobs en páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) en [cuentas de Azure Storage](../../storage/common/storage-create-storage-account.md). El siguiente script busca discos no administrados no asociados (blobs en páginas) examinando el valor de la propiedad **LeaseStatus**. Cuando un disco no administrado está asociado a una máquina virtual, la propiedad **LeaseStatus** se establece en **Bloqueado**. Cuando un disco no administrado está desasociado, la propiedad **LeaseStatus** se establece en **Desbloqueado**. El script examina todos los discos no administrados de todas las cuentas de almacenamiento de Azure de una suscripción de Azure. Cuando el script localiza un disco no administrado con la propiedad **LeaseStatus** establecida en **Desbloqueado**, se determina que el disco no está asociado.

>[!IMPORTANT]
>Primero, ejecute el script y establezca la variable **deleteUnattachedVHDs** en 0. Esta acción le permite buscar y ver todos los discos duros virtuales no administrados y no asociados.
>
>Después de revisar todos los discos no asociados, vuelva a ejecutar el script y establezca la variable **deleteUnattachedVHDs** en 1. Esta acción le permite eliminar todos los discos duros virtuales no administrados y no asociados.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>pasos siguientes

[Eliminación de la cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md)




