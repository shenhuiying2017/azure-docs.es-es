---
title: "Ejemplo de script de CLI de Azure: exportación o copia de instantánea como un VHD a una cuenta de almacenamiento en otra región | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: exportación o copia de instantánea como un VHD a una cuenta de almacenamiento en misma suscripción o en una diferente"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: cd0128256e016a329b0940eec4be41426cdaf51a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>Exportación o copia de una instantánea administrada como un VHD a una cuenta de almacenamiento en una región diferente con CLI

Este script exporta una instantánea administrada a una cuenta de almacenamiento en una región diferente. En primer lugar, genera el identificador URI de SAS de la instantánea y, luego, lo usa para copiarlo en una cuenta de almacenamiento en una región diferente. Use este script para mantener una copia de seguridad de los discos administrados en una región distinta para la recuperación ante desastres. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para generar el identificador URI de SAS para una instantánea administrada y copia la instantánea en una cuenta de almacenamiento usando el URI de SAS. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_grant_access) | Genera la SAS de solo lectura que se usa para copiar el archivo de VHD subyacente a una cuenta de almacenamiento o descargarlo localmente.  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy#az_storage_blob_copy_start) | Copia un blob de forma asincrónica desde una cuenta de almacenamiento a otra. |

## <a name="next-steps"></a>Pasos siguientes

[Crear un disco administrado a partir de un VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Crear una máquina virtual desde un disco administrado](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
