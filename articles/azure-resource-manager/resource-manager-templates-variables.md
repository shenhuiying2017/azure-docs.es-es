---
title: Variables en las plantillas de Azure Resource Manager | Microsoft Docs
description: "Describe cómo definir variables en las plantillas de Azure Resource Manager mediante la sintaxis declarativa de JSON."
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
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Sección de variables de plantillas de Azure Resource Manager
En la sección de variables, se crean valores que pueden usarse en toda la plantilla. No es necesario definir las variables, pero a menudo simplifican la plantilla reduciendo expresiones complejas.

## <a name="define-and-use-a-variable"></a>Definición y uso de una variable

El ejemplo siguiente muestra una variable de definición. Crea un valor de cadena para un nombre de cuenta de almacenamiento. Usa varias funciones de plantilla para obtener un valor de parámetro y concatenarlo a una cadena única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Use la variable al definir el recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Definiciones disponibles

En el ejemplo anterior se muestra una manera de definir una variable. Puede utilizar cualquiera de las definiciones siguientes:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Variables de configuración

Puede usar tipos complejos de JSON para definir los valores relacionados con un entorno. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

En parámetros, se crea un valor que indica que valores de configuración usar.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Puede recuperar la configuración actual con:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Usar el elemento de copia en una definición de variable

Puede usar la sintaxis **copy** para crear una variable con una matriz de varios elementos. Proporcione la cantidad de elementos. Cada elemento contiene las propiedades dentro del objeto **input**. Puede usar copy dentro de una variable o para crear la variable. Al definir una variable y usar **copiar** en esa variable, se crea un objeto que tiene una propiedad de matriz. Cuando usa **copiar** en el nivel superior y define uno o más variables dentro del mismo, se crean una o más matrices. En el ejemplo siguiente se muestran ambos enfoques:

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

La variable **disk-array-on-object** contiene el siguiente objeto con una matriz denominada **disks**:

```json
{
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    }
  ]
}
```

La variable **disks-top-level-array** contiene la siguiente matriz:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  }
]
```

También puede especificar más de un objeto cuando use la copia para crear variables. En el siguiente ejemplo se definen dos matrices como variables. Una se denomina **disks-top-level-array** y tiene cinco elementos. La otra se llama **a-different-array** y tiene tres elementos.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Este enfoque funciona bien cuando es necesario tomar valores de parámetro y asegurarse de que están en el formato correcto para un valor de plantilla. En el ejemplo siguiente se da formato a valores de parámetro para su uso en la definición de reglas de seguridad:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="recommendations"></a>Recomendaciones
La información siguiente puede ser útil cuando se trabaja con variables:

* Use las variables para los valores que deba utilizar más de una vez en una plantilla. Si un valor se usa solo una vez, codificarlo de forma rígida hace que la plantilla resulte más fácil de leer.
* No se puede usar la función [reference](resource-group-template-functions-resource.md#reference) en la sección **variables** de la plantilla. La función **reference** deriva su valor desde el estado de tiempo de ejecución del recurso. Sin embargo, las variables se resuelven durante el análisis inicial de la plantilla. Construya valores que requieran la función **reference** directamente en las secciones **resources** u **outputs** de la plantilla.
* Incluya variables para los nombres de recursos que deben ser únicos.

## <a name="example-templates"></a>Plantillas de ejemplo

Estas plantillas de ejemplo muestran algunos escenarios para usar las variables. Impleméntelos para probar cómo se controlan las variables en diferentes escenarios. 

|Plantilla  |Descripción  |
|---------|---------|
| [definiciones de variable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Muestra los diferentes tipos de variables. La plantilla no implementa ningún recurso. Genera valores de variable y devuelve esos valores. |
| [variable de configuración](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Muestra el uso de una variable que define los valores de configuración. La plantilla no implementa ningún recurso. Genera valores de variable y devuelve esos valores. |
| [reglas de seguridad de red](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) y [archivo de parámetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Genera una matriz en el formato correcto para asignar las reglas de seguridad a un grupo de seguridad de red. |


## <a name="next-steps"></a>Pasos siguientes
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
* Para obtener información detallada sobre las funciones que se pueden usar dentro de una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para combinar varias plantillas en la implementación, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Puede que necesite usar los recursos que existen dentro de un grupo de recursos diferente. Este escenario es habitual al trabajar con cuentas de almacenamiento o redes virtuales que se comparten entre varios grupos de recursos. Para obtener más información, vea la [función resourceId](resource-group-template-functions-resource.md#resourceid).