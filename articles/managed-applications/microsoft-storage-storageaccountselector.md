---
title: Elemento de interfaz de usuario StorageAccountSelector de aplicaciones administradas de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Storage.StorageAccountSelector para aplicaciones administradas de Azure
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
ms.openlocfilehash: 366a862acc15decf6a8e19f875d5d052695f373c
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Elemento de interfaz de usuario Microsoft.Storage.StorageAccountSelector
Un control para seleccionar una cuenta de almacenamiento nueva o existente. Use este elemento al [crear una aplicación administrada de Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Si se especifica, se valida automáticamente la unicidad de `defaultValue.name`. Si el nombre de cuenta de almacenamiento no es único, el usuario debe especificar otro nombre diferente o elegir una cuenta de almacenamiento existente.
- El valor predeterminado de `defaultValue.type` es **Premium_LRS**.
- Los tipos no especificados en `constraints.allowedTypes` está oculto, mientras que los tipos no especificado en `constraints.excludedTypes` se muestran.
Tanto `constraints.allowedTypes` como `constraints.excludedTypes` son opcionales, pero no se pueden usar simultáneamente.
- Si el valor de `options.hideExisting` es **true**, el usuario no puede elegir una cuenta de almacenamiento existente. El valor predeterminado es **false**.


## <a name="sample-output"></a>Salida de ejemplo
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
