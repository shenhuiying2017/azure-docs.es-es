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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b292a02770fa74812e74fa38f870e47ed7473b63
ms.lasthandoff: 03/04/2017

---

# <a name="restart-vms-by-tag"></a>Reinicio de máquinas virtuales por etiqueta

Este ejemplo crea máquinas virtuales con una etiqueta concreta en varios grupos de recursos.
Las máquinas virtuales se crean en paralelo mediante `--no-wait`; luego, espera a la finalización colectiva.

Después de crear las máquinas virtuales, se reinician mediante dos mecanismos de consulta diferentes.

El primero reinicia las máquinas virtuales con la misma consulta que se usó para esperar a su creación asincrónica.
```bash
az vm restart --ids $(az vm list --query "join(' ', ${GROUP_QUERY}] | [].id)" \
    -o tsv) $1>/dev/null
```

El segundo usa una consulta y una lista de recursos genéricos para capturar los identificadores por etiqueta.
```bash
az vm restart --ids $(az resource list --tag ${TAG} \
    --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv) $1>/dev/null
```

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/restart-by-tag/restart-by-tag.sh "Reinicio de máquinas virtuales por etiqueta")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar los grupos de recursos, las máquinas virtuales y todos los recursos relacionados.

```azurecli
az group delete -n GROUP1 --no-wait --yes && \ 
az group delete -n GROUP2 --no-wait --yes && \
az group delete -n GROUP3 --no-wait --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual, un conjunto de disponibilidad, un equilibrador de carga y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Crea las máquinas virtuales.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | Se usa con `--query` para asegurarse de que se aprovisionan las máquinas virtuales antes de reiniciarlas. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | Reinicia las máquinas virtuales. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

