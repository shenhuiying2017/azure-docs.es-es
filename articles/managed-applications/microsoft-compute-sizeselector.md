---
title: Elemento de interfaz de usuario SizeSelector de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Compute.SizeSelector para Azure Portal.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: d1b4974c78a5cdb7b4eb885797319b283be2d393
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260847"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de interfaz de usuario Microsoft.Compute.SizeSelector
Control para seleccionar un tamaño para una o varias instancias de máquina virtual.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- `recommendedSizes` debe contener al menos un tamaño. El primer tamaño recomendado se utiliza como valor predeterminado.
- Si no hay un tamaño recomendado en la ubicación seleccionada, el tamaño se omite automáticamente. En su lugar, se utiliza el siguiente tamaño recomendado.
- Los tipos no especificados en `constraints.allowedSizes` se ocultan, mientras que los tipos no especificados en `constraints.excludedSizes` se muestran.
Tanto `constraints.allowedSizes` como `constraints.excludedSizes` son opcionales, pero no se pueden usar simultáneamente. Para determinar la lista de los tamaños disponibles, consulte la [lista de tamaños de máquina virtual disponibles para una suscripción](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform` debe especificarse y puede ser **Windows** o **Linux**. Se usa para determinar los costos de hardware de las máquinas virtuales.
- `imageReference` se omite para las imágenes propias, pero se proporciona para las imágenes de terceros. Se usa para determinar los costos de software de las máquinas virtuales.
- `count` se usa para establecer el multiplicador apropiado para el elemento. Admite un valor estático, como **2**, o un valor dinámico de otro elemento, como `[steps('step1').vmCount]`. El valor predeterminado es **1**.
- `numAvailabilityZonesRequired` puede ser 1, 2 o 3.

## <a name="sample-output"></a>Salida de ejemplo
```json
"Standard_D1"
```

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
