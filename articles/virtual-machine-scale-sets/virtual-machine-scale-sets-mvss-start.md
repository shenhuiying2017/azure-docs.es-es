---
title: "Conjuntos de escalado de máquinas virtuales de Azure: conjunto de escalado de viabilidad mínimo | Microsoft Docs"
description: "Información sobre cómo crear una plantilla de conjunto de escalado de viabilidad mínimo"
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
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 65a400b63688c50fd97c34a5aa5dadc2f5dd286c
ms.lasthandoff: 03/06/2017


---

# <a name="about-this-tutorial"></a>Acerca de este tutorial

Las [plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) constituyen una excelente manera de implementar grupos de recursos relacionados. En esta serie de tutoriales se explica cómo crear una plantilla de conjunto de escalado de viabilidad mínimo y cómo modificarla para adaptarla a distintos escenarios. Todos los ejemplos proceden de este [repositorio de GitHub](https://github.com/gatneil/mvss). Cada diferencia que se muestra en este tutorial es el resultado de realizar una operación `git diff` entre ramas de este repositorio. Estas plantillas y diferencias se han diseñado como ejemplos sencillos y no completos. Para obtener ejemplos más completos de plantillas de conjuntos de escalado, consulte el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) y busque las carpetas que contienen la cadena `vmss`.

## <a name="a-minimum-viable-scale-set"></a>Un conjunto de escalado de viabilidad mínimo

Puede encontrar nuestra plantilla de conjuntos de escalado de viabilidad mínimo [aquí](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json). Si ya está familiarizado con las plantillas, puede avanzar sin problema a la sección Pasos siguientes para ver cómo modificar esta plantilla para otros escenarios. Sin embargo, si no lo está, podría encontrar útil esta descripción paso a paso. Para empezar, vamos a examinar la diferencia para crear esta plantilla (`git diff master minimum-viable-scale-set`) paso a paso:

En primer lugar, definimos los parámetros `$schema` y `contentVersion` de la plantilla. `$schema` define la versión del idioma de la plantilla y se utiliza para resaltar la sintaxis de Visual Studio y características de validación similares. `contentVersion` no la usa Azure realmente. En su lugar, sirve para ayudarlo a realizar un seguimiento de cuál es la versión de la plantilla.

```diff
+{
+  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
+  "contentVersion": "1.0.0.0",
```

Después, definimos dos parámetros, `adminUsername` y `adminPassword`. Los parámetros son valores que especifica el usuario durante la implementación. `adminUsername` es simplemente un elemento `string`, pero como `adminPassword` es un secreto, le proporcionamos el tipo `securestring`. Más adelante, veremos que estos parámetros se pasan a la configuración de conjunto de escalado.

```diff
+  "parameters": {
+    "adminUsername": {
+      "type": "string"
+    },
+    "adminPassword": {
+      "type": "securestring"
+    }
+  },
```

Las plantillas de Resource Manager también permiten definir variables que se usarán más adelante en la plantilla. En este ejemplo, no usamos variables, por lo que hemos dejado el objeto JSON vacío.

```diff
+  "variables": {},
```

Después, tenemos los recursos de la plantilla, donde se definen lo que realmente queremos implementar. A diferencia de la `parameters` y `variables` (que son objetos JSON), `resources` es una lista JSON de objetos JSON.

```diff
+  "resources": [
```

Todos los recursos requieren parámetros `type`, `name`, `apiVersion` y `location`. Nuestro primer recurso es de tipo `Microsft.Network/virtualNetwork` con el nombre `myVnet` y versión de API `2016-03-30`. Para averiguar la versión de API más reciente de un tipo de recurso, consulte la [documentación de la API de REST de Azure](https://docs.microsoft.com/rest/api/).

```diff
+    {
+      "type": "Microsoft.Network/virtualNetworks",
+      "name": "myVnet",
+      "apiVersion": "2016-12-01",
```

Para especificar la ubicación de la red virtual, usaremos una [función de plantilla de Resource Manager](../azure-resource-manager/resource-group-template-functions.md), que debe estar entre comillas y corchetes de la forma siguiente: `"[<template-function>]"`. En este caso, usamos la función resourceGroup, que no toma ningún argumento y devuelve un objeto JSON con metadatos del grupo de recursos en el que se está realizando esta implementación. El grupo de recursos lo establece el usuario durante la implementación. Luego, se indiza en este objeto JSON con `.location` para obtener la ubicación del objeto JSON.

```diff
+      "location": "[resourceGroup().location]",
```


Cada recurso de Resource Manager tiene su propia sección `properties` para configuraciones específicas del recurso. En este caso, vamos a especificar que la red virtual tenga una subred con el intervalo de direcciones IP privadas `10.0.0.0/16`. Un conjunto de escalado siempre está dentro de una subred, y no puede abarcar subredes.

```diff
+      "properties": {
+        "addressSpace": {
+          "addressPrefixes": [
+            "10.0.0.0/16"
+          ]
+        },
+        "subnets": [
+          {
+            "name": "mySubnet",
+            "properties": {
+              "addressPrefix": "10.0.0.0/16"
+            }
+          }
+        ]
+      }
+    },
```

Además de las propiedades obligatorias `type`, `name`, `apiVersion` y `location`, cada recurso podría tener opcionalmente una lista `dependsOn` de cadenas que especifica qué otros recursos de esta implementación deben finalizar antes de implementar este recurso. En este caso, solo hay un elemento en esta lista: la red virtual anterior. Especificamos esta dependencia porque el conjunto de escalado necesita que la red exista antes de crear las máquinas virtuales. De esta manera, el conjunto de escalado puede proporcionar estas direcciones IP privadas de las máquinas virtuales del intervalo de direcciones IP especificado anteriormente en las propiedades de red. El formato de cada cadena de la lista de dependsOn es `<type>/<name>` (los mismos parámetros `type` y `name` que hemos usado anteriormente en la definición de recursos de red virtual).

```diff
+    {
+      "type": "Microsoft.Compute/virtualMachineScaleSets",
+      "name": "myScaleSet",
+      "apiVersion": "2016-04-30-preview",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Network/virtualNetworks/myVnet"
+      ],
```

El conjunto de escalado necesita saber qué tamaño de máquina virtual debe crear (el nombre de SKU) y cuántas crear (la capacidad de SKU). Para ver qué tamaños de máquinas virtuales están disponibles, consulte la [documentación de tamaños de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```diff
+      "sku": {
+        "name": "Standard_A1",
+        "capacity": 2
+      },
```

El conjunto de escalado también debe saber cómo controlar las actualizaciones en el conjunto de escalado. En estos momentos hay dos opciones, `Manual` y `Automatic`. Para obtener más información sobre las diferencias entre ambas, consulte la documentación sobre [cómo actualizar un conjunto de escalado](./virtual-machine-scale-sets-upgrade-scale-set.md).

```diff
+      "properties": {
+        "upgradePolicy": {
+          "mode": "Manual"
+        },
```

El conjunto de escalado también debe saber qué sistema operativo colocar en las máquinas virtuales. Después, creamos las máquinas virtuales con una imagen Ubuntu 16.04-LTS totalmente revisada.

```diff
+        "virtualMachineProfile": {
+          "storageProfile": {
+            "imageReference": {
+              "publisher": "Canonical",
+              "offer": "UbuntuServer",
+              "sku": "16.04-LTS",
+              "version": "latest"
+            }
+          },
```

Puesto que el conjunto de escalado va a implementar varias máquinas virtuales, en lugar de especificar cada nombre de máquina virtual, especificamos un parámetro `computerNamePrefix`. El conjunto de escalado anexa un índice a este prefijo en cada máquina virtual, por lo que son los nombres de las máquinas virtuales tienen el formato `<computerNamePrefix>_<auto-generated-index>`. En este fragmento de código, también vemos que vamos a usar los parámetros de antes de establecer el nombre de usuario y la contraseña de administrador en todas las máquinas virtuales del conjunto de escalado. Para ello, usaremos la función de plantilla `parameters` función de plantilla que toma una cadena que especifica el parámetro al que se va a hacer referencia y genera el valor de ese parámetro.

```diff
+          "osProfile": {
+            "computerNamePrefix": "vm",
+            "adminUsername": "[parameters('adminUsername')]",
+            "adminPassword": "[parameters('adminPassword')]"
+          },
```

Finalmente, necesitamos especificar la configuración de red de las máquinas virtuales del conjunto de escalado. En este caso, solo tenemos que especificar el identificador de la subred que hemos creado anteriormente para que el conjunto de escalado sepa que puede colocar las interfaces de red en esta subred. Podemos obtener el identificador de la red virtual que contiene la subred con la función de plantilla `resourceId`. Esta función toma el tipo y el nombre de un recurso, y devuelve el identificador completo de ese recurso (este identificador tiene el formato: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`). Sin embargo, no basta con el identificador de la red virtual. Debemos especificar la subred específica en la que deben estar las máquinas virtuales del conjunto de escalado, por lo que concatenaremos `/subnets/mySubnet` al identificador de la red virtual. El resultado es el identificador completo de la subred. Esta concatenación la realizamos con la función `concat`, que toma una serie de cadenas y devuelve su concatenación.

```diff
+          "networkProfile": {
+            "networkInterfaceConfigurations": [
+              {
+                "name": "myNic",
+                "properties": {
+                  "primary": "true",
+                  "ipConfigurations": [
+                    {
+                      "name": "myIpConfig",
+                      "properties": {
+                        "subnet": {
+                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                        }
+                      }
+                    }
+                  ]
+                }
+              }
+            ]
+          }
+        }
+      }
+    }
+  ]
+}

```

## <a name="next-steps"></a>Pasos siguientes

Puede implementar la plantilla anterior siguiendo los pasos de [esta documentación](../azure-resource-manager/resource-group-template-deploy.md).

Para obtener más información sobre los conjuntos de escalado, puede consultar la [página de información sobre los conjuntos de escalado](./virtual-machine-scale-sets-overview.md).
