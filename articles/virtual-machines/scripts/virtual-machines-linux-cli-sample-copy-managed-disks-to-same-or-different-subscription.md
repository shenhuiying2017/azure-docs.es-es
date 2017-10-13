---
title: "Ejemplo de script de CLI de Azure: copia (transferencia) de discos administrados en la misma suscripción o en otra | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: copia (transferencia) de discos administrados en la misma suscripción o en otra"
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
ms.openlocfilehash: 8ff34f3d0b11c47f19205b92aebfc96e5cd5a014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copia de discos administrados en la misma suscripción o en otra con CLI

Este script copia un disco administrado en la misma suscripción o en otra, pero dentro de la misma región. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un nuevo disco administrado en la suscripción de destino mediante el identificador de disco administrado de origen. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | Obtiene todas las propiedades de un disco administrado usando las propiedades de nombre y grupo de recursos del disco administrado. La propiedad del identificador se usa para copiar el disco administrado en otra suscripción.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#az_disk_create) | Copia un disco administrado mediante la creación de un nuevo disco administrado en otra suscripción con el identificador y nombre el disco administrado primario.  |

## <a name="next-steps"></a>Pasos siguientes

[Crear una máquina virtual desde un disco administrado](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Linux](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
