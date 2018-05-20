---
title: 'Ejemplo de script de la CLI de Azure: Creación y comprobación de una máquina virtual en un laboratorio personalizado | Microsoft Docs'
description: Este script de la CLI de Azure crea una máquina virtual en un laboratorio personalizado y comprueba que está disponible.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: bd564cda7b4d5c2158b8499b48b8faa68309b461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-custom-lab"></a>Uso de la CLI de Azure para crear y comprobar la disponibilidad de una máquina virtual en un laboratorio personalizado

Este script de la CLI de Azure crea una máquina virtual en un laboratorio personalizado. La máquina virtual basada en una imagen de Marketplace con autenticación ssh. El script comprueba entonces que la máquina virtual está disponible para su uso. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Crea una máquina virtual en un laboratorio personalizado. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Muestra el estado de la máquina virtual en un laboratorio personalizado. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Encontrará más ejemplos de scripts de PowerShell de Azure Lab Services en los [ejemplos de la CLI de Azure Lab Services](../samples-cli.md).