---
title: "Exportación de plantillas de Resource Manager con Azure PowerShell | Microsoft Docs"
description: Use Azure Resource Manager y Azure PowerShell para exportar una plantilla desde un grupo de recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 7543811eb9448222b6e7c266756e68debc7d54be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Exportación de plantillas de Azure Resource Manager con Azure PowerShell

Resource Manager permite exportar una plantilla de Resource Manager a partir de los recursos existentes en una suscripción. Puede usar esa plantilla generada para aprender sobre la sintaxis de plantillas o para automatizar la nueva implementación de su solución según sea necesario.

Es importante tener en cuenta que hay dos formas diferentes de exportar una plantilla:

* Puede exportar la plantilla que utilizó para una implementación. La plantilla exportada incluye todos los parámetros y variables exactamente como aparecían en la plantilla original. Este enfoque es útil cuando se necesita recuperar una plantilla.
* Puede exportar una plantilla que representa el estado actual del grupo de recursos. La plantilla exportada no se basa en ninguna plantilla que usara para la implementación. Al contrario, crea una plantilla que es una instantánea del grupo de recursos. La plantilla exportada tiene muchos valores codificados de forma rígida y es probable que no tenga tantos parámetros como normalmente se definirían. Este enfoque resulta útil cuando se ha modificado el grupo de recursos. Ahora, debe capturar el grupo de recursos como plantilla.

En este tema se muestran ambos métodos.

## <a name="deploy-a-solution"></a>Implementación de una solución

Para ilustrar ambos enfoques para exportar una plantilla, empecemos por la implementación de una solución en la suscripción. Si ya tiene un grupo de recursos en la suscripción que desee exportar, no es necesario implementar esta solución. Sin embargo, el resto de este artículo hace referencia a la plantilla para esta solución. El script de ejemplo implementa una cuenta de almacenamiento.

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Guardado de una plantilla desde el historial de implementación

Puede recuperar una plantilla desde el historial de implementación mediante el comando [Save-AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate). En el ejemplo siguiente se guarda la plantilla implementada previamente:

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Devuelve la ubicación de la plantilla.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Abra el archivo y tenga en cuenta que es la misma plantilla que usó para la implementación. Los parámetros y variables coinciden con la plantilla de GitHub. Puede volver a implementar esta plantilla.

## <a name="export-resource-group-as-template"></a>Exportación de un grupo de recursos como una plantilla

En lugar de recuperar una plantilla del historial de implementación, puede recuperar una plantilla que represente el estado actual de un grupo de recursos mediante el uso del comando [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup). Utilice este comando si ha realizado muchos cambios en el grupo de recursos y ninguna plantilla existente representa todos los cambios.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

Devuelve la ubicación de la plantilla.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Abra el archivo y tenga en cuenta que es diferente de la plantilla en GitHub. Tiene parámetros diferentes y no hay ninguna variable. La ubicación y las SKU de almacenamiento tienen una codificación de forma rígida en los valores. En el ejemplo siguiente se muestra la plantilla exportada, pero la plantilla tiene un nombre de parámetro ligeramente diferente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Puede volver a implementar esta plantilla, pero tendrá que adivinar el nombre único para la cuenta de almacenamiento. El nombre del parámetro es ligeramente diferente.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Personalización de la plantilla exportada

Puede modificar esta plantilla para que sea más flexible y fácil de usar. Para permitir más ubicaciones, cambie la propiedad de ubicación para utilizar la misma ubicación que el grupo de recursos:

```json
"location": "[resourceGroup().location]",
```

Para evitar tener que adivinar un nombre único para la cuenta de almacenamiento, quite el parámetro para el nombre de cuenta de almacenamiento. Agregue un parámetro para un sufijo de nombre de almacenamiento y una SKU de almacenamiento:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Agregue una variable que construya el nombre de cuenta de almacenamiento con la función uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Establezca el nombre de la cuenta de almacenamiento en la variable:

```json
"name": "[variables('storageAccountName')]",
```

Establezca la SKU en el parámetro:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

La plantilla ahora tiene el aspecto siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Vuelva a implementar la plantilla modificada.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el uso del portal para exportar una plantilla, vea [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
