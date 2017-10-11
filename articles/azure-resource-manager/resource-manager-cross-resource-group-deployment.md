---
title: "Implementación de recursos de Azure en varios grupos de recursos | Microsoft Docs"
description: "Muestra cómo tener como destino más de un grupo de recursos de Azure durante la implementación."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Implementación de recursos de Azure en más de un grupo de recursos

Por lo general, todos los recursos de la plantilla se implementan en un único grupo de recursos. Sin embargo, existen escenarios en los que desea implementar un conjunto de recursos juntos pero colocarlos en distintos grupos de recursos. Por ejemplo, puede que desee implementar la máquina virtual de copia de seguridad para Azure Site Recovery en un grupo de recursos y una ubicación independientes. Resource Manager permite usar plantillas anidadas para tener como destino grupos de recursos diferentes al usado para la plantilla principal.

El grupo de recursos es el contenedor de ciclo de vida para la aplicación y su colección de recursos. Cree el grupo de recursos fuera de la plantilla y especifique el grupo de recursos de destino durante la implementación. Para ver una introducción a los grupos de recursos, consulte [Información general sobre Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Plantilla de ejemplo

Para usar como destino un recurso diferente, debe usar una plantilla anidada o vinculada durante la implementación. El tipo de recurso `Microsoft.Resources/deployments` proporciona un parámetro `resourceGroup`, que permite especificar un grupo de recursos distinto para la implementación anidada. Todos los grupos de recursos deben existir antes de que se ejecute la implementación. En el ejemplo siguiente se implementan dos cuentas de almacenamiento: una en el grupo de recursos especificado durante la implementación y otra en un grupo de recursos llamado `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Si establece `resourceGroup` en el nombre de un grupo de recursos que no existe, la implementación generará un error. Si no proporciona ningún valor para `resourceGroup`, Resource Manager usa el grupo de recursos principal.  

## <a name="deploy-the-template"></a>Implementación de la plantilla

Para implementar la plantilla de ejemplo, puede usar el portal, Azure PowerShell o la CLI de Azure. En el caso de Azure PowerShell o la CLI de Azure, debe usar una versión de mayo de 2017 o posterior. En los ejemplos se supone que ha guardado la plantilla localmente como un archivo denominado **crossrgdeployment.json**.

Para PowerShell:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Para la CLI de Azure:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

Una vez finalizada la implementación, verá dos grupos de recursos. Cada uno contiene una cuenta de almacenamiento.

## <a name="use-resourcegroup-function"></a>Usar la función resourceGroup()

En el caso de las implementaciones de grupos de recursos, la [función resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) se resuelve de manera diferente en función de cómo se especifica la plantilla anidada. 

Si inserta una plantilla dentro de otra, la función resourceGroup() de la plantilla anidada se resuelve en el grupo de recursos principal. Una plantilla insertada usa el formato siguiente:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Si vincula a una plantilla independiente, la función resourceGroup() de la plantilla vinculada se resuelve en el grupo de recursos anidado. Una plantilla vinculada usa el formato siguiente:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para entender cómo definir parámetros en la plantilla, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md).
