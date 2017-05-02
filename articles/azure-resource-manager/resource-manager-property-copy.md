---
title: Varias copias de la propiedad de recursos de Azure | Microsoft Docs
description: "En la plantilla de Azure Resource Manager, cree varias instancias de una propiedad en un tipo de recurso. Debe utilizar la función take en lugar del bucle copy para este escenario."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>Creación de varias instancias de la propiedad en un tipo de recurso
Solo puede utilizar una [copia](resource-group-create-multiple.md) en tipos de recursos, no en propiedades dentro de un tipo de recurso. Este requisito puede crear problemas cuando quiera crear varias instancias de algo que forme parte de un recurso. Un escenario común es crear varios discos de datos para una máquina virtual. No puede usar el valor copy con los discos de datos porque dataDisks es una propiedad de la máquina virtual, no su propio tipo de recurso. En su lugar, cree una matriz con todos los discos de datos que necesite y pase el número real de discos de datos que vaya a crear. En la definición de la máquina virtual, utilice la función `take` para obtener solo el número de elementos que realmente desee de la matriz.

Un ejemplo completo de este patrón se muestra en la plantilla [Crear una VM con una selección dinámica de discos de datos](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Las secciones relevantes de la plantilla de implementación se muestran en este artículo. Se quitaron las partes de la plantilla para resaltar las secciones implicadas en la creación dinámica de un número de discos de datos. 

## <a name="define-template-with-variable-for-the-property"></a>Definición de la plantilla con la variable para la propiedad

Para crear una máquina virtual con varios discos de datos, agregue una variable de matriz que defina más discos de datos de los que necesita. En el parámetro `numDataDisks`, pase el número real de discos de datos que crear. Use `take` para especificar el número exacto de elementos de la matriz que asignar a la máquina virtual.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
      ... /* not all elements shown */
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
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>Creación de varias máquinas virtuales con varios discos de datos

Puede usar la función **take** y el elemento **copy** juntos cuando necesite crear varias instancias de un recurso con un número variable de elementos para una propiedad de elemento. Por ejemplo, supongamos que necesita crear varias máquinas virtuales, pero cada una de ellas tiene un número diferente de discos de datos. 

Para dar a cada disco de datos un nombre que identifique a la máquina virtual asociada, coloque la matriz de discos de datos en una plantilla aparte. Incluya los parámetros del nombre de máquina virtual y el número de discos de datos que se va a devolver. En la sección de resultados, se devuelve el número de elementos especificados.

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

## <a name="next-steps"></a>Pasos siguientes
* Si quiere ver una introducción a la creación de varias instancias de un recurso, vea [Implementación de varias instancias de recursos en plantillas de Azure Resource Manager](resource-group-create-multiple.md).
* Para crear recursos iterados en orden, vea [Bucle secuencia para plantillas de Azure Resource Manager](resource-manager-sequential-loop.md).
* Para obtener información sobre cómo implementar la plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).


