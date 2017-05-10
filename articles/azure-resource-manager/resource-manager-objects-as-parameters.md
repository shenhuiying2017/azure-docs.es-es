---
title: "Plantillas de Azure Resource Manager: objetos como parámetro | Microsoft Docs"
description: "Se describe cómo ampliar la funcionalidad de las plantillas de Azure Resource Manager para utilizar objetos como parámetros."
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/01/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0ab00cc3455d4bff7bfe1dfb62bafa550d65dea8
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---objects-as-parameters"></a>Modelos para ampliar la funcionalidad de las plantillas de Azure Resource Manager: objetos como parámetros.

Las plantillas de Azure Resource Manager admiten parámetros para especificar valores a fin de personalizar una implementación de recursos. Aunque esta característica es útil y le permite crear implementaciones complejas, una única plantilla está limitada a 255 parámetros. Si usa un parámetro para cada propiedad en un recurso y tiene una implementación de gran tamaño, puede quedarse sin parámetros.

## <a name="create-object-as-parameter"></a>Creación de objeto como parámetro

Una manera de solucionar este problema es utilizar un objeto como parámetro. El modelo consiste en especificar un parámetro como un objeto de la plantilla y, a continuación, proporcionar el objeto como un valor o una matriz de valores. Se hace referencia a sus subpropiedades mediante la función `parameter()` y el operador punto. Por ejemplo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "VNetSettings":{"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('VNetSettings').name]",
      "location":"[resourceGroup().location]",
      "properties": {
          "addressSpace":{
              "addressPrefixes": [
                  "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
              ]
          },
          "subnets":[
              {
                  "name":"[parameters('VNetSettings').subnets[0].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
                  }
              },
              {
                  "name":"[parameters('VNetSettings').subnets[1].name]",
                  "properties": {
                      "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
                  }
              }
          ]
        }
    }
  ],          
  "outputs": {}
}

```

El archivo de parámetros correspondiente tiene el siguiente aspecto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "VNetSettings":{
          "value":{
              "name":"VNet1",
              "addressPrefixes": [
                  { 
                      "name": "firstPrefix",
                      "addressPrefix": "10.0.0.0/22"
                  }
              ],
              "subnets":[
                  {
                      "name": "firstSubnet",
                      "addressPrefix": "10.0.0.0/24"
                  },
                  {
                      "name":"secondSubnet",
                      "addressPrefix":"10.0.1.0/24"
                  }
              ]
          }
      }
  }
}
```

En este ejemplo, todos los valores especificados para la red virtual proceden de un único parámetro, `VNetSettings`. Este modelo es útil para la administración de valores de propiedad porque conserva todos los valores de un recurso determinado en un único objeto. Y aunque en este ejemplo se utiliza un objeto como parámetro, también puede usar una matriz de objetos como parámetro. Se hace referencia a los objetos mediante un índice en la matriz.

## <a name="use-object-instead-of-multiple-arrays"></a>Uso de objeto en lugar de varias matrices

Es posible que haya utilizado un modelo similar para crear varias instancias de un recurso mediante la creación de varias matrices de valores de propiedad y la iteración a través de cada matriz para seleccionar el valor. Este modelo funciona bien cuando se crean varios recursos del mismo tipo, pero puede ser problemático cuando se usa para crear recursos secundarios. 

Son dos los motivos que ocasionan este problema. En primer lugar, Resource Manager intenta implementar recursos secundarios en paralelo, pero su implementación genera un error cuando dos recursos secundarios actualizan el elemento primario al mismo tiempo. 

En segundo lugar, cada matriz del valor de propiedad se itera en paralelo y, si la matriz no tiene la misma forma, se produce un error. Por ejemplo, considere las siguientes matrices de propiedades:

```json
"variables": {
    "firstProperty": [
        {
            "name": "A",
            "type": "typeA"
        },
        {
            "name": "B",
            "type": "typeB"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ],
    "secondProperty": [
        "one","two"
    ]
}
```

