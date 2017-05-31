---
title: Referencia a una imagen personalizada en una plantilla de conjunto de escalado de Azure | Microsoft Docs
description: "Obtenga información sobre cómo agregar una imagen personalizada a una plantilla de un conjunto de escalado de máquinas virtuales de Microsoft Azure existente"
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
ms.date: 5/10/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: cf52fc9e95267c4bc5c0106aadf626685ddd5c24
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017

---

# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Incorporación de una imagen personalizada a una plantilla de un conjunto de escalado de Azure

Este artículo le enseña cómo modificar la [plantilla de conjunto de escalado mínimo viable](./virtual-machine-scale-sets-mvss-start.md) para implementar a partir de una imagen personalizada.

## <a name="change-the-template-definition"></a>Cambio de la definición de la plantilla

Nuestra plantilla de conjunto de escalado mínimo viable se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), y nuestra plantilla para implementar el conjunto de escalado a partir de una imagen personalizada se puede ver [aquí](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Vamos a examinar la diferencia usada para crear esta plantilla (`git diff minimum-viable-scale-set custom-image`) paso a paso:

### <a name="creating-a-managed-disk-image"></a>Creación de una imagen de disco administrado

Si ya tiene una imagen de disco administrado personalizada (un recurso del tipo `Microsoft.Compute/images`), puede omitir esta sección.

En primer lugar, se agrega un parámetro `sourceImageVhdUri`, que es el identificador URI del blob generalizado de Azure Storage que contiene la imagen personalizada desde la que se va a realizar la implementación.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

A continuación, se agrega un recurso de tipo `Microsoft.Compute/images`, que es la imagen de disco administrado basada en el blob generalizado ubicado en el URI `sourceImageVhdUri`. Esta imagen debe estar en la misma región que el conjunto de escalado que la utilice. En las propiedades de la imagen, se especifica el tipo de sistema operativo, la ubicación del blob (desde el parámetro `sourceImageVhdUri`) y el tipo de cuenta de almacenamiento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

En el recurso del conjunto de escalado, se agrega una cláusula `dependsOn` que hace referencia a la imagen personalizada para garantizar que la imagen se crea antes de que el conjunto de escalado se intente implementar desde ella:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Cambio en las propiedades del conjunto de escalado para usar la imagen de disco administrado

En el `imageReference` del conjunto de escalado `storageProfile`, en lugar de especificar el publicador, oferta, sku y versión de una imagen de la plataforma, especificamos el `id` del recurso `Microsoft.Compute/images`:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

En este ejemplo, se usa la función `resourceId` para obtener el identificador de recurso de la imagen que se creó en la misma plantilla. Si ha creado la imagen de disco administrado con antelación, deberá proporcionar el identificador de esa imagen en su lugar. Este identificador debe tener el formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

