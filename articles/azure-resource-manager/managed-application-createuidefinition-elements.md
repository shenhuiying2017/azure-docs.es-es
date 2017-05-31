---
title: Funciones para crear definiciones de interfaz de usuario para aplicaciones administradas de Azure | Microsoft Docs
description: "Describe las funciones que se usarán al crear definiciones de interfaz de usuario para aplicaciones administradas de Azure"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 635e44a7ec6f9244f5fe75eb5ad947cdd8ae59a4
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="createuidefinition-elements"></a>Elementos CreateUiDefinition
En este artículo se describen el esquema y las propiedades de todos los elementos compatibles de CreateUiDefinition. Use estos elementos al [crear una aplicación administrada de Azure](managed-application-publishing.md). El esquema de la mayoría de los elementos es el siguiente:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```
| Propiedad | Obligatorio | Description |
| -------- | -------- | ----------- |
| name | Sí | Un identificador interno para hacer referencia a una instancia específica de un elemento. El uso más común del nombre del elemento es en `outputs`, donde se asignan los valores de salida de los elementos especificados a los parámetros de la plantilla. También se puede usar para enlazar el valor de salida de un elemento a `defaultValue` de otro elemento. |
| type | Sí | El control de interfaz de usuario que se representa para el elemento. Para ver una lista de los tipos admitidos, consulte [Elementos](#elements). |
| label | Sí | Texto para mostrar del elemento. Algunos tipos de elementos contienen varias etiquetas, por lo que el valor podría ser un objeto que contiene varias cadenas. |
| defaultValue | No | Valor predeterminado de la propiedad. Algunos tipos de elemento admiten valores predeterminados complejos, por lo que el valor podría ser un objeto. |
| toolTip | No | El texto que se muestra en la información sobre herramientas del elemento. Similar a `label`, algunos elementos admiten varias cadenas de información sobre herramientas. Se pueden insertar vínculos en línea con la sintaxis de Markdown.
| constraints | No | Una o varias propiedades que se utilizan para personalizar el comportamiento de validación del elemento. Las propiedades que se admiten para las restricciones varían según el tipo de elemento. Algunos tipos de elemento no permiten personalizar el comportamiento de validación y, por tanto, no tienen ninguna propiedad constraints. |
| options | No | Propiedades adicionales que personalizan el comportamiento del elemento. De forma similar a `constraints`, las propiedades que se admiten varían según el tipo de elemento. |
| visible | No | Indica si se muestra el elemento. Si `true`, se muestran el elemento y los elementos secundarios aplicables. El valor predeterminado es `true`. Use [funciones lógicas](managed-application-createuidefinition-functions.md#logical-functions) para controlar dinámicamente el valor de esta propiedad.

## <a name="elements"></a>Elementos

La documentación de cada elemento contiene un ejemplo de interfaz de usuario, un esquema, comentarios sobre el comportamiento del elemento (normalmente sobre la validación y personalización compatibles) y una salida de ejemplo.

- [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
- [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](managed-application-overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).

