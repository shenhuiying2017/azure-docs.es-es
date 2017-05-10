---
title: "Plantillas de Azure Resource Manager: actualización de un recurso | Microsoft Docs"
description: "Se describe cómo ampliar la funcionalidad de las plantillas de Azure Resource Manager para actualizar un recurso."
services: azure-resource-manager, guidance
documentationcenter: na
author: petertay
manager: christb
editor: 
ms.service: guidance
ms.topic: article
ms.date: 05/02/2017
ms.author: mspnp
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 7807b3f6135ede80cd7e7e6486364482cde72e29
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---

# <a name="patterns-for-extending-the-functionality-of-azure-resource-manager-templates---updating-a-resource"></a>Modelos para la expansión de la funcionalidad de plantillas de Azure Resource Manager: actualización de un recurso

Hay algunos escenarios en los que es necesario actualizar un recurso durante una implementación. Este caso se da cuando no se pueden especificar todas las propiedades de un recurso hasta que se creen otros recursos dependientes. Por ejemplo, si crea un grupo de back-end para un equilibrador de carga, puede actualizar los adaptadores de red en sus máquinas virtuales (VM) para incluirlos en el grupo de back-end. Resource Manager admite la actualización de recursos durante la implementación, pero debe diseñar su plantilla correctamente para evitar errores y garantizar que la implementación se trate como una actualización.

## <a name="understand-the-pattern"></a>Descripción del modelo

En primer lugar, debe hacer referencia al recurso una vez en la plantilla para crearlo, pero entonces debe hacer referencia al recurso con el mismo nombre para actualizarlo más adelante. Sin embargo, si dos recursos tienen el mismo nombre en una plantilla, Resource Manager genera una excepción. Para evitar este error, especifique el recurso actualizado en una segunda plantilla que haya vinculado o incluido como una subplantilla mediante el tipo de recurso `Microsoft.Resources/deployments`.

En segundo lugar, en la segunda plantilla, debe especificar el nombre de la propiedad existente para cambiar o proporcionar un nombre nuevo para una propiedad que se va a agregar. A continuación, también debe especificar las propiedades originales y sus valores originales. Si no se proporcionan las propiedades y los valores originales, Resource Manager da por supuesto que desea crear un nuevo recurso y elimina el original. Reemplaza al recurso original con un nuevo recurso que incluye solo las nuevas propiedades que haya especificado.

Por último, debe hacer que el recurso sea dependiente de todos los recursos relacionados que desea implementar. Esta dependencia garantiza que los recursos se crean en el orden correcto. El orden es:

1. Creación de recurso
2. Creación de recursos dependientes
3. Actualización del recurso (del paso 1) con valores de los recursos dependientes (paso 2)

## <a name="example-template"></a>Plantilla de ejemplo

La plantilla de ejemplo siguiente muestra este modelo. Implementa una red virtual (VNet) denominada `firstVNet` que tiene una subred denominada `firstSubnet`. A continuación, implementa un adaptador de red denominado `nic1` y lo asocia a la subred. Luego, un recurso de implementación denominado `updateVNet` incluye una plantilla anidada que hace referencia al nombre del recurso `firstVNet`. 

