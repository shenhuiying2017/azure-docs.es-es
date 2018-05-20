---
title: 'Ejemplo de script de la CLI de Azure: Detención y eliminación de una máquina virtual en un laboratorio personalizado | Microsoft Docs'
description: Este script de la CLI de Azure detiene y elimina una máquina virtual en un laboratorio personalizado.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Uso de la CLI de Azure para detener y eliminar una máquina virtual en un laboratorio personalizado

Este script de la CLI de Azure detiene y elimina una máquina virtual en un laboratorio personalizado. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Detiene una máquina virtual en un laboratorio personalizado. Esta operación puede tardar varios minutos en completarse. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Elimina una máquina virtual en un laboratorio personalizado. Esta operación puede tardar varios minutos en completarse. |


## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Encontrará más ejemplos de scripts de PowerShell de Azure Lab Services en los [ejemplos de la CLI de Azure Lab Services](../samples-cli.md).