---
title: "Elemento de interfaz de usuario PublicIpAddressCombo de una aplicación administrada de Azure | Microsoft Docs"
description: Describe el elemento de la interfaz de usuario Microsoft.Network.PublicIpAddressCombo para aplicaciones administradas de Azure
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
ms.openlocfilehash: 71d8249046ed74f67514a93ece4efc2996ad9b4c
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Elemento de interfaz de usuario Microsoft.Network.PublicIpAddressCombo
Grupo de controles para seleccionar una dirección IP pública nueva o existente.

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
  "newOrExistingOrNone": "new"
}
```
- Cuando se especifica `options.hideNone`, `newOrExistingOrNone` siempre devuelve **none**.
- Cuando se especifica `options.hideDomainNameLabel`, `domainNameLabel` no se declara.

## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](managed-application-createuidefinition-elements.md).

