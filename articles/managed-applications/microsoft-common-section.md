---
title: Elemento de interfaz de usuario Section de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.Section para Azure Portal.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea2e3d404ac3ec9b7f909257451991dbb55f53
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
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
Para obtener acceso a los valores de salida de los elementos en `elements`, use las funciones [basics()](create-uidefinition-functions.md#basics) o [steps()](create-uidefinition-functions.md#steps) y la notación de puntos:

```json
basics('section1').element1
```

Los elementos del tipo `Microsoft.Common.Section` no tienen valores de salida por sí mismos.

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
