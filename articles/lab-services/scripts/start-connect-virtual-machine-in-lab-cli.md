---
title: 'Ejemplo de script de la CLI de Azure: Inicio de una máquina virtual en un laboratorio personalizado | Microsoft Docs'
description: Este script de la CLI de Azure inicia una máquina virtual en un laboratorio personalizado.
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
ms.openlocfilehash: c14328904f29f8160a1ccc1130fe6d2cfa629822
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-custom-lab"></a>Uso de la CLI de Azure para iniciar una máquina virtual en un laboratorio personalizado

Este script de la CLI de Azure inicia una máquina virtual en un laboratorio personalizado. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az lab vm start ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Inicia una máquina virtual en un laboratorio personalizado. Esta operación puede tardar varios minutos en completarse. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Encontrará más ejemplos de scripts de PowerShell de Azure Lab Services en los [ejemplos de la CLI de Azure Lab Services](../samples-cli.md).