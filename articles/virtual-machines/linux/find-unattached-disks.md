---
title: "Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados | Microsoft Docs"
description: "Cómo buscar y eliminar discos administrados y no administrados (VHD o blobs en páginas) de Azure no asociados mediante la CLI de Azure."
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 281e51783af05e02346b537f0abccdb2def38b31
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados
Cuando se elimina una máquina virtual (VM) en Azure, de forma predeterminada, no se elimina ningún disco asociado a la máquina virtual. Esta característica ayuda a evitar la pérdida de datos debido a la eliminación accidental de las máquinas virtuales. Después de eliminar una máquina virtual, seguirá pagando por los discos no asociados. En este artículo se muestra cómo buscar y eliminar los discos no asociados y reducir costos innecesarios. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos administrados: búsqueda y eliminación de discos no asociados 

El siguiente script busca los [discos administrados](managed-disks-overview.md) no asociados examinando el valor de la propiedad **ManagedBy**. Cuando un disco administrado está asociado a una máquina virtual, la propiedad **ManagedBy** contiene el identificador de recurso de la máquina virtual. Cuando se desasocia, le propiedad **ManagedBy** tiene un valor null. El script examina todos los discos administrados de una suscripción de Azure. Cuando el script localiza un disco administrad con la propiedad **ManagedBy** establecida en null, se determina que el disco no está asociado.

>[!IMPORTANT]
>Primero, ejecute el script y establezca la variable **deleteUnattachedDisks** en 0. Esta acción le permite buscar y ver todos los discos administrados no asociados.
>
>Después de revisar todos los discos no asociados, vuelva a ejecutar el script y establezca la variable **deleteUnattachedDisks** en 1. Esta acción le permite eliminar todos los discos administrados no asociados.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos no administrados: búsqueda y eliminación de discos no asociados 

Los discos no administrados son archivos VHD almacenados como [blobs en páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) en [cuentas de Azure Storage](../../storage/common/storage-create-storage-account.md). El siguiente script busca discos no administrados no asociados (blobs en páginas) examinando el valor de la propiedad **LeaseStatus**. Cuando un disco no administrado está asociado a una máquina virtual, la propiedad **LeaseStatus** se establece en **Bloqueado**. Cuando un disco no administrado está desasociado, la propiedad **LeaseStatus** se establece en **Desbloqueado**. El script examina todos los discos no administrados de todas las cuentas de almacenamiento de Azure de una suscripción de Azure. Cuando el script localiza un disco administrado con la propiedad **LeaseStatus** establecida en **Desbloqueado**, se determina que el disco no está asociado.

>[!IMPORTANT]
>Primero, ejecute el script y establezca la variable **deleteUnattachedVHDs** en 0. Esta acción le permite buscar y ver todos los discos duros virtuales no administrados y no asociados.
>
>Después de revisar todos los discos no asociados, vuelva a ejecutar el script y establezca la variable **deleteUnattachedVHDs** en 1. Esta acción le permite eliminar todos los discos duros virtuales no administrados y no asociados.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>pasos siguientes

[Eliminación de la cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md)



