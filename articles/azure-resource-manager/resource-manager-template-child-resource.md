---
title: "Definición de recursos secundarios en la plantilla de Azure | Microsoft Docs"
description: "Muestra cómo se establece el tipo y el nombre de un recurso secundario en una plantilla de Azure Resource Manager"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Establecimiento del nombre y el tipo de recurso secundario en la plantilla de Resource Manager
Al crear una plantilla, con frecuencia es necesario incluir un recurso secundario relacionado con uno primario. Por ejemplo, la plantilla puede incluir un servidor SQL Server y una base de datos. SQL Server es el recurso primario y la base de datos, el secundario. 

El formato del tipo de recurso secundario es: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

El formato del nombre de recurso secundario es: `{parent-resource-name}/{child-resource-name}`

Sin embargo, el tipo y el nombre se especifican en una plantilla de manera diferente en función de si se anida dentro del recurso primario o por sí misma en el nivel superior. En este tema se muestra cómo aplicar ambos enfoques.

Al construir una referencia completa a un recurso, el orden para combinar los segmentos a partir del tipo y el nombre no es simplemente una concatenación de los dos.  En su lugar, después del espacio de nombres, use una secuencia de pares *tipo/nombre* de menos a más específico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por ejemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` es correcto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` no es correcto

## <a name="nested-child-resource"></a>Recurso secundario anidado
La manera más fácil de definir un recurso secundario es anidarlo en el primario. En el ejemplo siguiente se muestra una base de datos de SQL anidada en un servidor SQL Server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Para el recurso secundario, el tipo se establece en `databases`, pero el tipo de recurso completo es `Microsoft.Sql/servers/databases`. No proporciona `Microsoft.Sql/servers/`, porque lo adopta del tipo de recurso primario. El nombre del recurso secundario se establece en `exampledatabase`, pero el nombre completo incluye el primario. No proporciona `exampleserver`, porque lo adopta del recurso primario.

## <a name="top-level-child-resource"></a>Recurso secundario de nivel superior
Puede definir el recurso secundario en el nivel superior. Este enfoque puede usarse si el recurso primario no está implementado en la misma plantilla o si desea usar `copy` para crear varios recursos secundarios. En este enfoque, es necesario proporcionar el tipo de recurso completo, así como incluir el nombre del recurso primario en el del secundario.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

La base de datos es un recurso secundario del servidor, aunque se definan en el mismo nivel en la plantilla.

## <a name="next-steps"></a>Pasos siguientes
* Para más recomendaciones sobre la creación de plantillas, consulte [Procedimientos recomendados para crear plantillas de Azure Resource Manager](resource-manager-template-best-practices.md).
* Para obtener un ejemplo de creación de varios recursos secundarios, consulte [Implementación de varias instancias de recursos en plantillas de Azure Resource Manager](resource-group-create-multiple.md).
