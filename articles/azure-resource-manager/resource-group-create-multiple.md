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
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: e19833cb58f37f5f8b83d5558d74255583137684
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Implementación de varias instancias de un recurso o una propiedad en plantillas de Azure Resource Manager
En este artículo se muestra cómo implementar un recurso de forma condicional y cómo realizar la iteración en la plantilla de Azure Resource Manager para crear varias instancias de un recurso.

## <a name="conditionally-deploy-resource"></a>Implementar recursos de forma condicional

Si durante la implementación debe decidir si crear una o ninguna instancia de un recurso, use el elemento `condition`. El valor de este elemento se resuelve como true o false. Cuando el valor es true, el recurso se implementa. Cuando el valor es false, el recurso no se implementa. Por ejemplo, para especificar si se implementa una nueva cuenta de almacenamiento o se usa una cuenta de almacenamiento existente, use:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Iteración de recursos
Si durante la implementación debe decidir si crear una o varias instancias de un recurso, agregue un elemento `copy` al tipo de recurso. En el elemento de copia, especifique el número de iteraciones y un nombre para este bucle. El valor de recuento debe ser un número entero positivo y no puede ser superior a 800. 

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

Resource Manager crea los recursos en paralelo de forma predeterminada. Por lo tanto, no se garantiza el orden en el que se crean. Sin embargo, es posible que quiera especificar que los recursos se implementen en secuencia. Por ejemplo, al actualizar un entorno de producción, puede que quiera escalonar las actualizaciones para que solo una cantidad determinada se actualice al mismo tiempo.

Para implementar en serie varias instancias de un recurso, establezca `mode` en el valor **serial** y `batchSize` en el número de instancias que se implementarán a la vez. Con mode establecido en serial, Resource Manager crea una dependencia en las instancias anteriores del bucle, por lo que no se inicia ningún lote hasta que se completa el lote anterior.

Por ejemplo, para implementar en serie dos cuentas de almacenamiento a la vez, use lo siguiente:

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
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            }
        }
    ],
    "outputs": {}
}
``` 

La propiedad mode también acepta **parallel**, que es el valor predeterminado.

## <a name="property-iteration"></a>Iteración de propiedades

Para crear varios valores para una propiedad de un recurso, agregue una matriz `copy` en el elemento properties. Esta matriz contiene objetos, y cada objeto tiene las siguientes propiedades:

* nombre: el nombre de la propiedad para la que se van a crear varios valores
* recuento: el número de valores que se van a crear
* entrada: un objeto que contiene los valores que se van a asignar a la propiedad  

En el ejemplo siguiente se muestra cómo aplicar `copy` a la propiedad dataDisks en una máquina virtual:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Tenga en cuenta que, cuando se usa `copyIndex` dentro de una iteración de propiedad, debe proporcionar el nombre de la iteración. No tiene que proporcionar el nombre cuando se usa con la iteración de recursos.

Resource Manager expande la matriz `copy` durante la implementación. El nombre de la matriz se convierte en el nombre de la propiedad. Los valores de entrada se convierten en las propiedades del objeto. La plantilla implementada se convierte en:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Puede usar la iteración de recursos y propiedades conjuntamente. Haga referencia a la iteración de la propiedad por el nombre.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
    "copy":{
        "count": 2,
        "name": "vnetloop"
    },
    "location": "[resourceGroup().location]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "copy": [
            {
                "name": "subnets",
                "count": 2,
                "input": {
                    "name": "[concat('subnet-', copyIndex('subnets'))]",
                    "properties": {
                        "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
                    }
                }
            }
        ]
    }
}
```

## <a name="variable-iteration"></a>Iteración variable

Para crear varias instancias de una variable, use el elemento `copy` en la sección de variables. Puede crear varias instancias de objetos con valores relacionados y, a continuación, asignar esos valores a las instancias del recurso. Puede usar la opción de copiar para crear un objeto con una propiedad de matriz o una matriz. En el ejemplo siguiente se muestran ambos enfoques:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
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
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
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

## <a name="iteration-for-a-child-resource"></a>Iteración para un recurso secundario
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

## <a name="example-templates"></a>Plantillas de ejemplo

En los ejemplos siguientes se muestran escenarios comunes para la creación de varios recursos o propiedades.

|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
|[Almacenamiento de copias](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Implementa varias cuentas de almacenamiento con un número de índice en el nombre. |
|[Almacenamiento de copias en serie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Implementa varias cuentas de almacenamiento, una tras otra. El nombre incluye el número de índice. |
|[Almacenamiento de copias con una matriz](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Implementa varias cuentas de almacenamiento. El nombre incluye el valor de una matriz. |
|[Máquina virtual con una red virtual, cuenta de almacenamiento o IP pública nueva o existente](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) |Implementa recursos nuevos o existentes de manera virtual con una máquina virtual. |
|[Implementación de máquinas virtuales con un número variable de discos de datos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Implementa varios discos de datos con una máquina virtual. |
|[Variables de copia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Muestra las diferentes maneras de efectuar una iteración en las variables. |
|[Varias reglas de seguridad](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Implementa varias reglas de seguridad en un grupo de seguridad de red. Crea las reglas de seguridad a partir de un parámetro. |

## <a name="next-steps"></a>pasos siguientes
* Para obtener información sobre las secciones de una plantilla, consulte el artículo sobre cómo [crear plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

