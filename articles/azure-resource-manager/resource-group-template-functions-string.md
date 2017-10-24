---
title: 'Funciones de la plantilla de Azure Resource Manager: cadena | Microsoft Docs'
description: Describe las funciones para usar en una plantilla de Azure Resource Manager para trabajar con cadenas.
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
ms.openlocfilehash: eeb3e46d9b8a5822b1aea3cc62bb214f3c3fec43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funciones de cadena para las plantillas de Azure Resource Manager

El Administrador de recursos ofrece las siguientes funciones para trabajar con cadenas:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [cadena](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

<a id="base64" />

## <a name="base64"></a>base64
`base64(inputString)`

Devuelve la representación de base64 de la cadena de entrada.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| inputString |Sí |string |Valor que se va a devolver como una representación de base64. |

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene la representación en base64.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se muestra cómo utilizar la función de base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

Convierte una representación en base64 a un objeto JSON.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| base64Value |Sí |string |La representación en base64 para convertir en un objeto JSON. |

### <a name="return-value"></a>Valor devuelto

Un objeto JSON.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se utiliza la función base64ToJson para convertir un valor base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

Convierte una representación en base64 en una cadena.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| base64Value |Sí |cadena |La representación en base64 para convertir en una cadena. |

### <a name="return-value"></a>Valor devuelto

Una cadena del valor convertido de base64.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se utiliza la función base64ToString para convertir un valor base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Objeto | {"one": "a", "two": "b"} |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/base64.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat (arg1, arg2, arg3, ...)`

Combina varios valores de cadena y devuelve la cadena concatenada, o combina varias matrices y devuelve la matriz concatenada.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |cadena o matriz |El primer valor para la concatenación. |
| argumentos adicionales |No |cadena |Valores adicionales en orden secuencial para la concatenación. |

### <a name="return-value"></a>Valor devuelto
Una cadena o matriz de valores concatenados.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) siguiente se muestra cómo combinar dos valores de cadena y devolver una cadena concatenada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) siguiente se muestra cómo combinar dos matrices.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| return | Matriz | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

<a id="contains" />

## <a name="contains"></a>contains
`contains (container, itemToFind)`

Comprueba si una matriz contiene un valor, un objeto contiene una clave o una cadena contiene una subcadena.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| container |Sí |matriz, objeto o cadena |El valor que contiene el valor para buscar. |
| itemToFind |Sí |cadena o entero |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el elemento se encuentra; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) siguiente se muestra cómo utilizar contains con diferentes tipos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| stringTrue | Booleano | True |
| stringFalse | Booleano | False |
| objectTrue | Booleano | True |
| objectFalse | Booleano | False |
| arrayTrue | Booleano | True |
| arrayFalse | Booleano | False |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

Convierte un valor en un identificador URI de datos.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToConvert |Sí |cadena |El valor para convertir en un identificador URI de datos. |

### <a name="return-value"></a>Valor devuelto

Una cadena con formato de identificador URI de datos.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) siguiente se convierte un valor en un identificador URI de datos, y se convierte un identificador URI de datos en una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hola mundo. |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

Convierte un valor con formato de identificador URI de datos en una cadena.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Sí |cadena |El valor del identificador URI para convertir. |

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene el valor convertido.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) siguiente se convierte un valor en un identificador URI de datos, y se convierte un identificador URI de datos en una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hola mundo. |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/datauri.json
```

<a id="empty" /> 

## <a name="empty"></a>empty
`empty(itemToTest)`

Determina si una matriz, un objeto o una cadena están vacíos.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| itemToTest |Sí |matriz, objeto o cadena |El valor para comprobar si está vacío. |

### <a name="return-value"></a>Valor devuelto

Devuelve **True** si el valor está vacío; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) siguiente se comprueba si una matriz, un objeto y una cadena están vacíos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Booleano | True |
| objectEmpty | Booleano | True |
| stringEmpty | Booleano | True |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="endswith" />

## <a name="endswith"></a>endsWith
`endsWith(stringToSearch, stringToFind)`

