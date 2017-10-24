---
title: Errores de recurso primario de Azure | Microsoft Docs
description: "Describe cómo resolver errores cuando se trabaja con un recurso primario."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e59147c4ac18f730f27b9d4aa9c008f219881065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-parent-resources"></a>Resolución de errores para recursos primarios

Este artículo describe los errores que pueden producirse al implementar un recurso que depende de un recurso primario.

## <a name="symptom"></a>Síntoma

Al implementar un recurso que es un elemento secundario de otro recurso, puede recibir el error siguiente:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Causa

Cuando un recurso es un elemento secundario de otro recurso, el primario tiene que existir antes de crear el secundario. El nombre del recurso secundario incluye el nombre primario. Por ejemplo, se podría definir una base de datos SQL como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Sin embargo, si no especifica una dependencia en el servidor, puede iniciar la implementación de la base de datos antes de que implemente el servidor.

## <a name="solution"></a>Solución

Para resolver este error, incluya una dependencia.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Para más información consulte [Definición del orden de implementación de recursos en plantillas de Azure Resource Manager](resource-group-define-dependencies.md).