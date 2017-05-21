---
title: "Elemento de interfaz de usuario OptionsGroup de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.OptionsGroup para aplicaciones administradas de Azure
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
ms.openlocfilehash: 0d1784b7e8d4fed13bbcfd828924ed464e8f4119
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.OptionsGroup
Control de selección con una fila de opciones disponibles.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- La etiqueta de `constraints.allowedValues` es el texto para mostrar de un elemento, y su valor es el valor de salida del elemento cuando se selecciona.
- Si se especifica, el valor predeterminado debe ser una etiqueta presente en `constraints.allowedValues`. Si no se especifica, se selecciona el primer elemento de `constraints.allowedValues` de forma predeterminada. El valor predeterminado es **null**.
- `constraints.allowedValues` debe contener al menos un dígito.
- Este elemento no admite la propiedad `constraints.required`; se debe seleccionar un elemento para que la validación sea correcta.

## <a name="sample-output"></a>Salida de ejemplo
```json
"Bar"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).

