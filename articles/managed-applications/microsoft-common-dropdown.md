---
title: "Elemento de interfaz de usuario DropDown de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Common.DropDown para aplicaciones administradas de Azure
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a3cbb7363c04043eb253e91e058753bc271e4e01
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.DropDown
Control de selección con una lista desplegable. Use este elemento al [crear una aplicación administrada de Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
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
- Si se especifica, el valor predeterminado debe ser una etiqueta presente en `constraints.allowedValues`. Si no se especifica, se selecciona el primer elemento de `constraints.allowedValues`. El valor predeterminado es **null**.
- `constraints.allowedValues` debe contener al menos un dígito.
- Este elemento no admite la propiedad `constraints.required`. Para emular este comportamiento, agregue un elemento con una etiqueta y el valor `""` (cadena vacía) a `constraints.allowedValues`.

## <a name="sample-output"></a>Salida de ejemplo
```json
"Bar"
```

## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
