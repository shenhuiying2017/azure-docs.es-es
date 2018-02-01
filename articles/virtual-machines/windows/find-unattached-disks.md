---
title: "Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados | Microsoft Docs"
description: "Cómo buscar y eliminar discos administrados y no administrados (VHD o blobs en páginas) de Azure no conectados mediante Azure PowerShell."
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
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados
Cuando elimina una máquina virtual en Azure, los discos que están conectados a ella no se eliminan de manera predeterminada. Esto evita que se pierdan datos debido a la eliminación por error de máquinas virtuales, pero sigue pagando innecesariamente por los discos no conectados. Use este artículo para buscar y eliminar todos los discos no conectados y así poder ahorrar costos. 


## <a name="find-and-delete-unattached-managed-disks"></a>Búsqueda y eliminación de discos administrados no conectados 

En el script siguiente se muestra cómo buscar [discos administrados](managed-disks-overview.md) no conectados con la propiedad *ManagedBy*. Recorre en bucle todos los discos administrados de una suscripción y revisa si la propiedad *ManagedBy* es nula para así encontrar discos administrados no conectados. La propiedad *ManagedBy* almacena el identificador de recurso de la máquina virtual a la que está conectado un disco administrado.

Es muy recomendable que ejecute primero el script estableciendo la variable *deleteUnattachedDisks* en 0 para ver todos los discos no conectados. Después de revisar los discos no conectados, ejecute el script estableciendo la variable *deleteUnattachedDisks* en 1 para eliminar todos los discos no conectados.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Búsqueda y eliminación de discos no administrados no conectados 

Los discos no administrados son archivos de disco duro virtual almacenados como [blobs en páginas] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) en [cuentas de Azure Storage](../../storage/common/storage-create-storage-account.md). En el script siguiente se muestra cómo buscar discos no administrados (blobs en páginas) no conectados con la propiedad *LeaseStatus*. Recorre en bucle todos los discos no administrados en todas las cuentas de almacenamiento de una suscripción y revisa si la propiedad *LeaseStatus* está desbloqueada para así buscar los discos no administrados no conectados. La propiedad *LeaseStatus* está establecida en bloqueada si hay un disco no administrado conectado a una máquina virtual.

Es muy recomendable que ejecute primero el script estableciendo la variable *deleteUnattachedVHDs* en 0 para ver todos los discos no conectados. Después de revisar los discos no conectados, ejecute el script estableciendo la variable *deleteUnattachedVHDs* en 1 para eliminar todos los discos no conectados.


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

[Eliminar cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md)




