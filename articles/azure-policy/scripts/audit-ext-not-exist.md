---
title: "Ejemplo de JSON de Azure Policy: Auditar si la extensión no existe | Microsoft Docs"
description: "Esta directiva de ejemplo de JSON audita si una extensión no se implementa con una máquina virtual."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: f873351fb7b6742bb0ad5916607c634282b480d3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="audit-if-extension-does-not-exist"></a>Auditar si la extensión no existe

Esta directiva audita si una extensión no se implementa con una máquina virtual. Se especifica el tipo y el publicador de la extensión para comprobar si se implementó.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../policy-templates/samples/compute/audit-vm-extension/azurepolicy.json "Audit if extension does not exist")]

Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Faudit-vm-extension%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "audit-vm-extension" -DisplayName "Audit if extension does not exist" -description "This policy audits if a required extension doesn't exist." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -publisher <Extension Publisher> -type <Extension Type> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpieza de la implementación de PowerShell

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-vm-extension' --display-name 'Audit if extension does not exist' --description 'This policy audits if a required extension doesn't exist.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/audit-vm-extension/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-vm-extension"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar ejemplos de plantillas de Azure Policy adicionales en [Plantillas para Azure Policy](../json-samples.md).
