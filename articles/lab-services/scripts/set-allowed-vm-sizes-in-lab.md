---
title: 'Script de PowerShell: establecimiento de los tamaños de máquina virtual permitidos en Azure Lab Services | Microsoft Docs'
description: Este script de PowerShell establece los tamaños de máquina virtual permitidos en Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 2fc7001e5594b1c37e6566cde1ee586194737e3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Uso de PowerShell para establecer los tamaños de máquina virtual permitidos en Azure Lab Services

Este script de PowerShell de ejemplo establece los tamaños de máquina virtual en Azure Lab Services.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>requisitos previos
* **Un laboratorio personalizado**. Este script requiere que disponga de un laboratorio personalizado existente. 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos: 

| Get-Help | Notas |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Busca recursos en función de los parámetros especificados. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obtiene recursos. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifica un recurso. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Crea un recurso. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de script de PowerShell de Azure Lab Services en los [ejemplos de PowerShell de Azure Lab Services](../samples-powershell.md).