---
title: Error RequestDisallowedByPolicy con la directiva de recursos de Azure | Microsoft Docs
description: Describe la causa del error RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Error RequestDisallowedByPolicy con la directiva de recursos de Azure

Este artículo describe la causa del error RequestDisallowedByPolicy y también proporciona soluciones para este error.

## <a name="symptom"></a>Síntoma

Al intentar llevar a cabo una acción durante la implementación, es posible que reciba un error **RequestDisallowedByPolicy** que impida que la acción se realice. El siguiente ejemplo muestra el error:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Solución de problemas

Para recuperar los detalles de la directiva que bloqueó la implementación, use uno de los métodos siguientes:

### <a name="method-1"></a>Método 1

En PowerShell, proporcione ese identificador de directiva como el parámetro `Id` para recuperar los detalles de la directiva que bloqueó la implementación.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>Método 2 

En la CLI de Azure 2.0, proporcione el nombre de la definición de directiva: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solución

Para la seguridad o el cumplimiento, el departamento de TI imponer una directiva de recursos que prohíba la creación de direcciones IP públicas, grupos de seguridad de red, rutas definidas por el usuario o tablas de enrutamiento. El mensaje de error en la sección **Síntomas** muestra una directiva denominada **regionPolicyDefinition**. La directiva puede tener un nombre diferente.
Para resolver este problema, trabaje con el departamento de TI para revisar las directivas de recursos y determinar cómo realizar la acción solicitada conforme a esas directivas.

Para más información, consulte los siguientes artículos.

- [Información general sobre las directivas de recursos](resource-manager-policy.md)
- [Visualización de asignaciones de directiva a través de Azure Portal](resource-manager-policy-portal.md)
