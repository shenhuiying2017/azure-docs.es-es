---
title: 'Funciones de la plantilla de Azure Resource Manager: recursos | Microsoft Docs'
description: Describe las funciones para usar en una plantilla de Azure Resource Manager para recuperar valores sobre recursos.
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
ms.openlocfilehash: 66c71906614e5d0c8e8531271444fc59a5cb779f
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funciones de recursos para las plantillas de Azure Resource Manager

El Administrador de recursos ofrece las siguientes funciones para obtener valores de recursos:

* [listKeys y list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [suscripción](#subscription)

Para obtener valores de parámetro, variables o la implementación actual, consulte [Funciones con valores de implementación](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys y list{Value}
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Devuelve los valores para cualquier tipo de recurso que admite la operación list. El uso más común es `listKeys`. 

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sí |string |Identificador único para el recurso. |
| apiVersion |Sí |cadena |Versión de API de estado en tiempo de ejecución de un recurso. Por lo general, en el formato, **aaaa-mm-dd**. |

### <a name="remarks"></a>Comentarios

Cualquier operación que comienza por **list** se puede usar como función en la plantilla. Entre las operaciones disponibles se incluyen no solo listKeys, sino también operaciones como `list`, `listAdminKeys` y `listStatus`. Para determinar qué tipos de recursos tienen una operación de lista, dispone de las siguientes opciones:

* Vea las [operaciones de API de REST](/rest/api/) para un proveedor de recursos y busque operaciones List. Por ejemplo, las cuentas de almacenamiento tienen una [operación listKeys](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Utilice el cmdlet de PowerShell [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). En el ejemplo siguiente se obtienen todas las operaciones List para cuentas de almacenamiento:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Use el siguiente comando de la CLI de Azure y la utilidad JSON [jq](http://stedolan.github.io/jq/download/) para filtrar solo las operaciones de lista:

  ```azurecli
  azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
  ```

Especifique el recurso utilizando la [función resourceId](#resourceid) o el formato `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo se devuelven las claves principal y secundaria de una cuenta de almacenamiento en la sección de salidas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountId": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "storageKeysOutput": {
            "value": "[listKeys(parameters('storageAccountId'), '2016-01-01')]",
            "type" : "object"
        }
    }
}
``` 

### <a name="return-value"></a>Valor devuelto

El objeto devuelto desde listKeys tiene el formato siguiente:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Otras funciones List tienen diferentes formatos de retorno. Para ver el formato de una función, inclúyalo en la sección de resultados tal y como se muestra en la plantilla de ejemplo. 

<a id="providers" />

## <a name="providers"></a>providers
`providers(providerNamespace, [resourceType])`

Devuelve información acerca de un proveedor de recursos y sus tipos de recursos admitidos. Si no proporciona un tipo de recurso, la función devuelve todos los tipos admitidos para el proveedor de recursos.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| providerNamespace |Sí |string |Espacio de nombres del proveedor |
| resourceType |No |cadena |El tipo de recurso en el espacio de nombres especificado. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo utilizar la función de proveedor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

Se devuelve cada tipo admitido en el formato siguiente: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

No se garantiza el orden de matriz de los valores devueltos.

<a id="reference" />

## <a name="reference"></a>reference
`reference(resourceName or resourceIdentifier, [apiVersion])`

Devuelve un objeto que representa el estado de tiempo de ejecución de un recurso.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| resourceName o resourceIdentifier |Sí |string |Nombre o identificador único de un recurso. |
| apiVersion |No |string |Versión de la API del recurso especificado. Incluya este parámetro cuando el recurso no esté aprovisionado en la misma plantilla. Por lo general, en el formato, **aaaa-mm-dd**. |

### <a name="remarks"></a>Comentarios

La función reference deriva su valor desde un estado de tiempo de ejecución y, por tanto, no se puede utilizar en la sección de variables. Se puede utilizar en la sección de salidas de una plantilla. 

Mediante el uso de la función de referencia, se declara implícitamente que un recurso depende de otro recurso si el recurso al que se hace referencia se aprovisiona en la misma plantilla. No tiene que usar también la propiedad dependsOn. La función no se evalúa hasta que el recurso al que se hace referencia complete la implementación.

Para ver los nombres y los valores de propiedad de un tipo de recurso, cree una plantilla que devuelva el objeto en la sección de salidas. Si tiene un recurso existente de ese tipo, la plantilla devuelve el objeto sin necesidad de implementar los nuevos recursos. 

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se hace referencia a una cuenta de almacenamiento que no se implementa en esta plantilla, pero que existe dentro del mismo grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

También implementar y hacer referencia al recurso en la misma plantilla.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      }
    }
}
``` 

Normalmente, se utiliza la función de referencia para devolver un valor determinado de un objeto, como el identificador URI del punto de conexión de blob o el nombre de dominio completo.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

### <a name="return-value"></a>Valor devuelto

Cada tipo de recurso devuelve propiedades diferentes para la función de referencia. La función no devuelve un solo formato predefinido. Para ver las propiedades de un tipo de recurso, devuelva el objeto en la sección de resultados tal como se muestra en el ejemplo.

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

Devuelve un objeto que representa el grupo de recursos actual. 

### <a name="examples"></a>Ejemplos

La plantilla siguiente devuelve las propiedades del grupo de recursos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Un uso común de la función resourceGroup es crear recursos en la misma ubicación que el grupo de recursos. En el ejemplo siguiente se utiliza la ubicación del grupo de recursos para asignar la ubicación de un sitio web.

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="return-value"></a>Valor devuelto

El objeto devuelto está en el formato siguiente:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Devuelve el identificador único de un recurso. Utilice esta función cuando el nombre del recurso sea ambiguo o no esté aprovisionado dentro de la misma plantilla. 

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |Cadena (en formato de GUID) |El valor predeterminado es la suscripción actual. Especifique este valor cuando necesite recuperar un recurso en otra suscripción. |
| resourceGroupName |No |cadena |El valor predeterminado es el grupo de recursos actual. Especifique este valor cuando necesite recuperar un recurso en otro grupo de recursos. |
| resourceType |Sí |string |Tipo de recurso, incluido el espacio de nombres del proveedor de recursos. |
| resourceName1 |Sí |cadena |Nombre del recurso. |
| resourceName2 |No |string |Siguiente segmento de nombre de recurso si el recurso está anidado. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se devuelve el identificador de recurso de la cuenta de almacenamiento en el grupo de recursos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceIdOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        }
    }
}
```

En el ejemplo siguiente se muestra cómo recuperar los identificadores de recursos para un sitio web en otro grupo de recursos y una base de datos en un grupo de recursos distinto:

```json
[resourceId('otherResourceGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

A menudo, necesitará utilizar esta función cuando se usa una cuenta de almacenamiento o red virtual en un grupo de recursos alternativo. La cuenta de almacenamiento o la red virtual puede utilizarse en varios grupos de recursos; por lo tanto, no es deseable su eliminación cuando se elimina un único grupo de recursos. En el ejemplo siguiente se muestra cómo un recurso de un grupo de recursos externos se puede utilizar fácilmente:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="return-value"></a>Valor devuelto

El identificador se devuelve con el formato siguiente:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

<a id="subscription" />

## <a name="subscription"></a>suscripción
`subscription()`

Devuelve detalles sobre la suscripción para la implementación actual. 

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra la función de suscripción a la que se llama en la sección de salidas. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

### <a name="return-value"></a>Valor devuelto

La función devuelve el siguiente formato:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).


