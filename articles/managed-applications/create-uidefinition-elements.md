---
title: Elemento CreateUIDefinition de Azure | Microsoft Docs
description: Describe los elementos que se usarán al crear definiciones de interfaz de usuario para Azure Portal.
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
ms.openlocfilehash: 0a69f46294fc370b1eb403440af5bb3c25ef995d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305249"
---
# <a name="createuidefinition-elements"></a>Elementos CreateUiDefinition
En este artículo se describen el esquema y las propiedades de todos los elementos compatibles de CreateUiDefinition. 

## <a name="schema"></a>Esquema

El esquema de la mayoría de los elementos es el siguiente:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Propiedad | Obligatorio | DESCRIPCIÓN |
| -------- | -------- | ----------- |
| Nombre | Sí | Un identificador interno para hacer referencia a una instancia específica de un elemento. El uso más común del nombre del elemento es en `outputs`, donde se asignan los valores de salida de los elementos especificados a los parámetros de la plantilla. También se puede usar para enlazar el valor de salida de un elemento a `defaultValue` de otro elemento. |
| Tipo | Sí | El control de interfaz de usuario que se representa para el elemento. Para ver una lista de los tipos admitidos, consulte [Elementos](#elements). |
| label | Sí | Texto para mostrar del elemento. Algunos tipos de elementos contienen varias etiquetas, por lo que el valor podría ser un objeto que contiene varias cadenas. |
| defaultValue | Sin  | Valor predeterminado de la propiedad. Algunos tipos de elemento admiten valores predeterminados complejos, por lo que el valor podría ser un objeto. |
| toolTip | Sin  | El texto que se muestra en la información sobre herramientas del elemento. Similar a `label`, algunos elementos admiten varias cadenas de información sobre herramientas. Se pueden insertar vínculos en línea con la sintaxis de Markdown.
| constraints | Sin  | Una o varias propiedades que se utilizan para personalizar el comportamiento de validación del elemento. Las propiedades que se admiten para las restricciones varían según el tipo de elemento. Algunos tipos de elemento no permiten personalizar el comportamiento de validación y, por tanto, no tienen ninguna propiedad constraints. |
| options | Sin  | Propiedades adicionales que personalizan el comportamiento del elemento. De forma similar a `constraints`, las propiedades que se admiten varían según el tipo de elemento. |
| visible | Sin  | Indica si se muestra el elemento. Si `true`, se muestran el elemento y los elementos secundarios aplicables. El valor predeterminado es `true`. Use [funciones lógicas](create-uidefinition-functions.md#logical-functions) para controlar dinámicamente el valor de esta propiedad.

## <a name="elements"></a>Elementos

La documentación de cada elemento contiene un ejemplo de interfaz de usuario, un esquema, comentarios sobre el comportamiento del elemento (normalmente sobre la validación y personalización compatibles) y una salida de ejemplo.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.AvailabilityZoneDropDown](microsoft-network-availabilityzonedropdown.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Pasos siguientes
Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
