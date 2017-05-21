---
title: "Elemento de interfaz de usuario UserNameTextBox de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.UserNameTextBox para aplicaciones administradas de Azure
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
ms.openlocfilehash: 1379c1c48d883fe74d49be16e081219a52f611da
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento de interfaz de usuario Microsoft.Compute.UserNameTextBox
Control de cuadro de texto con validación integrada para nombres de usuario de Windows y Linux.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Si `constraints.required` está establecido en **true**, el cuadro de texto debe contener un valor para que la validación sea correcta. El valor predeterminado es **true**.
- `osPlatform` debe especificarse y puede ser **Windows** o **Linux**.
- `constraints.regex` es un patrón de expresión regular de JavaScript. Si se especifica, el valor del cuadro de texto debe coincidir con el patrón para que la validación sea correcta. El valor predeterminado es **null**.
- `constraints.validationMessage` es una cadena que se muestra cuando el valor del cuadro de texto produce un error en la validación especificada por `constraints.regex`. Si no se especifica, se utilizan los mensajes de validación integrados del cuadro de texto. El valor predeterminado es **null**.
- Este elemento tiene validación integrada que se basa en el valor especificado para `osPlatform`. La validación integrada puede usarse junto con una expresión regular personalizada.
Si se especifica un valor para `constraints.regex`, se activan las validaciones integradas y personalizadas.

## <a name="sample-output"></a>Salida de ejemplo
```json
"tabrezm"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).

