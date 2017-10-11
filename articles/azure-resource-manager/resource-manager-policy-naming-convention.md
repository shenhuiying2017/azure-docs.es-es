---
title: Directivas de recursos de Azure para la nomenclatura de convenciones | Microsoft Docs
description: Describe las directivas de Azure Resource Manager para convenciones de nomenclatura de recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>Aplicación de directivas de recursos para nombres y texto
Este tema muestra varias [directivas de recurso](resource-manager-policy.md) que puede aplicar para establecer las convenciones de texto y nomenclatura. Estas directivas garantizan la coherencia para valores de etiquetas y nombres de recursos. 

## <a name="set-naming-convention-with-wildcard"></a>Establecimiento de la convención de nomenclatura con caracteres comodín
En el ejemplo siguiente se muestra el uso de caracteres comodín que admite la condición **like**. La condición indica que se denegará la solicitud si el nombre coincide con el patrón indicado (namePrefix\*nameSuffix):

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>Establecimiento de la convención de nomenclatura con patrones

Para especificar que los nombres de recursos coincidan con un patrón, use la condición match. El ejemplo siguiente requiere que los nombres empiecen por `contoso` y contengan seis letras adicionales:

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>Establecimiento del patrón de fecha para el valor de etiqueta

Para requerir un patrón de fecha de dos dígitos, guion, tres letras, guion y cuatro dígitos, use:

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts). 
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

