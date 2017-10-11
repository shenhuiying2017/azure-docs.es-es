---
title: Directivas de recursos de Azure para cuentas de almacenamiento | Microsoft Docs
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
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="apply-resource-policies-to-storage-accounts"></a>Aplicación de directivas de recursos a cuentas de almacenamiento
En este tema se muestran varias [directivas de recursos](resource-manager-policy.md) que puede aplicar a cuentas de Azure Storage. Estas directivas garantizan la coherencia de las cuentas de almacenamiento implementada en la organización. 

## <a name="define-permitted-storage-account-types"></a>Definición de tipos de cuenta de almacenamiento permitidos

La siguiente directiva restringe qué [tipos de cuenta de almacenamiento](../storage/common/storage-redundancy.md) se pueden implementar:

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

Una regla de directiva similar con un parámetro para aceptar las SKU permitidas está disponible como una definición de directiva integrada. La directiva integrada tiene el identificador de recurso `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

## <a name="define-permitted-access-tier"></a>Definición del nivel de acceso permitido

La siguiente directiva especifica el tipo de [nivel de acceso](../storage/blobs/storage-blob-storage-tiers.md) que se puede especificar para las cuentas de almacenamiento:

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

La siguiente directiva requiere que todas las cuentas de almacenamiento habiliten el [cifrado del servicio Storage](../storage/common/storage-service-encryption.md):

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

Esta regla de directiva también está disponible como una definición de directiva integrada con el identificador de recurso `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Pasos siguientes
* Después de definir una regla de directiva (como se muestra en los ejemplos anteriores), debe crear la definición de directiva y asignarla a un ámbito. El ámbito puede ser una suscripción, un grupo de recursos o un recurso. Para asignar directivas a través del portal, consulte [Use Azure portal to assign and manage resource policies](resource-manager-policy-portal.md) (Uso de Azure Portal para asignar y administrar directivas de recursos). Para asignar directivas a través de la API de REST, PowerShell o la CLI de Azure, consulte [Assign and manage policies through script](resource-manager-policy-create-assign.md) (Asignación y administración de directivas a través de scripts). 
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

