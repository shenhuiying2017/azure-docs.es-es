---
title: Elemento de interfaz de usuario CredentialsCombo de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Compute.CredentialsCombo para Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 914e354265754a05476e96411d35e6cb04183213
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261061"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Elemento de interfaz de usuario Microsoft.Compute.CredentialsCombo
Grupo de controles con validación integrada para las contraseñas de Windows y Linux, y las claves públicas de SSH.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>Esquema
Si `osPlatform` es **Windows**, se utiliza el siguiente esquema:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Si `osPlatform` es **Linux**, se utiliza el siguiente esquema:
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- `osPlatform` debe especificarse y puede ser **Windows** o **Linux**.
- Si `constraints.required` está establecido en **true**, los cuadros de texto de clave pública SSH o contraseña deben contener valores para que la validación sea correcta. El valor predeterminado es **true**.
- Si `options.hideConfirmation` está establecido en **true**, se oculta el segundo cuadro de texto para confirmar la contraseña del usuario. El valor predeterminado es **false**.
- Si `options.hidePassword` está establecido en **true**, se oculta la opción para utilizar la autenticación de contraseña. Se puede utilizar solo cuando `osPlatform` es **Linux**. El valor predeterminado es **false**.
- Se pueden implementar restricciones adicionales en las contraseñas permitidas con la propiedad `customPasswordRegex`. La cadena de `customValidationMessage` se muestra cuando se produce un error de validación personalizada en una contraseña. El valor predeterminado para ambas propiedades es **null**.

## <a name="sample-output"></a>Salida de ejemplo
Si `osPlatform` es **Windows**, o el usuario proporcionó una contraseña en lugar de una clave pública SSH, se espera la siguiente salida:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Si el usuario proporcionó una clave pública SSH, se espera la siguiente salida:
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
