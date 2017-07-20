---
title: "Ejemplo de script de CLI de Azure: creación de un disco administrado a partir de un archivo de disco duro virtual en una cuenta de almacenamiento en la misma suscripción | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: creación un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción"
services: managed-disks-linux
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 2e0d668c1fc419abde95207654cab35ea8b058fb
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Creación un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción con CLI

Este script crea un disco administrado a partir de un archivo de VHD en una cuenta de almacenamiento en la misma suscripción. Use este script para importar un VHD especializado (no generalizado ni preparado con sysprep) en el disco de sistema operativo administrado para crear una máquina virtual. O bien úselo para importar un VHD de datos en el disco de datos administrado. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Crear un disco administrado a partir de un VHD")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un disco administrado a partir de un VHD. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Crea un disco administrado usando el identificador URI de un VHD en una cuenta de almacenamiento en la misma suscripción. |

## <a name="next-steps"></a>Pasos siguientes

[Crear una máquina virtual conectando un disco administrado como disco del SO](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Linux](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

