---
title: Dependencias en plantillas de Resource Manager | Microsoft Docs
description: "Describe cómo establecer un recurso como dependiente de otro recurso durante la implementación para garantizar el orden de implementación correcto de los recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5f810a46db4deed9c31db4f7c072c48b0817ebc4
ms.openlocfilehash: 35ce1f12a3de0a41d400cceebe6aefbadbe51528


---
# <a name="defining-dependencies-in-azure-resource-manager-templates"></a>Definición de dependencias en plantillas del Administrador de recursos de Azure
Antes de proceder a la implementación de un recurso determinado, es posible que deban existir otros recursos. Por ejemplo, debe existir un servidor SQL para intentar implementar una base de datos SQL. Esta relación se define al marcar un recurso como dependiente del otro. Normalmente, una dependencia se define con el elemento **dependsOn**, pero también se puede hacer con la función **reference**. 

Administrador de recursos evalúa las dependencias entre recursos y los implementa en su orden dependiente. Cuando no hay recursos dependientes entre sí, Administrador de recursos los implementa en paralelo. Solo tiene que definir las dependencias de recursos que se implementan en la misma plantilla. 

## <a name="dependson"></a>dependsOn
Dentro de la plantilla, el elemento dependsOn permite definir un recurso como dependiente de uno o varios recursos. Su valor puede ser una lista de nombres de recursos separados por coma. 

En el ejemplo siguiente se muestra un conjunto de escalado de máquinas virtuales que depende de un equilibrador de carga, una red virtual y un bucle que crea varias cuentas de almacenamiento. Esos otros recursos no aparecen a continuación, pero tendrían que existir en otra ubicación de la plantilla.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[variables('loadBalancerName')]",
        "[variables('virtualNetworkName')]"
      ],
      ...
    }

En el ejemplo anterior, se incluye una dependencia de los recursos que se crean a través de un bucle de copia denominado "**storageLoop**". Para ver un ejemplo, consulte [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md)

Al definir las dependencias, puede incluir el espacio de nombres de proveedor de recursos y el tipo de recurso para evitar la ambigüedad. Por ejemplo, para aclarar un equilibrador de carga y la red virtual que puede tener los mismos nombres que otros recursos, utilice el formato siguiente:

    "dependsOn": [
      "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
      "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
    ] 

Si bien puede que se sienta tentado a usar dependsOn para asignar relaciones entre los recursos, es importante comprender por qué lo hace ya que puede afectar al rendimiento de la implementación. Por ejemplo, para documentar cómo están interconectados los recursos, dependsOn no es el enfoque correcto. No se pueden consultar los recursos que se definieron en el elemento dependsOn después de realizar la implementación. El uso de dependsOn podría llegar a repercutir en el tiempo de implementación, ya que Resource Manager no implementa dos recursos en paralelo que tengan una dependencia. Para documentar las relaciones entre los recursos, utilice en su lugar la [vinculación de recursos](resource-group-link-resources.md).

## <a name="child-resources"></a>Recursos secundarios
La propiedad resources permite especificar los recursos secundarios que están relacionados con el recurso que se está definiendo. Los recursos secundarios solo se pueden definir en cinco niveles de profundidad. Es importante tener en cuenta que no se crea una dependencia implícita entre un recurso secundario y el recurso primario. Si necesita que el recurso secundario se implemente después del recurso primario, debe declarar explícitamente esa dependencia con la propiedad dependsOn. 

Cada recurso primario solo acepta determinados tipos de recursos como recursos secundarios. Los tipos de recursos que se aceptan se especifican en el [esquema de plantilla](https://github.com/Azure/azure-resource-manager-schemas) del recurso principal. El nombre del tipo de recurso secundario incluye el nombre del tipo de recurso primario, por ejemplo, **Microsoft.Web/sites/config** y **Microsoft.Web/sites/extensions** son ambos recursos secundarios de **Microsoft.Web/Sites**.

En el ejemplo siguiente se muestran un servidor SQL y una base de datos SQL. Observe que se ha definido una dependencia explícita entre la base de datos SQL y el servidor SQL, a pesar de que la base de datos es un elemento secundario del servidor.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## <a name="reference-function"></a>función reference
La [función reference](resource-group-template-functions.md#reference) permite que una expresión derive su valor de otros pares de valor y nombre JSON o de recursos en tiempo de ejecución. Las expresiones de referencia declaran implícitamente que un recurso depende de otro. 

    reference('resourceName').propertyPath

Puede usar este elemento o el elemento dependsOn para especificar las dependencias, pero no es necesario usar ambos para el mismo recurso dependiente. Siempre que sea posible, utilice una referencia implícita para evitar agregar una dependencia innecesaria de forma accidental.

Para más información, consulte [función reference](resource-group-template-functions.md#reference).

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre la creación de plantillas del Administrador de recursos de Azure, consulte [Creación de plantillas](resource-group-authoring-templates.md). 
* Para obtener una lista de las funciones disponibles en una plantilla, consulte [Funciones de plantilla](resource-group-template-functions.md).




<!--HONumber=Nov16_HO4-->


