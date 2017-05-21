---
title: "Elemento de interfaz de usuario TextBox de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.TextBox para aplicaciones administradas de Azure
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
ms.openlocfilehash: aa161d2f6611cd7f1ef8fac8eebfde12eedd9fcf
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---

# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.TextBox
Control que puede usarse para editar texto sin formato.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Halp!",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Si `constraints.required` está establecido en **true**, el cuadro de texto debe contener un valor para que la validación sea correcta. El valor predeterminado es **false**.
- `constraints.regex` es un patrón de expresión regular de JavaScript. Si se especifica, el valor del cuadro de texto debe coincidir con el patrón para que la validación sea correcta. El valor predeterminado es **null**.
- `constraints.validationMessage` es una cadena que se muestra cuando el valor del cuadro de texto produce un error de validación. Si no se especifica, se utilizan los mensajes de validación integrados del cuadro de texto. El valor predeterminado es **null**.
- Es posible especificar un valor para `constraints.regex` cuando `constraints.required` está establecido en **false**. En este escenario, no se requiere un valor para que la validación del cuadro de texto sea correcta. Si se especifica uno, debe coincidir con el patrón de expresión regular.

## <a name="sample-output"></a>Salida de ejemplo

```json
"foobar"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).

