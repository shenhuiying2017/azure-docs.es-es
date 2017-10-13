---
title: "Ejemplo de script de la CLI de Azure: reinicio de máquinas virtuales | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: reinicio de máquinas virtuales por etiqueta e identificador"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: ea114f484c774573b7d219cff9102a7308af356e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="restart-vms"></a>Reinicio de máquinas virtuales

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Este ejemplo muestra dos maneras de obtener algunas máquinas virtuales y reiniciarlas.

La primera reinicia todas las máquinas virtuales del grupo de recursos.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

La segunda obtiene las máquinas virtuales etiquetadas con `az resouce list` y filtros en los recursos que son máquinas virtuales, y reinicia esas máquinas virtuales.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../windows/cli-options.md) (Uso de la CLI de Azure en Windows).


## <a name="sample-script"></a>Script de ejemplo

El ejemplo tiene tres scripts.
El primero aprovisiona las máquinas virtuales.
Utiliza la opción de no espera, por lo que devuelve el comando sin esperar a que se aprovisione cada máquina virtual.
El segundo espera a que las máquinas virtuales se aprovisionen completamente.
El tercero reinicia todas las máquinas virtuales que se aprovisionaron y, luego, solo aquellas con etiqueta.

### <a name="provision-the-vms"></a>Aprovisionamiento de máquinas virtuales

Este script crea un grupo de recursos y, luego, genera tres máquinas virtuales para realizar la operación de reinicio.
Dos de ellos tienen etiqueta.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Esperar

Este script comprueba el estado de aprovisionamiento cada 20 segundos hasta que todas las tres máquinas virtuales se aprovisionan, o bien hasta que en una de ellas se produce un error de aprovisionamiento.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Reinicio de máquinas virtuales

Este script reinicia todas las máquinas virtuales del grupo de recursos y, luego, reinicia las que tienen etiqueta.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar los grupos de recursos, las máquinas virtuales y todos los recursos relacionados.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Crea las máquinas virtuales.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Se usa con `--query` para asegurarse de que se aprovisionan las máquinas virtuales antes de reiniciar y, luego, para obtener los identificadores de las máquinas virtuales para reiniciarlas. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Se usa con `--query` para obtener los identificadores de las máquinas virtuales con la etiqueta. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Reinicia las máquinas virtuales. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
