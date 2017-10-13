---
title: "Ejemplo de script de Azure PowerShell: Creación de un área de trabajo de Log Analytics | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: Creación de un área de trabajo de Log Analytics"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
tags: 
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Creación de un área de trabajo de Log Analytics con PowerShell

Este script le permite trabajar rápidamente con un área de trabajo de Azure Log Analytics, que es necesaria si desea empezar a recopilar, analizar y realizar acciones en los datos.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear una nueva área de trabajo de Log Analytics en su suscripción. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Le permite obtener información sobre un área de trabajo existente. |
| [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Le permite crear un área de trabajo en la ubicación y el grupo de recursos especificados. |


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