Determina si una cadena termina con un valor. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |cadena |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el último carácter o caracteres de la cadena coinciden con el valor; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) siguiente se muestra cómo utilizar las funciones startsWith y endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Booleano | True |
| startsCapTrue | Booleano | True |
| startsFalse | Booleano | False |
| endsTrue | Booleano | True |
| endsCapTrue | Booleano | True |
| endsFalse | Booleano | False |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Devuelve el primer carácter de la cadena o el primer elemento de la matriz.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el primer elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

Una cadena del primer carácter, o el tipo (cadena, entero, matriz u objeto) del primer elemento en una matriz.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) siguiente se muestra cómo utilizar la primera función con una matriz y una cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

## <a name="guid"></a>GUID

`guid (baseString, ...)`

Crea un valor en el formato de un identificador único global en función de los valores proporcionados como parámetros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| baseString |Sí |cadena |El valor utilizado en la función hash para crear el GUID. |
| parámetros adicionales según sea necesario |No |cadena |Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad. |

### <a name="remarks"></a>Comentarios

Esta función es útil cuando se necesita crear un valor en el formato de un identificador único global. Proporciona valores de parámetros que limitan el ámbito de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación.

El valor devuelto no es una cadena aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 36 caracteres. Debe ser único globalmente.

En los ejemplos siguientes se muestra cómo utilizar un GUID para crear un valor único para niveles de uso común.

Único basado en la suscripción

```json
"[guid(subscription().subscriptionId)]"
```

Único basado en el grupo de recursos

```json
"[guid(resourceGroup().id)]"
```

Único basado en la implementación de un grupo de recursos

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene 36 caracteres en el formato de un identificador único global.

### <a name="examples"></a>Ejemplos

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) siguiente devuelve resultados de GUID:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/guid.json
```

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

Devuelve la primera posición de un valor dentro de una cadena. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |cadena |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa la posición del elemento que se va a buscar. El valor está basado en cero. Si no se encuentra el elemento, se devuelve -1.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) siguiente se muestra cómo utilizar las funciones indexOf y lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Devuelve el último carácter de la cadena, o el último elemento de la matriz.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el último elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

Una cadena del último carácter, o el tipo (cadena, entero, matriz u objeto) del último elemento de una matriz.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) siguiente se muestra cómo utilizar la última función con una matriz y una cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

Devuelve la última posición de un valor dentro de una cadena. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |cadena |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa la última posición del elemento que se va a buscar. El valor está basado en cero. Si no se encuentra el elemento, se devuelve -1.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) siguiente se muestra cómo utilizar las funciones indexOf y lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/indexof.json
```

<a id="length" />

## <a name="length"></a>length
`length(string)`

Devuelve el número de caracteres de una cadena, o elementos de una matriz.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |La matriz que se usará para obtener el número de elementos, o la cadena que se usará para obtener el número de caracteres. |

### <a name="return-value"></a>Valor devuelto

Un entero. 

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) siguiente se muestra cómo utilizar length con una matriz y una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

<a id="padleft" />

## <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

Devuelve una cadena alineada a la derecha agregando caracteres a la izquierda hasta alcanzar la longitud total especificada.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| valueToPad |Sí |cadena o entero |Valor que se va a alinear a la derecha. |
| totalLength |Sí |int |El número total de caracteres de la cadena devuelta. |
| paddingCharacter |No |carácter individual |El carácter que se va a usar para el relleno a la izquierda hasta alcanza la longitud total. El valor predeterminado es un espacio. |

Si la cadena original es mayor que el número de caracteres que se va a rellenar, no se agrega ningún carácter.

### <a name="return-value"></a>Valor devuelto

