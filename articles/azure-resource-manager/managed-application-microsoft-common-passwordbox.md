---
title: "Elemento de interfaz de usuario PasswordBox de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.PasswordBox para aplicaciones administradas de Azure
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
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: 7b2277f9e1446b42d85180e2a8baa2bcc0df147f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.PasswordBox
Control que puede usarse para proporcionar una contraseña y confirmarla. Use este elemento al [crear una aplicación administrada de Azure](managed-application-publishing.md).

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Este elemento no admite la propiedad `defaultValue`.
- Para más detalles sobre la implementación de `constraints`, consulte [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md).
- Si `options.hideConfirmation` está establecido en **true**, se oculta el segundo cuadro de texto para confirmar la contraseña del usuario. El valor predeterminado es **false**.

## <a name="sample-output"></a>Salida de ejemplo
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](managed-application-overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).
