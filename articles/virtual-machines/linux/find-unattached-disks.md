---
title: "Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados | Microsoft Docs"
description: "Cómo buscar y eliminar discos administrados y no administrados (VHD o blobs en páginas) de Azure no conectados mediante la CLI de Azure"
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
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Búsqueda y eliminación de discos administrados y no administrados de Azure no conectados
Cuando elimina una máquina virtual en Azure, los discos que están conectados a ella no se eliminan de manera predeterminada. Esto evita que se pierdan datos debido a la eliminación por error de máquinas virtuales, pero sigue pagando innecesariamente por los discos no conectados. Use este artículo para buscar y eliminar todos los discos no conectados y así poder ahorrar costos. 


## <a name="find-and-delete-unattached-managed-disks"></a>Búsqueda y eliminación de discos administrados no conectados 

En el script siguiente se muestra cómo buscar discos administrados no conectados con la propiedad ManagedBy.  Recorre en bucle todos los discos administrados de una suscripción y revisa si la propiedad *ManagedBy* es nula para así encontrar discos administrados no conectados. La propiedad *ManagedBy* almacena el identificador de recurso de la máquina virtual a la que está conectado un disco administrado. 

Es muy recomendable que ejecute primero el script estableciendo la variable *deleteUnattachedDisks* en 0 para ver todos los discos no conectados. Después de revisar los discos no conectados, ejecute el script estableciendo la variable *deleteUnattachedDisks* en 1 para eliminar todos los discos no conectados.

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
## <a name="find-and-delete-unattached-unmanaged-disks"></a>Búsqueda y eliminación de discos no administrados no conectados 

Los discos no administrados son archivos VHD almacenados como [blobs en páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) en [cuentas de Azure Storage](../../storage/common/storage-create-storage-account.md). En el script siguiente se muestra cómo buscar discos no administrados (blobs en páginas) no conectados con la propiedad LeaseStatus. Recorre en bucle todos los discos no administrados en todas las cuentas de almacenamiento de una suscripción y revisa si la propiedad *LeaseStatus* está desbloqueada para así buscar los discos no administrados no conectados. La propiedad *LeaseStatus* está establecida en bloqueada si hay un disco no administrado conectado a una máquina virtual. 

Es muy recomendable que ejecute primero el script estableciendo la variable *deleteUnattachedVHDs* en 0 para ver todos los discos no conectados. Después de revisar los discos no conectados, ejecute el script estableciendo la variable *deleteUnattachedVHDs* en 1 para eliminar todos los discos no conectados.


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

[Eliminar cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md)



