---
title: "Ejemplo de JSON de Azure Policy: Permitir una imagen de máquina virtual personalizada desde un grupo de recursos | Microsoft Docs"
description: "Esta directiva de ejemplo de JSON requiere que las imágenes personalizadas provengan de un grupo de recursos aprobado."
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
ms.openlocfilehash: 3e47a6021bfbebd1a5fc9cbcd9601099f830990b
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="allow-custom-vm-image-from-a-resource-group"></a>Permitir una imagen de máquina virtual personalizada desde un grupo de recursos

Esta directiva de ejemplo de JSON requiere que las imágenes personalizadas provengan de un grupo de recursos aprobado. Se especifica el nombre del grupo de recursos aprobado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../policy-templates/samples/compute/custom-image-from-rg/azurepolicy.json "Allow custom VM image from a Resource Group")]


Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

````powershell
$definition = New-AzureRmPolicyDefinition -Name "custom-image-from-rg" -DisplayName "Allow custom VM image from a Resource Group" -description "This policy allows only usage of images from a resource group" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' -Mode All
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

az policy definition create --name 'custom-image-from-rg' --display-name 'Allow custom VM image from a Resource Group' --description 'This policy allows only usage of images from a resource group' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/custom-image-from-rg/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "custom-image-from-rg"

````

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar ejemplos de plantillas de Azure Policy adicionales en la documentación de Azure Policy
