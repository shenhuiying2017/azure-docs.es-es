---
title: "Ejemplo de JSON de Azure Policy: Auditar la configuración de detección de amenazas en el nivel de servidor | Microsoft Docs"
description: "Esta directiva de ejemplo de JSON audita las directivas de alertas de seguridad de SQL Database si no están establecidas en el estado especificado."
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
ms.openlocfilehash: 715af96743d0cb6490750e10e8f13ffaa69765fd
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="audit-server-level-threat-detection-setting"></a>Auditar la configuración de detección de amenazas en el nivel de servidor

Esta directiva audita las directivas de alertas de seguridad de SQL Database si no están establecidas en el estado especificado. Se especifica un valor que indica si la detección de amenazas está habilitada o deshabilitada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../policy-templates/samples/SQL/audit-sql-server-threat-detection/azurepolicy.json "Audit Server level threat detection setting")]

Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Faudit-sql-server-threat-detection%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$definition = New-AzureRmPolicyDefinition -Name "audit-sql-server-threat-detection" -DisplayName "Audit Server level threat detection setting" -description "Audit threat detection setting for SQL Server" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-threat-detection/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-threat-detection/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -setting <Threat Detection Setting> -PolicyDefinition $definition
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
az policy definition create --name 'audit-sql-server-threat-detection' --display-name 'Audit Server level threat detection setting' --description 'Audit threat detection setting for SQL Server' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-threat-detection/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-sql-server-threat-detection/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-sql-server-threat-detection"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar ejemplos de plantillas de Azure Policy adicionales en [Plantillas para Azure Policy](../json-samples.md).
