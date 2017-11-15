---
title: 'Ejemplo de JSON de Azure Policy: Asegurar el cifrado de archivos de almacenamiento | Microsoft Docs'
description: "Esta directiva de ejemplo de JSON requiere que el cifrado de archivos esté habilitado para las cuentas de almacenamiento."
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
ms.openlocfilehash: 24f6d3e16ae3e98019f97c725960d329cb687555
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="ensure-storage-file-encryption"></a>Asegurar el cifrado de archivos de almacenamiento

Esta directiva requiere que el cifrado de archivos esté habilitado para las cuentas de almacenamiento.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../policy-templates/samples/Storage/storage-account-file-encryption/azurepolicy.json "Ensure storage file encryption")]


Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]



````powershell
$definition = New-AzureRmPolicyDefinition -Name "storage-account-file-encryption" -DisplayName "Ensure storage file encryption" -description "Ensures file encryption for storage accounts" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>Limpieza de la implementación de PowerShell

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]


````cli

az policy definition create --name 'storage-account-file-encryption' --display-name 'Ensure storage file encryption' --description 'Ensures file encryption for storage accounts' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-file-encryption/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "storage-account-file-encryption"

````

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar ejemplos de plantillas de Azure Policy adicionales en [Plantillas para Azure Policy](../json-samples.md).
