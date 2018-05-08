---
title: Elemento de interfaz de usuario TextBox de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.TextBox para Azure Portal.
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
ms.openlocfilehash: 4238d241ec5daacd06485b118a8ccf9b68a9d6d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
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
  "defaultValue": "my value",
  "toolTip": "Use only allowed characters",
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
"my value"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
