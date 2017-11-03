---
title: Elemento de interfaz de usuario MultiStorageAccountCombo de aplicaciones administradas de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Storage.MultiStorageAccountCombo para aplicaciones administradas de Azure
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
ms.openlocfilehash: e1dda7917988146807ca6cfab10a3a4eac0b7bb2
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Elemento de la interfaz de usuario Microsoft.Storage.MultiStorageAccountCombo
Un grupo de controles para crear varias cuentas de almacenamiento con nombres que comienzan con un prefijo común. Use este elemento al [crear una aplicación administrada de Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- El valor de `defaultValue.prefix` se concatena con uno o varios números enteros para generar la secuencia de nombres de cuenta de almacenamiento. Por ejemplo, si `defaultValue.prefix` es **foobar** y `count` es **2**, se generan los nombres de cuenta de almacenamiento **foobar1** y **foobar2**. La unicidad de los nombres de cuenta de almacenamiento generados se valida automáticamente.
- Los nombres de cuenta de almacenamiento se generan lexicográficamente en función de `count`. Por ejemplo, si `count` es 10, los nombres de cuenta de almacenamiento terminan en enteros de 2 dígitos (01, 02, 03, etc.).
- El valor predeterminado de `defaultValue.prefix` es **null**, mientras que el de `defaultValue.type` es **Premium_LRS**.
- Los tipos no especificados en `constraints.allowedTypes` está oculto, mientras que los tipos no especificado en `constraints.excludedTypes` se muestran.
Tanto `constraints.allowedTypes` como `constraints.excludedTypes` son opcionales, pero no se pueden usar simultáneamente.
- Además de para generar nombres de cuenta de almacenamiento, `count` se usa para establecer el multiplicador apropiado para el elemento. Admite un valor estático, como **2**, o un valor dinámico de otro elemento, como `[steps('step1').storageAccountCount]`. El valor predeterminado es **1**.

## <a name="sample-output"></a>Salida de ejemplo
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
