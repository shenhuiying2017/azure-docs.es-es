---
title: "Funciones de la plantilla de Azure Resource Manager: numéricas | Microsoft Docs"
description: "Describe las funciones para usar en una plantilla de Azure Resource Manager para trabajar con números."
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
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 5844540801a6f0ff593b3f404f6815473c65a52e
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Funciones numéricas para las plantillas de Azure Resource Manager

El Administrador de recursos ofrece las siguientes funciones para trabajar con números enteros:

* [agregar](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [min](#min)
* [max](#max)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

## <a name="add"></a>agregar
`add(operand1, operand2)`

Devuelve la suma de los dos enteros especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- | 
|operand1 |Sí |int |Primer número que se agregará. |
|operand2 |Sí |int |Segundo número que se agregará. |

### <a name="examples"></a>Ejemplos

El ejemplo siguiente agrega dos parámetros.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero que contiene la suma de los parámetros.

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

Devuelve el índice actual de un bucle de iteración. 

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| Offset |No |int |El número que se agregará al valor de iteración basado en cero. |

### <a name="remarks"></a>Comentarios

Esta función siempre se usa con un objeto **copy** . Si no se proporciona ningún valor para **offset**, se devuelve el valor de la iteración actual. El valor del iteración comienza en cero. Para ver una descripción completa de cómo usar **copyIndex**, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra un bucle de copia y el valor de índice incluido en el nombre. 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>Valor devuelto

Un entero que representa el índice actual de la iteración.

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

Devuelve la división de enteros de los dos enteros especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| operand1 |Sí |int |Número que se va a dividir. |
| operand2 |Sí |int |Número que se usa para dividir. No puede ser 0. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se divide un parámetro por otro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero que representa la división.

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

Convierte el valor en un número de punto flotante. Solo use esta función al pasar parámetros personalizados a una aplicación, como una aplicación lógica.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |cadena o entero |El valor para convertir en número de punto flotante. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo usar float para pasar parámetros a una aplicación lógica:

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
        "custom1": {
            "value": "[float('3.0')]"
        },
        "custom2": {
            "value": "[float(3)]"
        },
```

### <a name="return-value"></a>Valor devuelto
Un número de punto flotante.

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

Convierte el valor especificado en un entero.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sí |cadena o entero |Valor que se convierte en entero. |

### <a name="examples"></a>Ejemplos

En el siguiente ejemplo se convierte el valor del parámetro proporcionado por el usuario en entero.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[variables('intValue')]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero.

<a id="min" />

## <a name="min"></a>Min
`min (arg1)`

Devuelve el valor mínimo de una matriz de enteros o una lista separada por comas de enteros.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor mínimo. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo utilizar min con una matriz y una lista de enteros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor mínimo de la colección.

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

Devuelve el valor máximo de una matriz de enteros o una lista separada por comas de enteros.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz de enteros, o lista separada por comas de enteros |La colección para obtener el valor máximo. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo utilizar max con una matriz y una lista de enteros:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero que representa el valor máximo de la colección.

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

Devuelve el resto de la división de enteros de los dos enteros especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| operand1 |Sí |int |Número que se va a dividir. |
| operand2 |Sí |int |Número que se usa para dividir; no puede ser 0. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se devuelve el resultado de dividir un parámetro por otro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto
Un entero que representa el resto.

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

Devuelve la multiplicación de los dos enteros especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| operand1 |Sí |int |Primer número que se va a multiplicar. |
| operand2 |Sí |int |Segundo número que se va a multiplicar. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se multiplica un parámetro por otro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Un entero que representa la multiplicación.

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

Devuelve la resta de los dos enteros especificados.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| operand1 |Sí |int |Número del que se resta. |
| operand2 |Sí |int |Número que se resta. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se resta un parámetro de otro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto
Un entero que representa la resta.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).


