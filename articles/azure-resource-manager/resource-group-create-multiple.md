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
ms.date: 02/24/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 04a3866f88b00486c30c578699d34cd6e8e776d7
ms.openlocfilehash: 056ee5e67b9a6d396586c53b04d50f89e6fbb560
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Implementación de varias instancias de recursos en plantillas de Azure Resource Manager
En este tema se muestra cómo iterar en la plantilla del Administrador de recursos de Azure para crear varias instancias de un recurso.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex y length
En el recurso que se va a crear varias veces, puede definir un objeto **copy** que especifica el número de veces que se va a iterar. La copia tiene el formato siguiente:

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

Puede acceder al valor de iteración actual con la función **copyIndex()**. En el ejemplo siguiente se utiliza copyIndex con la función concat para construir un nombre.

```json
[concat('examplecopy-', copyIndex())]
```

Al crear varios recursos desde una matriz de valores, puede usar la función **length** para especificar el recuento. Especifique la matriz como parámetro para la función length.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Solo puede aplicar el objeto copy a un recurso de nivel superior. No podrá aplicarlo a una propiedad de un tipo de recurso o a un recurso secundario. Sin embargo, en este tema se muestra cómo especificar varios elementos para una propiedad y crear varias instancias de un recurso secundario. En el siguiente ejemplo de pseudocódigo se explica dónde puede aplicarse el objeto copy:

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* no, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* copy can be applied if resource is promoted to top level */ 
      }
    ]
  }
] 
```

Aunque no se puede aplicar **copy** a una propiedad, dicha propiedad sigue siendo parte de las iteraciones del recurso que contiene la propiedad. Por lo tanto, puede usar **copyIndex()** en la propiedad para especificar los valores.

Existen varios escenarios en donde recomendamos realizar una iteración en una propiedad de un recurso. Por ejemplo, podría especificar varios discos de datos para una máquina virtual. Para ver cómo iterar en una propiedad, vea el artículo sobre cómo [crear varias instancias cuando no funciona el objeto copy](#create-multiple-instances-when-copy-wont-work). 

Para trabajar con recursos secundarios, lea el artículo sobre cómo [crear varias instancias de un recurso secundario](#create-multiple-instances-of-a-child-resource).

## <a name="use-index-value-in-name"></a>Uso del valor de índice en el nombre
Puede usar la operación de copia para crear varias instancias de un recurso que se nombra exclusivamente según el índice incremental. Por ejemplo, puede que desee agregar un número exclusivo al final de cada nombre de recurso que se implementa. Para implementar tres sitios web con los nombres:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Use la siguiente plantilla:

```json
"parameters": { 
  "count": { 
    "type": "int", 
    "defaultValue": 3 
  } 
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())]", 
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

## <a name="offset-index-value"></a>Valor de índice de desplazamiento
En el ejemplo anterior, el valor de índice va de 0 a 2. Para desplazar el valor de índice, puede pasar un valor de la función **copyIndex()**, como **copyIndex(1)**. El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Por lo tanto, con la misma plantilla que en el ejemplo anterior, pero especificando **copyIndex(1)** , implementaría tres sitios web con los nombres:

* examplecopy-1
* examplecopy-2
* examplecopy-3

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

Por supuesto, puede establecer el número de copias en un valor distinto de la longitud de la matriz. Por ejemplo, podría crear una matriz con muchos valores y, a continuación, pasar un valor de parámetro que especifique el número de elementos de la matriz a implementar. En ese caso, establezca el número de copias como se muestra en el primer ejemplo. 

## <a name="depend-on-resources-in-a-loop"></a>Dependencia de los recursos de un bucle
Puede especificar que un recurso se implemente después de otro recurso mediante el elemento **dependsOn**. Para implementar un recurso que dependa de la colección de recursos de un bucle, proporcione el nombre del bucle copy en el elemento **dependsOn**. En el ejemplo siguiente se muestra cómo implementar tres cuentas de almacenamiento antes de implementar la máquina virtual. La definición de la máquina virtual no se muestra. Tenga en cuenta que el elemento copy tiene **name** establecido en **storagecopy** y el elemento **dependsOn** para las máquinas virtuales también se establece en **storagecopy**.

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
No puede usar un bucle copy en un recurso secundario. Para crear varias instancias de un recurso que se define normalmente como anidado dentro de otro recurso, debe crear en su lugar dicho recurso como uno de nivel superior. La relación con el recurso principal se define a través de las propiedades **type** y **name**.

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