Observe la propiedad `addressSpace` y la propiedad `subnets` en este recurso. Tenga en cuenta que el valor `addressSpace` se establece en el mismo valor de propiedad en el objeto de implementación de recurso `firstVNet`. En la matriz `subnets`, el valor de `firstSubnet` se establece de forma similar. Dado que se han especificado todas las propiedades de `firstVNet` originales, Resource Manager actualiza el recurso en Azure. En este caso, la actualización es la adición de una nueva subred denominada `secondSubnet`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
      {
      "apiVersion": "2016-03-30",
      "name": "firstVNet",
      "location":"[resourceGroup().location]",
      "type": "Microsoft.Network/virtualNetworks",
      "properties": {
          "addressSpace":{"addressPrefixes": [
              "10.0.0.0/22"
          ]},
          "subnets":[              
              {
                  "name":"firstSubnet",
                  "properties":{
                    "addressPrefix":"10.0.0.0/24"
                  }
              }
            ]
      }
    },
    {
        "apiVersion": "2015-06-15",
        "type":"Microsoft.Network/networkInterfaces",
        "name":"nic1",
        "location":"[resourceGroup().location]",
        "dependsOn": [
            "firstVNet"
        ],
        "properties": {
            "ipConfigurations":[
                {
                    "name":"ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod":"Dynamic",
                        "subnet": {
                            "id": "[concat(resourceId('Microsoft.Network/virtualNetworks','firstVNet'),'/subnets/firstSubnet')]"
                        }
                    }
                }
            ]
        }
    },
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "updateVNet",
      "dependsOn": [
          "nic1"
      ],
      "properties": {
        "mode": "Incremental",
        "parameters": {},
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
              {
                  "apiVersion": "2016-03-30",
                  "name": "firstVNet",
                  "location":"[resourceGroup().location]",
                  "type": "Microsoft.Network/virtualNetworks",
                  "properties": {
                      "addressSpace": "[reference('firstVNet').addressSpace]",
                      "subnets":[
                          {
                              "name":"[reference('firstVNet').subnets[0].name]",
                              "properties":{
                                  "addressPrefix":"[reference('firstVNet').subnets[0].properties.addressPrefix]"
                                  }
                          },
                          {
                              "name":"secondSubnet",
                              "properties":{
                                  "addressPrefix":"10.0.1.0/24"
                                  }
                          }
                     ]
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

## <a name="try-the-template"></a>Prueba de la plantilla

Si desea experimentar con esta plantilla, siga estos pasos:

1.    Vaya a Azure Portal, seleccione el icono "+" y busque el tipo de recurso "implementación de plantilla". Cuando lo encuentre en los resultados de búsqueda, selecciónelo.
2.    Cuando llegue a la página "Implementación de plantilla", seleccione el botón **Crear**, que abre la hoja "Implementación personalizada".
3.    Seleccione el icono **Editar**.
4.    Elimine la plantilla vacía.
5.    Copie y pegue la plantilla de ejemplo anterior en el panel derecho.
6.    Seleccione el botón **Guardar**.
7.    Vuelve al panel "Implementación personalizada", pero esta vez hay algunos cuadros desplegables. Seleccione la suscripción, cree o use un grupo de recursos existente y seleccione una ubicación. Revise los términos y condiciones y seleccione el botón **Acepto**.
8.    Seleccione el botón **Comprar**.

Cuando haya finalizado la implementación, abra el grupo de recursos especificado en el portal. Verá una red virtual denominada `firstVNet` y un adaptador de red denominado `nic1`. Haga clic en `firstVNet` y luego en `subnets`. Verá el `firstSubnet` que se creó originalmente, y verá el `secondSubnet` que se agregó en el recurso `updateVNet`. 

![Subred original y subred actualizada](./media/resource-manager-update/firstVNet-subnets.png)

A continuación, vuelva al grupo de recursos y haga clic en `nic1`; a continuación, haga clic en `IP configurations`. En la sección `IP configurations`, `Subnet` se configura en `firstSubnet (10.0.0.0/24)`. 

![Parámetros de configuración de IP del adaptador de red 1](./media/resource-manager-update/nic1-ipconfigurations.png)

El `firstVNet` original se ha actualizado, en lugar de haberse creado de nuevo. Si `firstVNet` se hubiera creado de nuevo, `nic1` no se asociaría con `firstVNet`.

## <a name="next-steps"></a>Pasos siguientes

Puede usar este modelo en sus plantillas para volver a especificar las propiedades originales del recurso que desea actualizar. Especifique el recurso de actualización en una plantilla vinculada o anidada mediante el tipo de recurso `Microsoft.Resources/deployments`.

* Para obtener una introducción a la función `reference()`, vea [Funciones de la plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Este patrón también se implementa en el [proyecto de bloques de creación de plantillas](https://github.com/mspnp/template-building-blocks) y las [arquitecturas de referencia de Azure](/azure/architecture/reference-architectures/).
