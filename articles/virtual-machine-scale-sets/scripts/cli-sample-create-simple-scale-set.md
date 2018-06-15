---
title: 'Ejemplos de la CLI de Azure 2.0: creación de un conjunto de escalado de máquinas virtuales | Microsoft Docs'
description: Ejemplos de la CLI de Azure 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 8eda45a5e886f755f9f1850b146fd0b0521d1cba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246464"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Creación de un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0
Este script crea un conjunto de escalado de máquinas virtuales de Azure con un sistema operativo Ubuntu y recursos de red relacionados, como el equilibrador de carga. Después de ejecutar el script, puede acceder a las instancias de máquina virtual a través de SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Ejecute el siguiente comando para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos para crear un grupo de recursos, un conjunto de escalado de máquinas virtuales y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Crea el conjunto de escalado de máquinas virtuales y lo conecta a la red virtual, la subred y el grupo de seguridad de red. También se crea un equilibrador de carga para distribuir el tráfico a varias instancias de máquina virtual. Este comando también especifica la imagen de máquina virtual que se usará y las credenciales administrativas.  |
| [az group delete](/cli/azure/ad/group#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la CLI de Azure 2.0, consulte la [documentación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

Se pueden encontrar otros ejemplos de scripts de la CLI de Azure 2.0 para conjuntos de escalado de máquinas virtuales en la [documentación de Azure Virtual Machine Scale Set](../cli-samples.md).