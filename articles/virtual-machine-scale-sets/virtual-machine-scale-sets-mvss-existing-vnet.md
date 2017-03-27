---
title: "Conjuntos de escalado de máquinas virtuales de Azure: plantilla existente de Virtual Network | Microsoft Docs"
description: Aprenda a crear una plantilla de conjunto de escalado con una red virtual existente.
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/06/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ddb3e1789e49d138e744c2238679236134b69324
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-article"></a>Información acerca de este artículo

En este artículo se muestra cómo modificar la [plantilla de conjunto de escalado mínimo viable](./virtual-machine-scale-sets-mvss-start.md) para realizar la implementación en una red virtual existente en lugar de crear una nueva.

## <a name="modifying-the-minimum-viable-scale-set-to-deploy-into-an-existing-virtual-network"></a>Modificación del conjunto de escalado mínimo viable para implementarlo en una red virtual existente

Nuestra plantilla de conjunto de escalado mínimo viable se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), y nuestra plantilla para implementar el conjunto de escalado en una red virtual se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Vamos a examinar la diferencia usada para crear esta plantilla (`git diff master minimum-viable-scale-set`) paso a paso:

Primero, agregamos un parámetro `subnetId`. Esta cadena se pasa a la configuración del conjunto de escalado, lo que permite que el conjunto de escalado identifique la subred creada anteriormente en la que implementar las máquinas virtuales. Esta cadena debe tener el formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Por ejemplo, para implementar el conjunto de escalado en una red virtual existente con el nombre `myvnet`, la subred `mysubnet`, el grupo de recursos `myrg` y la suscripción `00000000-0000-0000-0000-000000000000`, el valor de subnetId sería: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

A continuación, podemos eliminar el recurso de red virtual de la matriz `resources`, dado que usamos una red virtual existente y no necesitamos implementar una nueva.

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

La red virtual ya existe antes de que se implemente la plantilla, así que no es necesario especificar una cláusula dependsOn entre el conjunto de escalado y la red virtual. Por lo tanto, se eliminan estas líneas:

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

Por último, se pasa el parámetro `subnetId` definido por el usuario (en lugar de usar `resourceId` para obtener el id. de una red virtual en la misma implementación, que es lo que hace la plantilla de conjunto de escalado mínimo viable).

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




## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

