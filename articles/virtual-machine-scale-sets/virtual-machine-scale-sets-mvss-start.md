---
title: "Información sobre las plantillas de conjunto de escalado de máquinas virtuales | Microsoft Docs"
description: "Obtenga información sobre cómo crear una plantilla de conjunto de escalado de viabilidad mínimo para conjuntos de escalado de máquinas virtuales"
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
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 5cd495d1332c71d7eae775f933b73d98826f10e4
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Más información sobre las plantillas de conjuntos de escalado de máquinas virtuales
Las [plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) constituyen una excelente manera de implementar grupos de recursos relacionados. En esta serie de tutoriales se explica cómo crear una plantilla de conjunto de escalado de viabilidad mínimo y cómo modificarla para adaptarla a distintos escenarios. Todos los ejemplos proceden de este [repositorio de GitHub](https://github.com/gatneil/mvss). 

Esta plantilla está diseñada para ser sencilla. Para ejemplos más completos de plantillas de conjuntos de escalado, consulte el [repositorio de GitHub de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) y busque las carpetas que contienen la cadena `vmss`.

Si ya está familiarizado con la creación de plantillas, puede avanzar a la sección "Pasos siguientes" para ver cómo modificar esta plantilla.

## <a name="review-the-template"></a>Revisión de la plantilla

Use GitHub para revisar la plantilla de conjunto de escalado mínimo viable, [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

En este tutorial se examina la diferencia (`git diff master minimum-viable-scale-set`) para crear parte por parte la plantilla de conjunto de escalado mínimo viable.

## <a name="define-schema-and-contentversion"></a>Definición de $schema y contentVersion
En primer lugar, defina `$schema` y `contentVersion` en la plantilla. El elemento `$schema` define la versión del lenguaje de plantilla y se usa para resaltar la sintaxis de Visual Studio y características de validación similares. Azure no usa el elemento `contentVersion`. En su lugar, le ayuda a hacer seguimiento de la versión de la plantilla.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definición de parámetros
Después, defina dos parámetros, `adminUsername` y `adminPassword`. Los parámetros son valores que especifica durante la implementación. El parámetro `adminUsername` es simplemente un tipo `string`, pero como `adminPassword` es un secreto, proporciónele el tipo `securestring`. Más adelante,estos parámetros se pasan a la configuración de conjunto de escalado.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definición de variables
Las plantillas de Resource Manager también permiten definir variables que se usarán más adelante en la plantilla. En el ejemplo no se usa ninguna variable, por lo que el objeto JSON está vacío.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definición de recursos
A continuación está la sección de recursos de la plantilla. Aquí puede definir lo que realmente desea implementar. A diferencia de `parameters` y `variables` (que son objetos), `resources` es una lista JSON de objetos JSON.

```json
   "resources": [
```

Todos los recursos requieren las propiedades `type`, `name`, `apiVersion` y `location`. El primer recurso de este ejemplo tiene el tipo `Microsft.Network/virtualNetwork`, el nombre `myVnet` y el valor de apiVersion `2016-03-30`. (Para encontrar la versión más reciente de la API para un tipo de recurso, consulte la [documentación de la API de REST de Azure](https://docs.microsoft.com/rest/api/)).

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Especificación de ubicación
Para especificar la ubicación de la red virtual, use una [función de plantilla de Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Esta función debe estar entre comillas y corchetes de la siguiente manera: `"[<template-function>]"`. En este caso, use la función `resourceGroup`, que no toma ningún argumento y devuelve un objeto JSON con metadatos del grupo de recursos en el que se está realizando esta implementación. El grupo de recursos lo establece el usuario durante la implementación. Este valor se indexa luego en el objeto JSON con `.location` para obtener la ubicación de dicho objeto.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Especificación de las propiedades de la red virtual
Cada recurso de Resource Manager tiene su propia sección `properties` para configuraciones específicas del recurso. En este caso, se especifica que la red virtual tenga una subred con el intervalo de direcciones IP privadas `10.0.0.0/16`. Un conjunto de escalado siempre está dentro de una subred, y no puede abarcar subredes.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Incorporación de la lista dependsOn
Además de las propiedades `type`, `name`, `apiVersion` y `location` requeridas, cada recurso tiene una lista de cadenas `dependsOn` opcional. Esta lista especifica qué otros recursos de esta implementación deben finalizar antes de implementar este recurso.

En este caso, solo hay un elemento en la lista: la máquina virtual del ejemplo anterior. Se especifica esta dependencia porque el conjunto de escalado necesita que la red exista antes de crear las máquinas virtuales. De esta manera, el conjunto de escalado puede proporcionar estas direcciones IP privadas de las máquinas virtuales del intervalo de direcciones IP especificado anteriormente en las propiedades de red. El formato de cada cadena de la lista dependsOn es `<type>/<name>`. Use el mismo valor de `type` y `name` que se usó anteriormente en la definición de recurso de red virtual.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Especificación de propiedades de conjunto de escalado
Los conjuntos de escalado tienen muchas propiedades para personalizar las máquinas virtuales en el conjunto de escalado. Para una lista completa de estas propiedades, consulte la [documentación de API de REST del conjunto de escalado](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). En ese tutorial solo se establecen algunas propiedades de uso frecuente.
### <a name="supply-vm-size-and-capacity"></a>Suministro de capacidad y tamaño de máquina virtual
El conjunto de escalado necesita saber qué tamaño de máquina virtual debe crear ("nombre de SKU") y cuántas crear ("capacidad de SKU"). Para ver qué tamaños de máquinas virtuales están disponibles, consulte la [documentación de tamaños de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Elección del tipo de actualizaciones
El conjunto de escalado también debe saber cómo controlar las actualizaciones en el conjunto de escalado. En estos momentos hay dos opciones, `Manual` y `Automatic`. Para obtener más información sobre las diferencias entre ambas, consulte la documentación sobre [cómo actualizar un conjunto de escalado](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Elección del sistema operativo de la máquina virtual
El conjunto de escalado debe saber qué sistema operativo colocar en las máquinas virtuales. Después, cree las máquinas virtuales con una imagen Ubuntu 16.04-LTS totalmente revisada.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Especificación de computerNamePrefix
El conjunto de escalado implementa varias máquinas virtuales. En lugar de especificar el nombre de cada máquina virtual, especifique `computerNamePrefix`. El conjunto de escalado anexa un índice a este prefijo en cada máquina virtual, por lo que son los nombres de las máquinas virtuales tienen el formato `<computerNamePrefix>_<auto-generated-index>`.

En el fragmento de código siguiente, use los parámetros de antes de establecer el nombre de usuario y la contraseña de administrador en todas las máquinas virtuales del conjunto de escalado. Este proceso usa la función de plantilla `parameters`. Esta función toma una cadena que especifica a qué parámetro se hace referencia y genera el valor de ese parámetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Especificación de la red de máquina virtual
Finalmente, especifique la configuración de red de las máquinas virtuales del conjunto de escalado. En este caso, solo es necesario especificar el identificador de la subred que se creó anteriormente. Esto le indica al conjunto de escalado que coloque las interfaces de red en esta subred.

Puede obtener el identificador de la red virtual que contiene la subred con la función de plantilla `resourceId`. Esta función toma el tipo y el nombre de un recurso, y devuelve el identificador completo de ese recurso. El identificador tiene el formato siguiente: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Sin embargo, no basta con el identificador de la red virtual. Proporcione la subred específica donde deben estar las máquinas virtuales del conjunto de escalado. Para ello, concatene `/subnets/mySubnet` al identificador de la red virtual. El resultado es el identificador completo de la subred. Esta concatenación la realizamos con la función `concat`, que toma una serie de cadenas y devuelve su concatenación.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>pasos siguientes

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
