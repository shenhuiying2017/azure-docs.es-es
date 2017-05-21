---
title: "Elemento de interfaz de usuario Section de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.Section para aplicaciones administradas de Azure
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 7111b02432a774e056b5a5e13f93ae7f11fb6848
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonsection-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.Section
Control que agrupa uno o varios elementos en un encabezado.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- `elements` debe contener al menos un elemento, y puede contener todos los tipos de elementos excepto `Microsoft.Common.Section`.
- Este elemento no admite la propiedad `toolTip`.

## <a name="sample-output"></a>Salida de ejemplo
Para obtener acceso a los valores de salida de los elementos en `elements`, use las funciones [basics()](managed-application-createuidefinition-functions.md#basics) o [steps()](managed-application-createuidefinition-functions.md#steps) y la notación de puntos:

```json
basics('section1').element1
```

Los elementos del tipo `Microsoft.Common.Section` no tienen valores de salida por sí mismos.

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).