Para crear varias instancias de conjuntos de datos, muévalos fuera de la factoría de datos. El conjunto de datos debe estar en el mismo nivel que la factoría de datos, pero seguirá siendo un recurso secundario de la factoría de datos. La relación entre el conjunto de datos y la factoría de datos se conserva a través de los parámetros **type** y **name**. Puesto que la propiedad de type ya no se puede inferir de su posición en la plantilla, debe proporcionar el nombre completo del tipo con el formato: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

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

## <a name="create-multiple-instances-when-copy-wont-work"></a>Creación de varias instancias cuando no funcionará la copia
Solo puede utilizar una **copia** en tipos de recursos, no en propiedades dentro de un tipo de recurso. Este requisito puede crear problemas cuando quiera crear varias instancias de algo que forme parte de un recurso. Un escenario común es crear varios discos de datos para una máquina virtual. No puede usar el valor **copy** con los discos de datos porque **dataDisks** es una propiedad de la máquina virtual, no su propio tipo de recurso. En su lugar, cree una matriz con todos los discos de datos que necesite y pase el número real de discos de datos que vaya a crear. En la definición de la máquina virtual, utilice la función **take** para obtener solo el número de elementos que realmente desee de la matriz.

Un ejemplo completo de este patrón se muestra en la plantilla [Crear una VM con una selección dinámica de discos de datos](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

En el siguiente ejemplo, se muestran las secciones pertinentes de la plantilla de implementación. Se quitó un lote de la plantilla para resaltar las secciones implicadas en la creación dinámica de un número de discos de datos. Tenga en cuenta el parámetro **numDataDisks** que le permite pasar el número de discos que se va a crear. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

Puede usar la función **take** y el elemento **copy** juntos cuando necesite crear varias instancias de un recurso con un número variable de elementos para una propiedad de elemento. Por ejemplo, supongamos que necesita crear varias máquinas virtuales, pero cada una de ellas tiene un número diferente de discos de datos. Para dar a cada disco de datos un nombre que identifique a la máquina virtual asociada, coloque la matriz de discos de datos en una plantilla aparte. Incluya los parámetros del nombre de máquina virtual y el número de discos de datos que se va a devolver. En la sección de resultados, se devuelve el número de elementos especificados.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

En la plantilla principal, incluya los parámetros del número de máquinas virtuales y una matriz del número de discos de datos correspondiente a cada máquina virtual.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

En la sección de recursos, implemente varias instancias de la plantilla que define los discos de datos. 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

En la sección de recursos, implemente varias instancias de la máquina virtual. Para los discos de datos, haga referencia a la implementación anidada que contiene el número correcto de discos datos y los nombres adecuados de los discos de datos.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>Devolución de valores de un bucle
Aunque es conveniente crear varias instancias de un tipo de recurso, puede ser complicado devolver valores de dicho bucle. Una manera de conservar y devolver valores consiste en utilizar el elemento **copy** con una plantilla anidada y realizar un recorrido de ida y vuelta en una matriz que contenga todos los valores que se van a devolver. Por ejemplo, supongamos que quiere crear varias cuentas de almacenamiento y devolver el punto de conexión principal de cada uno de ellos. 

En primer lugar, genere la plantilla anidada que crea la cuenta de almacenamiento. Tenga en cuenta que esta acepta un parámetro de matriz para los URI del blob. Este parámetro se usa para realizar un recorrido de ida y vuelta en todos los valores de las implementaciones anteriores. El resultado de la plantilla es una matriz que concatena el nuevo URI del blob a los URI anteriores.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

Ahora, cree la plantilla principal que tiene una instancia estática de la plantilla anidada y recorra las instancias restantes de la plantilla anidada. Para cada instancia de la implementación de bucle, pase una matriz que sea el resultado de la implementación anterior.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre las secciones de una plantilla, consulte el artículo sobre cómo [crear plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener todas las funciones que puede usar en una plantilla, consulte el artículo sobre las [funciones de la plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


