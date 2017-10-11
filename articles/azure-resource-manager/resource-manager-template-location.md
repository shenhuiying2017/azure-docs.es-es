---
title: "Ubicación para recursos en plantillas de Azure | Microsoft Docs"
description: "Muestra cómo establecer una ubicación para un recurso en una plantilla de Azure Resource Manager"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
ms.openlocfilehash: 73e50a593c41e841dcaf184abb895406ff5001e9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Establecimiento de la ubicación para recursos en plantillas de Azure Resource Manager
Al implementar una plantilla, debe proporcionar una ubicación para cada recurso. Este tema muestra cómo determinar las ubicaciones que están disponibles con su suscripción para cada tipo de recurso.

## <a name="determine-supported-locations"></a>Determinación de las ubicaciones admitidas

Para obtener una lista completa de las ubicaciones admitidas para cada tipo de recurso, consulte [Productos disponibles por región](https://azure.microsoft.com/regions/services/). No obstante, es posible que la suscripción no tenga acceso a todas las ubicaciones de esa lista. Para ver una lista personalizada de las ubicaciones que están disponibles con su suscripción, utilice Azure PowerShell o la CLI de Azure. 

En el ejemplo siguiente se usa PowerShell para obtener las ubicaciones para el tipo de recurso `Microsoft.Web\sites`:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

En el ejemplo siguiente se usa la CLI de Azure 2.0 para obtener las ubicaciones para el tipo de recurso `Microsoft.Web\sites`:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>Establecimiento de la ubicación en la plantilla

Después de determinar las ubicaciones admitidas para los recursos, debe establecer esa ubicación en la plantilla. La manera más fácil de establecer este valor consiste en crear un grupo de recursos en una ubicación que admita los tipos de recursos y establecer cada ubicación en `[resourceGroup().location]`. Puede volver a implementar la plantilla en grupos de recursos de diferentes ubicaciones y no cambie ningún valor en la plantilla ni en los parámetros. 

En el ejemplo siguiente se muestra una cuenta de almacenamiento que está implementada en la misma ubicación que el grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Si tiene que codificar la ubicación en la plantilla, indique el nombre de una de las regiones admitidas. En el ejemplo siguiente se muestra una cuenta de almacenamiento que está siempre implementada en Centro-Norte de EE. UU:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para más recomendaciones sobre la creación de plantillas, consulte [Procedimientos recomendados para crear plantillas de Azure Resource Manager](resource-manager-template-best-practices.md).

