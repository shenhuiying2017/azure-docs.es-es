---
title: "Implementación de varias instancias de recursos de Azure | Microsoft Docs"
description: "Use la operación de copia y matrices en una plantilla del Administrador de recursos de Azure para iterar varias veces al implementar recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: a8e35456af8c9f2cf1bf9e9c364e33641f29a477
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017


---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Implementación de varias instancias de un recurso o una propiedad en plantillas de Azure Resource Manager
En este tema se muestra cómo iterar en la plantilla del Administrador de recursos de Azure para crear varias instancias de un recurso.

## <a name="resource-iteration"></a>Iteración de recursos
Para crear varias instancias de un tipo de recurso, agregue un elemento `copy` al tipo de recurso. En el elemento de copia, especifique el número de iteraciones y un nombre para este bucle. El valor de recuento debe ser un número entero positivo y no puede ser superior a 800. Resource Manager crea los recursos en paralelo. Por lo tanto, no se garantiza el orden en el que se crean. Para crear recursos iterados en secuencia, consulte [Copia en serie](#serial-copy). 

El recurso para crear varias veces tiene el formato siguiente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Tenga en cuenta que el nombre de cada recurso incluye la función `copyIndex()`, que devuelve la iteración actual del bucle. `copyIndex()` es de base cero. Así, en el ejemplo siguiente:

```json
"name": "[concat('storage', copyIndex())]",
```

Crea estos nombres:

* storage0
* storage1
* storage2.

Para desplazar el valor de índice, puede pasar un valor de la función copyIndex(). El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Así, en el ejemplo siguiente:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Crea estos nombres:

* storage1
* storage2
* storage3

La operación de copia es útil al trabajar con matrices, ya que puede iterar a través de cada elemento de la matriz. Use la función `length` en la matriz para especificar el número de iteraciones, y `copyIndex` para recuperar el índice actual de la matriz. Así, en el ejemplo siguiente:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Crea estos nombres:

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>Copia en serie

Al usar el elemento de copia para crear varias instancias de un tipo de recurso, Resource Manager implementa esas instancias en paralelo de forma predeterminada. Sin embargo, es posible que quiera especificar que los recursos se implementen en secuencia. Por ejemplo, al actualizar un entorno de producción, puede que quiera escalonar las actualizaciones para que solo una cantidad determinada se actualice al mismo tiempo.

Resource Manager proporciona las propiedades sobre el elemento de copia que le permiten implementar varias instancias en serie. En el elemento de copia, establezca `mode` en **serial** y `batchSize` en el número de instancias que se implementarán a la vez. Con mode establecido en serial, Resource Manager crea una dependencia en las instancias anteriores del bucle, por lo que no se inicia ningún lote hasta que se completa el lote anterior.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

La propiedad mode también acepta **parallel**, que es el valor predeterminado.

Para probar la copia en serie sin crear recursos reales, use la siguiente plantilla, que implementa plantillas anidadas vacías:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

En el historial de implementación, observe que las implementaciones anidadas se procesan en secuencia.

![implementación en serie](./media/resource-group-create-multiple/serial-copy.png)

Para un escenario más realista, en el ejemplo siguiente se implementan dos instancias a la vez de una VM de Linux a partir de una plantilla anidada:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="depend-on-resources-in-a-loop"></a>Dependencia de los recursos de un bucle
Especifique que un recurso se implemente después de otro recurso mediante el elemento `dependsOn`. Para implementar un recurso que dependa de la colección de recursos de un bucle, proporcione el nombre del bucle copy en el elemento dependsOn. En el ejemplo siguiente se muestra cómo implementar tres cuentas de almacenamiento antes de implementar la máquina virtual. La definición de la máquina virtual no se muestra. Tenga en cuenta que el elemento copy tiene el nombre establecido en `storagecopy` y el elemento dependsOn para las máquinas virtuales también se establece en `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Creación de varias instancias de un recurso secundario
No puede usar un bucle copy en un recurso secundario. Para crear varias instancias de un recurso que se define normalmente como anidado dentro de otro recurso, debe crear en su lugar dicho recurso como uno de nivel superior. La relación con el recurso principal se define a través de las propiedades type y name.

Por ejemplo, supongamos que suele definir un conjunto de datos como un recurso secundario dentro de una factoría de datos.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Para crear varias instancias de conjuntos de datos, muévalos fuera de la factoría de datos. El conjunto de datos debe estar en el mismo nivel que la factoría de datos, pero seguirá siendo un recurso secundario de la factoría de datos. La relación entre el conjunto de datos y la factoría de datos se conserva a través de los parámetros type y name. Puesto que la propiedad de type ya no se puede inferir de su posición en la plantilla, debe proporcionar el nombre completo del tipo con el formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Para establecer una relación de principal-secundario con una instancia de la factoría de datos, proporcione un nombre para el conjunto de datos que incluya el nombre de recurso principal. Utilice el formato: `{parent-resource-name}/{child-resource-name}`.  

En el siguiente ejemplo se muestra la implementación:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre las secciones de una plantilla, consulte el artículo sobre cómo [crear plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


