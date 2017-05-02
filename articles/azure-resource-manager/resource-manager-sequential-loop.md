---
title: Bucle secuencial en plantillas de Azure | Microsoft Docs
description: "Describe cómo extender la funcionalidad de las plantillas de Azure Resource Manager para implementar el bucle secuencial cuando se implementan varias instancias de un tipo de recurso."
services: guidance, azure-resource-manager
documentationcenter: na
author: petertaylor9999
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 04/18/2017
ms.author: petertay
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 44d59488600e05d6ae80a169b5d682e8fa94ccc5
ms.lasthandoff: 04/18/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---sequential-looping"></a>Patrones para la expansión de la funcionalidad de plantillas de Azure Resource Manager: Bucle secuencial

Las plantillas de Azure Resource Manager admiten la implementación de un grupo de recursos similares a través de un bucle de copia. Un bucle de copia en un objeto de recurso puede usarse para recorrer en iteración una matriz de cadenas que se usan para generar nombres únicos para los recursos. Un bucle de copia en el objeto de recurso también puede usarse para recorrer en iteración una matriz de variables que describen el recurso.

Estos patrones funcionan bien, pero solo funcionan bien cuando no hay dependencias entre los miembros del grupo. En un bucle de iteración, Resource Manager intenta implementar recursos en paralelo. Si un recurso de primer depende de un segundo, la implementación puede producir un error si el Resource Manager implementa el segundo recurso antes del primer recurso.

El problema real es que Resource Manager no admite actualmente `dependsOn` dentro de un bucle de iteración. Sin embargo, esta funcionalidad puede implementarse con la funcionalidad de Resource Manager existente y algunos nombres de recursos creativos. 

## <a name="sequential-looping-pattern"></a>Patrón de bucle secuencial

El patrón es el siguiente: se establece un nombre para el primer recurso con una concatenación de un prefijo de nombre y `0`, o lo que el primer índice del bucle. Un segundo recurso incluye un bucle de copia, y en el bucle de copia el siguiente nombre de recurso es una concatenación del prefijo del nombre y el resultado de la función `copyIndex(1)`, que suma 1 a la función `copyIndex()` actual. El segundo recurso también incluye un elemento `dependsOn` que hace referencia la concatenación del prefijo de nombre y el resultado de la función `copyIndex()`. Este enfoque crear una relación `dependsOn` del recurso siguiente hasta el recurso anterior. Resource Manager espera para implementar el recurso siguiente hasta que se implementa el recurso anterior.

La plantilla siguiente muestra este patrón. El tipo de recurso Microsoft.Resources/deployments es simplemente una plantilla anidada que no implementa realmente nada.

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
  "variables": {
    "count": "[sub(parameters('numberToDeploy'), 1)]"
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "loop-0",
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
            "collection": {
              "type": "string",
              "value": "loop-0 "
            }
          }
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
        "count": "[variables('count')]"
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
            "collection": {
              "type": "string",
              "value": "[concat(reference(concat('loop-',copyIndex())).outputs.collection.value,'loop-',copyIndex(1), ' ')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference(concat('loop-',variables('count'))).outputs.collection.value]"
    }
  }
}

```
En esta plantilla, el primer objeto de recurso se denomina `loop-0`. A continuación, en el segundo objeto de recurso, el siguiente nombre de recurso es una concatenación de la palabra `loop-` y el resultado de la función `copyIndex(1)`: `loop-1`. El elemento `dependsOn` hace referencia al recurso anterior, ya que es una concatenación de la palabra `loop-` y el resultado de la función `copyIndex()`: `loop-0`. El patrón del segundo objeto de recurso se repite hasta que se alcanza `count`, finalizando con un recurso denominado `loop-4` que `dependsOn` `loop-3`. Tenga en cuenta que `count` es una variable que resta `1` del parámetro `numberToDeploy` para mantener el número basado en cero correcto.

## <a name="try-the-template"></a>Prueba de la plantilla

Si desea experimentar con esta plantilla, siga estos pasos:

1.    Vaya a Azure Portal, seleccione el icono "+" y busque el tipo de recurso "implementación de plantilla". Cuando lo encuentre en los resultados de búsqueda, selecciónelo.
2.    Cuando llegue a la página "implementación de plantilla", seleccione el botón "crear". Vaya a la hoja "implementación personalizada" y observe que la plantilla no tiene ningún recurso.
3.    Seleccione el icono "editar".
4.    Elimine la plantilla vacía del panel derecho.
5.    Copie y pegue la plantilla de ejemplo anterior en el panel derecho.
6.    Seleccione el botón "guardar".
7.    Se le devuelve al panel "implementación personalizada", pero esta vez hay algunos cuadros desplegables. Seleccione la suscripción, cree o use un grupo de recursos existente y seleccione una ubicación. Revise los términos y condiciones, y haga clic en el botón "Acepto".
8.    Haga clic en el botón "comprar".

Para comprobar que los recursos se están implementando secuencialmente, seleccione "grupos de recursos" y luego seleccione el grupo de recursos que seleccionó anteriormente. Seleccione el botón "implementaciones" en la hoja de grupo de recursos, y vea los recursos implementados en orden secuencial con la marca de tiempo correspondiente.

![Implementación de plantilla de bucle secuencial en Azure Resource Manager](./media/resource-manager-sequential-loop/deployments.png)

## <a name="next-steps"></a>Pasos siguientes

Use este patrón en las plantillas mediante la adición de los recursos a la plantilla anidada. También puede crearlas directamente en el elemento de plantilla del recurso `Microsoft.Resources/deployments` o establecer un vínculo a ellas mediante el elemento `templateLink`. El tipo de recurso del ejemplo es una plantilla anidada, pero se puede implementar cualquier tipo de recurso. La única excepción es que no se puede hacer referencia a los recursos secundarios desde dentro de un bucle de iteración.

* Si quiere ver una introducción a la creación de varias instancias de un recurso, vea [Implementación de varias instancias de recursos en plantillas de Azure Resource Manager](resource-group-create-multiple.md).
* Este patrón también se implementa en el [proyecto de bloques de creación de plantillas](https://github.com/mspnp/template-building-blocks) y las [arquitecturas de referencia de Azure](/azure/architecture/reference-architectures/).
