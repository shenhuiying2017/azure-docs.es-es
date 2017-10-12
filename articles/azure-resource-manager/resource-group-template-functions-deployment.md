---
title: "Funciones de la plantilla de Azure Resource Manager: implementación | Microsoft Docs"
description: "Describe las funciones para usar en una plantilla de Azure Resource Manager para recuperar información de implementación."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 17fe2bc467acc5542d021961a066940dbecf6120
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Funciones de implementación para las plantillas de Azure Resource Manager 

El Administrador de recursos ofrece las siguientes funciones para obtener valores de las secciones de la plantilla y valores relacionados con la implementación:

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Para obtener valores de recursos, grupos de recursos o suscripciones, consulte [Funciones de recursos](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>deployment
`deployment()`

Devuelve información sobre la operación de implementación actual.

### <a name="return-value"></a>Valor devuelto

Esta función devuelve el objeto pasado durante la implementación. Las propiedades del objeto devuelto varían en función de si el objeto de implementación se ha pasado como un vínculo o como un objeto en línea. Cuando se pasa el objeto de implementación en línea, como cuando se usa el parámetro **-TemplateFile** en Azure PowerShell para orientarlo a un archivo local, el objeto devuelto tiene el formato siguiente:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Cuando el objeto se pasa como un vínculo, como cuando se usa el parámetro **-TemplateUri** para orientarlo a un objeto remoto, se devuelve el objeto en el formato siguiente: 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>Comentarios

Puede usar deployment() para establecer un vínculo con otra plantilla basada en el identificador URI de la plantilla primaria.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="example"></a>Ejemplo

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) siguiente devuelve el objeto de implementación:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

El ejemplo anterior devuelve el objeto siguiente:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Devuelve un valor de parámetro. El nombre del parámetro especificado debe definirse en la sección de parámetros de la plantilla.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| parameterName |Sí |cadena |El nombre del parámetro que se va a devolver. |

### <a name="return-value"></a>Valor devuelto

Valor del parámetro especificado.

### <a name="remarks"></a>Comentarios

Por lo general, se usan parámetros para establecer los valores de recurso. En el ejemplo siguiente se establece el nombre del sitio web en el valor del parámetro pasado durante la implementación.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Ejemplo

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) siguiente se muestra un uso simplificado de la función de los parámetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | opción 1 |
| intOutput | int | 1 |
| objectOutput | Objeto | {"one": "a", "two": "b"} |
| arrayOutput | Matriz | [1, 2, 3] |
| crossOutput | String | opción 1 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| variableName |Sí |string |El nombre de la variable que se va a devolver. |

### <a name="return-value"></a>Valor devuelto

Valor de la variable especificada.

### <a name="remarks"></a>Comentarios

Por lo general, para simplificar la plantilla se usan variables para crear valores complejos de una sola vez. En el ejemplo siguiente se crea un nombre único para una cuenta de almacenamiento.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Ejemplo

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) siguiente devuelve distintos valores de variable.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Matriz | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Objeto | {"property1": "value1", "property2": "value2"} |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).

