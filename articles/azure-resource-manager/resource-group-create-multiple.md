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
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 8ecf7c058b90fd18e41fd4e1cbc29e22dfeb0883
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Implementación de varias instancias de recursos en plantillas de Azure Resource Manager
En este tema se muestra cómo iterar en la plantilla del Administrador de recursos de Azure para crear varias instancias de un recurso.

## <a name="copy-and-copyindex"></a>copy y copyIndex
El recurso para crear varias veces tiene el formato siguiente:

```json
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Tenga en cuenta el número de veces para recorrer en iteración se especifica en el objeto de copia:

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

El valor de recuento debe ser un número entero positivo y no puede ser superior a 800.

Tenga en cuenta que el nombre de cada recurso incluye la función `copyIndex()`, que devuelve la iteración actual del bucle.

```json
"name": "[concat('examplecopy-', copyIndex())]",
```

Si implementa tres sitios web, se denominan:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Para desplazar el valor de índice, puede pasar un valor de la función copyIndex(), como `copyIndex(1)`. El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Por lo tanto, con la misma plantilla que en el ejemplo anterior, pero especificando copyIndex(1), implementaría tres sitios web con los nombres:

* examplecopy-1
* examplecopy-2
* examplecopy-3

Resource Manager crea los recursos en paralelo. Por lo tanto, no se garantiza el orden en el que se crean. Para crear recursos iterados en orden, vea [Bucle secuencia para plantillas de Azure Resource Manager](resource-manager-sequential-loop.md). 

Solo puede aplicar el objeto copy a un recurso de nivel superior. No podrá aplicarlo a una propiedad de un tipo de recurso o a un recurso secundario. En el siguiente ejemplo de pseudocódigo se explica dónde puede aplicarse el objeto copy:

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* Yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* No, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* No, copy cannot be applied here. The resource must be promoted to top-level. */ 
      }
    ]
  }
] 
```

Para recorrer en iteración un recurso secundarios, lea [Creación de varias instancias de un recurso secundario](#create-multiple-instances-of-a-child-resource).

Aunque no se puede aplicar copy a una propiedad, dicha propiedad sigue siendo parte de las iteraciones del recurso que contiene la propiedad. Por lo tanto, puede usar copyIndex() en la propiedad para especificar los valores. Para crear varios valores para una propiedad, vea [Creación de varias instancias de propiedad en un tipo de recurso](resource-manager-property-copy.md).

## <a name="use-copy-with-array"></a>Uso de la copia con la matriz
La operación de copia es útil al trabajar con matrices, ya que puede iterar a través de cada elemento de la matriz. Para implementar tres sitios web con los nombres:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Use la siguiente plantilla:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

Tenga en cuenta que la función `length` se utiliza para especificar el número. Especifique la matriz como parámetro para la función length.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
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
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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
* Para crear recursos iterados en orden, vea [Bucle secuencia para plantillas de Azure Resource Manager](resource-manager-sequential-loop.md).
* Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


