---
title: Directivas de almacenamiento de Azure Resource Manager | Microsoft Docs
description: "Se describen las directivas de Azure Resource Manager para administrar la implementación de cuentas de almacenamiento."
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>Aplicación de directivas de recursos de Azure a cuentas de almacenamiento
Mediante directivas de Azure Resource Manager se definen reglas coherentes sobre el modo en que se implementan los recursos en su organización. Además, se crean directivas personalizadas para garantizar que los usuarios de su organización no infringen las convenciones que son necesarias para administrar los recursos de esta. En este tema se muestran varias directivas que definen reglas para cuentas de Azure Storage. Para más información sobre las directivas, consulte [Uso de directivas para administrar los recursos ](resource-manager-policy.md).

En los ejemplos de este tema se muestran los valores codificados de forma fija en la regla de directiva. Sin embargo, puede usar parámetros para pasar valores que se usan al asignar la directiva. Para más información, consulte [Parámetros de la directiva](resource-manager-policy.md#parameters).

## <a name="define-permitted-storage-account-types"></a>Definición de tipos de cuenta de almacenamiento permitidos

La siguiente directiva restringe qué [tipos de cuenta de almacenamiento](../storage/storage-redundancy.md) se pueden implementar:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="define-permitted-access-tier"></a>Definición del nivel de acceso permitido

La siguiente directiva especifica el tipo de [nivel de acceso](../storage/storage-blob-storage-tiers.md) que se puede especificar para las cuentas de almacenamiento:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Comprobación de que esté habilitado el cifrado

La siguiente directiva requiere que todas las cuentas de almacenamiento habiliten el [cifrado del servicio Storage](../storage/storage-service-encryption.md):

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="create-and-assign-policies"></a>Creación y asignación de directivas

Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para ver ejemplos sobre cómo crear y asignar directivas, consulte [Creación y asignación de directivas](resource-manager-policy.md#create-and-assign-a-policy). 

## <a name="next-steps"></a>Pasos siguientes
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).




<!--HONumber=Jan17_HO3-->


