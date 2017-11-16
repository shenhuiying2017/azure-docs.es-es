---
title: "Ejemplo de JSON de Azure Policy: Auditar la configuración de diagnóstico | Microsoft Docs"
description: "Esta directiva de ejemplo de JSON audita si la configuración de diagnóstico no está habilitada para tipos de recursos especificados."
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
ms.openlocfilehash: 0e97470491f548cafd5023851e2c2f6bd76cbe9f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="audit-diagnostic-setting"></a>Auditar la configuración de diagnóstico

Audita si la configuración de diagnóstico no está habilitada para tipos de recursos especificados. Se especifica una matriz de tipos de recursos para comprobar si la configuración de diagnóstico está habilitada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../policy-templates/samples/Monitoring/audit-diagnostic-setting/azurepolicy.json "Audit diagnostic setting")]

Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FMonitoring%2Faudit-diagnostic-setting%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "audit-diagnostic-setting" -DisplayName "Audit diagnostic setting" -description "Audit diagnostic setting for selected resource types" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Monitoring/audit-diagnostic-setting/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Monitoring/audit-diagnostic-setting/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfResourceTypes <Resource Types> -PolicyDefinition $definition
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
az policy definition create --name 'audit-diagnostic-setting' --display-name 'Audit diagnostic setting' --description 'Audit diagnostic setting for selected resource types' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Monitoring/audit-diagnostic-setting/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Monitoring/audit-diagnostic-setting/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-diagnostic-setting"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar ejemplos de plantillas de Azure Policy adicionales en [Plantillas para Azure Policy](../json-samples.md).
