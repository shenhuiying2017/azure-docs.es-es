---
title: "Establecimiento del orden de implementación de recursos de Azure | Microsoft Docs"
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
ms.date: 01/03/2017
ms.author: tomfitz
ms.openlocfilehash: 3d6a46116ae9d7d940bc10dfa832540f42c0af7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definición del orden de implementación de recursos en plantillas de Azure Resource Manager
Antes de proceder a la implementación de un recurso determinado, es posible que deban existir otros recursos. Por ejemplo, debe existir un servidor SQL para intentar implementar una base de datos SQL. Esta relación se define al marcar un recurso como dependiente del otro. Una dependencia se define con el elemento **dependsOn** o mediante la función **reference**. 

Administrador de recursos evalúa las dependencias entre recursos y los implementa en su orden dependiente. Cuando no hay recursos dependientes entre sí, Administrador de recursos los implementa en paralelo. Solo tiene que definir las dependencias de recursos que se implementan en la misma plantilla. 

## <a name="dependson"></a>dependsOn
Dentro de la plantilla, el elemento dependsOn permite definir un recurso como dependiente de uno o varios recursos. Su valor puede ser una lista de nombres de recursos separados por coma. 

En el ejemplo siguiente se muestra un conjunto de escalado de máquinas virtuales que depende de un equilibrador de carga, una red virtual y un bucle que crea varias cuentas de almacenamiento. Esos otros recursos no aparecen a continuación, pero tendrían que existir en otra ubicación de la plantilla.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

En el ejemplo anterior, se incluye una dependencia de los recursos que se crean a través de un bucle de copia denominado "**storageLoop**". Para ver un ejemplo, consulte [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md)

Al definir las dependencias, puede incluir el espacio de nombres de proveedor de recursos y el tipo de recurso para evitar la ambigüedad. Por ejemplo, para aclarar un equilibrador de carga y la red virtual que puede tener los mismos nombres que otros recursos, utilice el formato siguiente:

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

Si bien puede que se sienta tentado a usar dependsOn para asignar relaciones entre los recursos, es importante comprender por qué lo hace. Por ejemplo, para documentar cómo están interconectados los recursos, dependsOn no es el enfoque correcto. No se pueden consultar los recursos que se definieron en el elemento dependsOn después de realizar la implementación. El uso de dependsOn podría llegar a repercutir en el tiempo de implementación, ya que Resource Manager no implementa dos recursos en paralelo que tengan una dependencia. Para documentar las relaciones entre los recursos, utilice en su lugar la [vinculación de recursos](/rest/api/resources/resourcelinks).

## <a name="child-resources"></a>Recursos secundarios
La propiedad resources permite especificar los recursos secundarios que están relacionados con el recurso que se está definiendo. Los recursos secundarios solo se pueden definir en cinco niveles de profundidad. Es importante tener en cuenta que no se crea una dependencia implícita entre un recurso secundario y el recurso primario. Si necesita que el recurso secundario se implemente después del recurso primario, debe declarar explícitamente esa dependencia con la propiedad dependsOn. 

Cada recurso primario solo acepta determinados tipos de recursos como recursos secundarios. Los tipos de recursos que se aceptan se especifican en el [esquema de plantilla](https://github.com/Azure/azure-resource-manager-schemas) del recurso principal. El nombre del tipo de recurso secundario incluye el nombre del tipo de recurso primario, por ejemplo, **Microsoft.Web/sites/config** y **Microsoft.Web/sites/extensions** son ambos recursos secundarios de **Microsoft.Web/Sites**.

En el ejemplo siguiente se muestran un servidor SQL y una base de datos SQL. Observe que se ha definido una dependencia explícita entre la base de datos SQL y el servidor SQL, a pesar de que la base de datos es un elemento secundario del servidor.

```json
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
```

## <a name="reference-function"></a>función reference
La [función reference](resource-group-template-functions-resource.md#reference) permite que una expresión derive su valor de otros pares de valor y nombre JSON o de recursos en tiempo de ejecución. Las expresiones de referencia declaran implícitamente que un recurso depende de otro. El formato general es:

```json
reference('resourceName').propertyPath
```

En el ejemplo siguiente, un punto de conexión de CDN depende explícitamente del perfil de CDN e implícitamente de una aplicación web.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Puede usar este elemento o el elemento dependsOn para especificar las dependencias, pero no es necesario usar ambos para el mismo recurso dependiente. Siempre que sea posible, use una referencia implícita para evitar agregar una dependencia innecesaria.

Para más información, consulte [función reference](resource-group-template-functions-resource.md#reference).

## <a name="recommendations-for-setting-dependencies"></a>Recomendaciones para configurar las dependencias

A la hora de decidir qué dependencias establecer, use las siguientes directrices:

* Establezca el menor número de dependencias posible.
* Establezca un recurso secundario como dependiente de su recurso principal.
* Use la función **reference** para establecer dependencias implícitas entre recursos que necesiten compartir una propiedad. No agregue una dependencia explícita (**dependsOn**) cuando ya haya definido una dependencia implícita. Este enfoque reduce el riesgo de que se tengan dependencias innecesarias. 
* Establezca una dependencia cuando no se pueda **crear** un recurso sin la funcionalidad de otro recurso. No establezca una dependencia si los recursos solo interactúan después de la implementación.
* Permita dependencias en cascada sin establecerlas explícitamente. Por ejemplo, la máquina virtual depende de una interfaz de red virtual y la interfaz de red virtual depende de una red virtual y las direcciones IP públicas. Por lo tanto, la máquina virtual se implementa después de los tres recursos, pero no establece explícitamente la máquina virtual como dependiente de los tres recursos. Este enfoque aclara el orden de dependencia y facilita el cambio de la plantilla más adelante.
* Si no se puede determinar un valor antes de la implementación, intente implementar el recurso sin una dependencia. Por ejemplo, si un valor de configuración necesita el nombre de otro recurso, quizás no necesite una dependencia. Esta guía no siempre funciona porque algunos recursos comprueban la existencia del otro recurso. Si recibe un error, agregue una dependencia. 

Resource Manager identifica dependencias circulares durante la validación de plantillas. Si recibe un error que indica que existe una dependencia circular, evalúe la plantilla para ver si algunas dependencias no son necesarias y se pueden quitar. Si no es suficiente con quitar dependencias, puede evitar dependencias circulares moviendo algunas operaciones de implementación a recursos secundarios que se implementan después de los recursos que tienen la dependencia circular. Por ejemplo, suponga que va a implementar dos máquinas virtuales pero debe establecer propiedades en cada una que hagan referencia a la otra. Puede implementarlas en el orden siguiente:

1. vm1
2. vm2
3. La extensión en vm1 depende de vm1 y vm2. La extensión establece valores en vm1 que obtiene de vm2.
4. La extensión en vm2 depende de vm1 y vm2. La extensión establece valores en vm2 que obtiene de vm1.

Para información sobre cómo evaluar el orden de implementación y resolver errores de dependencia, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a solucionar los problemas de dependencias durante la implementación, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre la creación de plantillas del Administrador de recursos de Azure, consulte [Creación de plantillas](resource-group-authoring-templates.md). 
* Para obtener una lista de las funciones disponibles en una plantilla, consulte [Funciones de plantilla](resource-group-template-functions.md).

