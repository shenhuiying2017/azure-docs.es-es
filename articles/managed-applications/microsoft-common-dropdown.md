---
title: Elemento de la interfaz de usuario de DropDown de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.DropDown para Azure Portal.
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
ms.openlocfilehash: 6c92304ae623807ffba05dcdbb576e1cef63b10c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.DropDown
Control de selección con una lista desplegable.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- La etiqueta de `constraints.allowedValues` es el texto para mostrar de un elemento, y su valor es el valor de salida del elemento cuando se selecciona.
- Si se especifica, el valor predeterminado debe ser una etiqueta presente en `constraints.allowedValues`. Si no se especifica, se selecciona el primer elemento de `constraints.allowedValues`. El valor predeterminado es **null**.
- `constraints.allowedValues` debe contener al menos un dígito.
- Este elemento no admite la propiedad `constraints.required`. Para emular este comportamiento, agregue un elemento con una etiqueta y el valor `""` (cadena vacía) a `constraints.allowedValues`.

## <a name="sample-output"></a>Salida de ejemplo
```json
"Bar"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
