---
title: 'Script de PowerShell: creación de un rol personalizado en un laboratorio personalizado en Azure Lab Services | Microsoft Docs'
description: Este script de PowerShell agrega un usuario externo a un laboratorio personalizado de Azure Lab Services.
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
ms.openlocfilehash: df91c9f842d338e1725fec2734129f2f1f3d3721
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-role-in-a-custom-lab-in-azure-lab-services"></a>Uso de PowerShell para crear un rol personalizado en un laboratorio personalizado de Azure Lab Services

Este script de PowerShell de ejemplo crea un rol personalizado para usarlo en un laboratorio personalizado de Azure Lab Services. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>requisitos previos
* **Un laboratorio personalizado**. Este script requiere que disponga de un laboratorio personalizado existente. 

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos: 

| Get-Help | Notas |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Obtiene las operaciones de un proveedor de recursos de Azure que son protegibles con RBAC de Azure. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Enumera todos los roles de RBAC de Azure disponibles para asignación. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Crea un rol personalizado. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de script de PowerShell de Azure Lab Services en los [ejemplos de PowerShell de Azure Lab Services](../samples-powershell.md).