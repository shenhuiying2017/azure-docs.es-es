---
title: Referencia a una red virtual existente en una plantilla de conjunto de escalado de Azure | Microsoft Docs
description: "Obtenga información sobre cómo agregar una red virtual a una plantilla de un conjunto de escalado de máquinas virtuales de Microsoft Azure existente"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: eb35975de5864e129f97b614a61487456dd972ef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Incorporación de una referencia a una red virtual existente en una plantilla de conjunto de escalado de Azure

En este artículo se muestra cómo modificar la [plantilla de conjunto de escalado mínimo viable](./virtual-machine-scale-sets-mvss-start.md) para realizar la implementación en una red virtual existente en lugar de crear una nueva.

## <a name="change-the-template-definition"></a>Cambio de la definición de la plantilla

La plantilla de conjunto de escalado mínimo viable se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), y la plantilla para implementar el conjunto de escalado en una red virtual se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Vamos a examinar la diferencia usada para crear esta plantilla (`git diff minimum-viable-scale-set existing-vnet`) paso a paso:

Primero, agregue un parámetro `subnetId`. Esta cadena se pasa a la configuración del conjunto de escalado, lo que permite que el conjunto de escalado identifique la subred creada anteriormente en la que implementar las máquinas virtuales. Esta cadena debe tener el formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Por ejemplo, para implementar el conjunto de escalado en una red virtual existente con el nombre `myvnet`, la subred `mysubnet`, el grupo de recursos `myrg` y la suscripción `00000000-0000-0000-0000-000000000000`, el valor de subnetId sería: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

A continuación, elimine el recurso de red virtual de la matriz `resources`, puesto que usa una red virtual existente y no necesita implementar una nueva.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

La red virtual ya existe antes de que se implemente la plantilla, así que no es necesario especificar una cláusula dependsOn entre el conjunto de escalado y la red virtual. Elimine las líneas siguientes:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por último, pase el parámetro `subnetId` definido por el usuario (en lugar de usar `resourceId` para obtener el identificador de una red virtual en la misma implementación, que es lo que hace la plantilla de conjunto de escalado mínimo viable).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
