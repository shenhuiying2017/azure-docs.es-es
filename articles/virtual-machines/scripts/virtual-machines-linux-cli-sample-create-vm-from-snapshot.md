---
title: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual a partir de una instantánea | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual a partir de una instantánea"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 960a44e0df2c46cea464d9277f22f449b62e7d06
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>Creación de una máquina virtual a partir de una instantánea con la CLI

Este script crea una máquina virtual a partir de una instantánea de un disco del SO.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "Creación de máquinas virtuales a partir de una instantánea")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un disco administrado, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | Obtiene instantáneas mediante el nombre de la instantánea y el del grupo de recursos. La propiedad de identificador del objeto devuelto se utiliza para crear un disco administrado.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Crea discos administrados a partir de una instantánea usando el identificador de las instantáneas, el nombre del disco, el tipo de almacenamiento y el tamaño.  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crea una máquina virtual con un disco del SO administrado. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