El modelo típico para asignar estos valores a propiedades dentro de un bucle de copia es obtener acceso a la propiedad mediante la función `variables()` y utilizar `copyIndex()` como índice en la matriz. Por ejemplo:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    ...
    "copy": {
        "name": "copyLoop1",
        "count": "[length(variables('firstProperty'))]"
    },
    ...
    "properties": {
        "name": { "value": "[variables('firstProperty')[copyIndex()].name]" },
        "type": { "value": "[variables('firstProperty')[copyIndex()].type]" },
        "number": { "value": "[variables('secondProperty')[copyIndex()]]" }
    }
}
```
Tenga en cuenta que `count` en el bucle de copia se basa en el número de propiedades en la matriz `firstProperty`. Sin embargo, no hay el mismo número de propiedades en la matriz `secondProperty`. La validación genera un error para esta plantilla porque la longitud de la matriz `secondProperty` no tiene la misma longitud.

Sin embargo, si incluye todas las propiedades en un único objeto, es mucho más fácil ver cuando falta un valor. Por ejemplo:

```json
"variables": {
    "propertyObject": [
        {
            "name": "A",
            "type": "typeA",
            "number": "one"
        },
        {
            "name": "B",
            "type": "typeB",
            "number": "two"
        },
        {
            "name": "C",
            "type": "typeC"
        }
    ]
}
```

## <a name="use-with-sequential-copy"></a>Uso con copia secuencial

Este modelo es incluso más útil cuando se combina con el [patrón de copia secuencial](resource-manager-sequential-loop.md), especialmente para implementar recursos secundarios. La plantilla de ejemplo siguiente implementa un grupo de seguridad de red con dos reglas de seguridad. El primer recurso denominado `NSG1` implementa el grupo de seguridad de red. El segundo grupo de recursos denominado `loop-0` realiza dos funciones: en primer lugar, `dependsOn` el grupo de seguridad de red de manera que su implementación no comience hasta que `NSG1` se haya completado, y es la primera iteración del bucle secuencial. El tercer recurso es una plantilla anidada que implementa las reglas de seguridad utilizando un objeto para sus valores de parámetro, como en el ejemplo anterior.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "networkSecurityGroupsSettings": {"type":"object"}
  },
  "variables": {},
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location":"[resourceGroup().location]",
      "properties": {
          "securityRules":[]
      }
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "loop-0",
        "dependsOn": [
            "NSG1"
        ],
        "properties": {
            "mode":"Incremental",
            "parameters":{},
            "template": {
                "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            }
        }       
    },
    {
        "apiVersion": "2015-01-01",
        "type": "Microsoft.Resources/deployments",
        "name": "[concat('loop-', copyIndex(1))]",
        "dependsOn": [
          "[concat('loop-', copyIndex())]"
        ],
        "copy": {
          "name": "iterator",
          "count": "[length(parameters('networkSecurityGroupsSettings').securityRules)]"
        },
        "properties": {
          "mode": "Incremental",
          "template": {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
           "parameters": {},
            "variables": {},
            "resources": [
                {
                    "name": "[concat('NSG1/' , parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].name)]",
                    "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                    "apiVersion": "2016-09-01",
                    "location":"[resourceGroup().location]",
                    "properties":{
                        "description": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].description]",
                        "priority":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].priority]",
                        "protocol":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].protocol]",
                        "sourcePortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourcePortRange]",
                        "destinationPortRange": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationPortRange]",
                        "sourceAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].sourceAddressPrefix]",
                        "destinationAddressPrefix": "[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].destinationAddressPrefix]",
                        "access":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].access]",
                        "direction":"[parameters('networkSecurityGroupsSettings').securityRules[copyIndex()].direction]"
                        }
                  }
            ],
            "outputs": {}
          }
        }
    }
  ],          
  "outputs": {}
}

```

El archivo de parámetros correspondiente tiene el siguiente aspecto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{ 
      "networkSecurityGroupsSettings": {
      "value": {
          "securityRules": [
            {
              "name": "RDPAllow",
              "description": "allow RDP connections",
              "direction": "Inbound",
              "priority": 100,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.0.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "access": "Allow",
              "protocol": "Tcp"
            },
            {
              "name": "HTTPAllow",
              "description": "allow HTTP connections",
              "direction": "Inbound",
              "priority": 200,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "10.0.1.0/24",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      }
    }
  }
```

## <a name="try-the-template"></a>Prueba de la plantilla

Si desea experimentar con estas plantillas, siga estos pasos:

1.    Vaya a Azure Portal, seleccione el icono "+" y busque el tipo de recurso "implementación de plantilla". Cuando lo encuentre en los resultados de búsqueda, selecciónelo.
2.    Cuando llegue a la página "Implementación de plantilla", seleccione el botón **crear**. Este botón abre la hoja de "Implementación personalizada".
3.    Seleccione el botón **Editar plantilla**.
4.    Elimine la plantilla vacía del panel derecho.
5.    Copie y pegue la plantilla de ejemplo en el panel derecho.
6.    Seleccione el botón **Guardar**.
7.    Cuando vuelva al panel "Implementación personalizada", seleccione el botón **Editar parámetros**.
8.  En la hoja "Editar parámetros", elimine la plantilla existente.
9.  Copie y pegue la plantilla de parámetro de ejemplo anterior.
10. Seleccione el botón **Guardar**, que vuelve a abrir la hoja "Implementación personalizada".
11. En la hoja "Implementación personalizada", seleccione la suscripción, cree un grupo de recursos nuevo o use uno existente y seleccione una ubicación. Revise los términos y condiciones y haga clic en la casilla "Acepto".
12.    Seleccione el botón **Comprar**.

## <a name="next-steps"></a>Pasos siguientes

Si necesita más de los 255 parámetros máximos permitidos por implementación, use este modelo para especificar menos parámetros en la plantilla. También puede usar este patrón para administrar más fácilmente las propiedades de los recursos y luego implementarlos sin conflictos mediante el patrón de bucle secuencial.

* Para obtener una introducción a la función `parameter()` y el uso de matrices, vea [Funciones de la plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Este patrón también se implementa en el [proyecto de bloques de creación de plantillas](https://github.com/mspnp/template-building-blocks) y las [arquitecturas de referencia de Azure](/azure/architecture/reference-architectures/).
