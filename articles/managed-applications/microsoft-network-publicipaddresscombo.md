---
title: "Elemento de interfaz de usuario PublicIpAddressCombo de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Network.PublicIpAddressCombo para aplicaciones administradas de Azure
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
ms.openlocfilehash: 990b84cf0df74b7b08a5fa105c2c2213aa251f9d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento de interfaz de usuario Microsoft.Network.PublicIpAddressCombo
Grupo de controles para seleccionar una dirección IP pública nueva o existente. Use este elemento al [crear una aplicación administrada de Azure](publish-service-catalog-app.md).

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- Si el usuario selecciona “None” para la dirección IP pública, se oculta el cuadro de texto de la etiqueta de nombre de dominio.
- Si el usuario selecciona una dirección IP pública, se deshabilita el cuadro de texto de la etiqueta de nombre de dominio. Su valor es la etiqueta de nombre de dominio de la dirección IP seleccionada.
- El sufijo de nombre de dominio (por ejemplo, westus.cloudapp.azure.com) se actualiza automáticamente en función de la ubicación seleccionada.

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- Si `constraints.required.domainNameLabel` está establecido en **true**, el usuario debe proporcionar una etiqueta de nombre de dominio al crear una dirección IP pública nueva. Las direcciones IP públicas existentes sin una etiqueta no están disponibles para la selección.
- Si `options.hideNone` está establecido en **true**, la opción para seleccionar **None** como dirección IP pública está oculta. El valor predeterminado es **false**.
- Si `options.hideDomainNameLabel` está establecido en **true**, se oculta el cuadro de texto de la etiqueta de nombre de dominio. El valor predeterminado es **false**.
- Si `options.hideExisting` es true, el usuario no puede elegir una dirección IP pública existente. El valor predeterminado es **false**.

## <a name="sample-output"></a>Salida de ejemplo
Si el usuario no selecciona ninguna dirección IP pública, se espera la siguiente salida:
```json
{
  "newOrExistingOrNone": "none"
}
```

Si el usuario selecciona una dirección IP pública nueva o existente, se espera la siguiente salida:
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- Cuando `options.hideNone` se especifica como **true**, `newOrExistingOrNone` solo tendrá un valor de **nuevo** o **existente**.
- Cuando `options.hideDomainNameLabel` se especifica como **verdadero**, `domainNameLabel` no se declara.

## <a name="next-steps"></a>pasos siguientes
* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](overview.md).
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