Una cadena con al menos el número de caracteres especificados.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) siguiente se muestra cómo rellenar el valor del parámetro proporcionado por el usuario agregando el carácter cero hasta que alcance el número total de caracteres. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/padleft.json
```

<a id="replace" />

## <a name="replace"></a>replace
`replace(originalString, oldString, newString)`

Devuelve una nueva cadena con todas las instancias de una cadena reemplazadas por otra cadena.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| originalString |Sí |cadena |Valor que tiene todas las instancias de una cadena reemplazadas por otra cadena. |
| oldString |Sí |cadena |Cadena que se va a quitar de la cadena original. |
| newString |Sí |cadena |La cadena que se va a agregar en lugar de la cadena eliminada. |

### <a name="return-value"></a>Valor devuelto

Una cadena con los caracteres reemplazados.

### <a name="examples"></a>Ejemplos

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) siguiente muestra cómo quitar todos los guiones de la cadena proporcionada por el usuario y cómo reemplazar parte de la cadena por otra cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/replace.json
```

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Devuelve una cadena con todos los caracteres después del número especificado de caracteres, o una matriz con todos los elementos después del número especificado de elementos.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena que se usará para la omisión. |
| numberToSkip |Sí |int |El número de elementos o caracteres que se van a omitir. Si este valor es 0 o un valor inferior, se devuelven todos los elementos o caracteres del valor. Si es mayor que la longitud de la matriz o la cadena, se devuelve una matriz o cadena vacía. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) siguiente se omite el número especificado de elementos de la matriz, y el número especificado de caracteres de la cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["three"] |
| stringOutput | String | two three |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="split" />

## <a name="split"></a>split
`split(inputString, delimiter)`

Devuelve una matriz de cadenas que contiene las subcadenas de la cadena de entrada que están delimitadas por los delimitadores especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| inputString |Sí |cadena |La cadena que se va a dividir. |
| delimiter |Sí |cadena o matriz de cadenas |Delimitador que se utilizará para dividir la cadena. |

### <a name="return-value"></a>Valor devuelto

Una matriz de cadenas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) siguiente se divide la cadena de entrada con una coma, y con una coma o un punto y coma.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| firstOutput | Matriz | ["one", "two", "three"] |
| secondOutput | Matriz | ["one", "two", "three"] |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/split.json
```

<a id="startswith" />

## <a name="startswith"></a>startsWith
`startsWith(stringToSearch, stringToFind)`

Determina si una cadena empieza con un valor. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |cadena |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el primer carácter o caracteres de la cadena coinciden con el valor; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) siguiente se muestra cómo utilizar las funciones startsWith y endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| startsTrue | Booleano | True |
| startsCapTrue | Booleano | True |
| startsFalse | Booleano | False |
| endsTrue | Booleano | True |
| endsCapTrue | Booleano | True |
| endsFalse | Booleano | False |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/startsendswith.json
```

<a id="string" />

## <a name="string"></a>cadena
`string(valueToConvert)`

Convierte el valor especificado en cadena.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sí | Cualquiera |El valor que se convierte en cadena. Se puede convertir cualquier tipo de valor, incluidos objetos y matrices. |

### <a name="return-value"></a>Valor devuelto

Cadena del valor convertido.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) siguiente se muestra cómo convertir distintos tipos de valores en cadenas:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/string.json
```

<a id="substring" />

## <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

Devuelve una subcadena que empieza en la posición de carácter especificada y que contiene el número especificado de caracteres.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToParse |Sí |cadena |La cadena original desde la que se extrae la subcadena. |
| startIndex |No |int |La posición de carácter inicial basado en cero de la subcadena. |
| length |No |int |El número de caracteres de la subcadena. Debe hacer referencia a una ubicación dentro de la cadena. |

### <a name="return-value"></a>Valor devuelto

Subcadena.

### <a name="remarks"></a>Comentarios

La función genera un error cuando la subcadena supera el final de la cadena. En el ejemplo siguiente se produce el error "Los parámetros index y length deben hacer referencia a una ubicación dentro de la cadena. Parámetro index: '0'; parámetro length: '11'; longitud del parámetro string: '10'.

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) siguiente se extrae una subcadena de un parámetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| substringOutput | String | two |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/substring.json
```

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Devuelve una cadena con el número especificado de caracteres desde el inicio de la cadena, o una matriz con el número especificado de elementos desde el inicio de la matriz.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena de la que se van a tomar los elementos. |
| numberToTake |Sí |int |El número de elementos o caracteres que se van a tomar. Si este valor es 0 o un valor inferior, se devolverá una matriz o cadena vacía. Si es mayor que la longitud de la matriz o cadena especificada, se devuelven todos los elementos de la matriz o cadena. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) siguiente se toma el número especificado de elementos de la matriz y de caracteres de la cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| arrayOutput | Matriz | ["one", "two"] |
| stringOutput | String | en |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

