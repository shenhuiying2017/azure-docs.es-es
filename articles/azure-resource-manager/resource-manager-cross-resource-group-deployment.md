---
title: "Implementación de recursos de Azure en varios grupos de recursos y suscripciones | Microsoft Docs"
description: "Muestra cómo tener como destino más de un grupo de recursos y una suscripción de Azure durante la implementación."
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
ms.date: 12/18/2017
ms.author: tomfitz
ms.openlocfilehash: 48ba938db992ce192d8afb51365d87fba4422590
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Implementación de recursos de Azure en más de un grupo de recursos o una suscripción

Por lo general, todos los recursos de la plantilla se implementan en un único [grupo de recursos](resource-group-overview.md). Sin embargo, existen escenarios en los que desea implementar un conjunto de recursos juntos pero colocarlos en distintos grupos de recursos o suscripciones. Por ejemplo, puede que desee implementar la máquina virtual de copia de seguridad para Azure Site Recovery en un grupo de recursos y una ubicación independientes. Resource Manager permite usar plantillas anidadas para tener como destino grupos de recursos y suscripciones diferentes a los usados para la plantilla principal.

## <a name="specify-a-subscription-and-resource-group"></a>Especificación de una suscripción y un grupo de recursos

Para usar como destino un recurso diferente, utilice una plantilla anidada o vinculada. El tipo de recurso `Microsoft.Resources/deployments` proporciona los parámetros para `subscriptionId` y `resourceGroup`. Estas propiedades permiten especificar un grupo de recursos y suscripción diferentes para la implementación anidada. Todos los grupos de recursos deben existir antes de que se ejecute la implementación. Si no se especifica el grupo de recursos o el identificador de la suscripción, se utilizan los de la plantilla primaria.

Para especificar un grupo de recursos y una suscripción diferentes, use:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Si los grupos de recursos se encuentran en la misma suscripción, puede quitar el valor **subscriptionId**.

En el ejemplo siguiente se implementan dos cuentas de almacenamiento: una en el grupo de recursos especificado durante la implementación y otra en un grupo de recursos que se especifica en el parámetro `secondResourceGroup`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Si establece `resourceGroup` en el nombre de un grupo de recursos que no existe, la implementación generará un error.

Para implementar la plantilla de ejemplo, use Azure PowerShell 4.0.0 o posterior o la CLI de Azure 2.0.0 o posterior.

## <a name="use-the-resourcegroup-function"></a>Uso de la función resourceGroup()

En el caso de las implementaciones de grupos de recursos, la [función resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) se resuelve de manera diferente en función de cómo se especifica la plantilla anidada. 

Si inserta una plantilla dentro de otra, la función resourceGroup() de la plantilla anidada se resuelve en el grupo de recursos primario. Una plantilla insertada usa el formato siguiente:

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

Si existe un vínculo a una plantilla independiente, la función resourceGroup() de la plantilla vinculada se resuelve en el grupo de recursos anidado. Una plantilla vinculada usa el formato siguiente:

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

## <a name="example-templates"></a>Plantillas de ejemplo

Las plantillas siguientes muestran varias implementaciones de grupos de recursos. Después de la tabla se muestran algunos scripts para implementar las plantillas.

|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
|[Plantilla de varias suscripciones](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Implementa una cuenta de almacenamiento en un grupo de recursos y una cuenta de almacenamiento en un segundo grupo de recursos. Se incluye un valor para el identificador de suscripción cuando el segundo grupo de recursos está en una suscripción diferente. |
|[Plantilla de propiedades de varios grupos de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Muestra la resolución de la función `resourceGroup()`. No implementa ningún recurso. |

### <a name="powershell"></a>PowerShell

Para implementar dos cuentas de almacenamiento en dos grupos de recursos de la **misma suscripción** con PowerShell use:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Para implementar dos cuentas de almacenamiento en **dos suscripciones** con PowerShell, use:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Para probar la resolución del **objeto del grupo de recursos** para la plantilla primaria, la plantilla en línea y la plantilla vinculada con PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

### <a name="azure-cli"></a>Azure CLI

Para implementar dos cuentas de almacenamiento en dos grupos de recursos de la **misma suscripción** con la CLI de Azure, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Para implementar dos cuentas de almacenamiento en **dos suscripciones** con la CLI de Azure, use:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Para probar la resolución del **objeto del grupo de recursos** para la plantilla primaria, la plantilla en línea y la plantilla vinculada con la CLI de Azure, use:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>pasos siguientes

* Para entender cómo definir parámetros en la plantilla, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md).
