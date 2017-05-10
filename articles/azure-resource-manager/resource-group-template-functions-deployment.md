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
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: ce888415b6a5f82fb3d49834b055f8afe97442a8
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


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

### <a name="examples"></a>Ejemplos

El ejemplo siguiente devuelve el objeto de implementación:

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

En el ejemplo siguiente se muestra cómo usar deployment() para establecer un vínculo con otra plantilla basada en el identificador URI de la plantilla principal.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

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



<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Devuelve un valor de parámetro. El nombre del parámetro especificado debe definirse en la sección de parámetros de la plantilla.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| parameterName |Sí |cadena |El nombre del parámetro que se va a devolver. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra un uso simplificado de la función de los parámetros.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="return-value"></a>Valor devuelto

Tipo de parámetro

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

Devuelve el valor de variable. El nombre de la variable especificada debe definirse en la sección de variables de la plantilla.

### <a name="parameters"></a>parameters

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| variableName |Sí |string |El nombre de la variable que se va a devolver. |

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se utiliza un valor de variable.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

### <a name="return-value"></a>Valor devuelto

Tipo de variable

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy.md).