Convierte la cadena especificada a minúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToChange |Sí |string |Valor que se va a convertir a minúsculas. |

### <a name="return-value"></a>Valor devuelto

Cadena convertida a minúsculas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) siguiente se convierte un valor de parámetro a minúsculas y a mayúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

Convierte la cadena especificada a mayúsculas.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToChange |Sí |cadena |Valor que se va a convertir a mayúsculas. |

### <a name="return-value"></a>Valor devuelto

Cadena convertida a mayúsculas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) siguiente se convierte un valor de parámetro a minúsculas y a mayúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/tolower.json
```

<a id="trim" />

## <a name="trim"></a>trim
`trim (stringToTrim)`

Quita todos los caracteres de espacio en blanco iniciales y finales de la cadena especificada.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sí |cadena |Valor que se recortará. |

### <a name="return-value"></a>Valor devuelto

Cadena sin caracteres de espacio en blanco iniciales ni finales.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) siguiente se recortan los caracteres de espacio en blanco del parámetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| return | String | one two three |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/trim.json
```

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

Crea una cadena de hash determinista basada en los valores proporcionados como parámetros. 

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| baseString |Sí |string |Valor utilizado en la función hash para crear una cadena única. |
| parámetros adicionales según sea necesario |No |cadena |Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad. |

### <a name="remarks"></a>Comentarios

Esta función es útil cuando se debe crear un nombre único para un recurso. Proporciona valores de parámetros que limitan el ámbito de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación. 

El valor devuelto no es una cadena aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 13 caracteres. Debe ser único globalmente. Puede que desee combinar el valor con un prefijo de su convención de nomenclatura para crear un nombre que sea más fácil de reconocer. En el ejemplo siguiente se muestra el formato del valor devuelto. El valor real varía según los parámetros proporcionados.

    tcvhiyu5h2o5o

En los ejemplos siguientes se muestra cómo utilizar uniqueString a fin de crear un valor único para niveles de uso común.

Único basado en la suscripción

```json
"[uniqueString(subscription().subscriptionId)]"
```

Único basado en el grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Único basado en la implementación de un grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

En el ejemplo siguiente se muestra cómo crear un nombre único para una cuenta de almacenamiento basada en el grupo de recursos. Dentro del grupo de recursos, el nombre no es único si crea de la misma manera.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

### <a name="return-value"></a>Valor devuelto

Cadena que contiene 13 caracteres.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) siguiente se devuelven los resultados de uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uniquestring.json
```

<a id="uri" />

## <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

Crea un URI absoluto mediante la combinación de la cadena de relativeUri y baseUri.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| baseUri |Sí |cadena |La cadena de uri base. |
| relativeUri |Sí |string |La cadena de uri relativo que se agregará a la cadena de uri base. |

El valor del parámetro **baseUri** puede incluir un archivo específico, pero al construir el identificador URI, solo se usa la ruta de acceso base. Por ejemplo, al pasar `http://contoso.com/resources/azuredeploy.json` como parámetro baseUri, se obtiene como resultado un identificador URI base de `http://contoso.com/resources/`.

### <a name="return-value"></a>Valor devuelto

Una cadena que representa el identificador URI absoluto para los valores base y relativos.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo construir un vínculo a una plantilla anidada en función del valor de la plantilla principal.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

Codifica un identificador URI.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sí |string |El valor para codificar. |

### <a name="return-value"></a>Valor devuelto

Una cadena del valor codificado por el identificador URI.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

Devuelve una cadena del valor codificado por el identificador URI.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Sí |cadena |El valor codificado por el identificador URI para convertir en una cadena. |

### <a name="return-value"></a>Valor devuelto

Una cadena descodificada del valor codificado por el identificador URI.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| Nombre | Tipo | Valor |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

Para implementar esta plantilla de ejemplo con la CLI de Azure, use:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

Para implementar esta plantilla de ejemplo con PowerShell, use:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/uri.json
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).

