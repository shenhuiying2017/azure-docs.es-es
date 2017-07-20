---
title: "Ejemplo de script de CLI de Azure: copia (transferencia) de instantánea de un disco administrado en la misma suscripción o en otra con CLI | Microsoft Docs"
description: "Ejemplo de script de CLI de Azure: copia (transferencia) de instantánea de un disco administrado en la misma suscripción o en otra con CLI"
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
ms.openlocfilehash: 7f09b204bd1901f426143e597399950d3c7dd110
ms.contentlocale: es-es
ms.lasthandoff: 05/23/2017

---

# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>Copia de instantánea de un disco administrado en la misma suscripción o en otra con CLI

Este script copia una instantánea de un disco administrado en la misma suscripción o en otra. Use este script para mover una instantánea a la misma o a otra suscripción en la misma región que la instantánea primaria.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/storage/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copiar instantánea")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear una instantánea en la suscripción de destino mediante el identificador de la instantánea de origen. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Obtiene todas las propiedades de una instantánea usando las propiedades de nombre y grupo de recursos de la instantánea. La propiedad del identificador se usa para copiar la instantánea en otra suscripción.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#create) | Copia una instantánea mediante la creación de una instantánea en otra suscripción usando el identificador y nombre de la instantánea primaria.  |

## <a name="next-steps"></a>Pasos siguientes

[Creación de una máquina virtual a partir de una instantánea](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual y discos administrados en la [documentación de Azure sobre máquinas virtuales Linux](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

