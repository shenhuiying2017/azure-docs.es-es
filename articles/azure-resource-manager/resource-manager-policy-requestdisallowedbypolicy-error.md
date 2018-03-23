---
title: Error RequestDisallowedByPolicy con la directiva de recursos de Azure | Microsoft Docs
description: Describe la causa del error RequestDisallowedByPolicy.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 5a9efa6b807e933726104e7af315589ede5d9b74
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Error RequestDisallowedByPolicy con la directiva de recursos de Azure

Este artículo describe la causa del error RequestDisallowedByPolicy y también proporciona soluciones para este error.

## <a name="symptom"></a>Síntoma

Durante la implementación, es posible que reciba un error **RequestDisallowedByPolicy** que impida crear los recursos. El siguiente ejemplo muestra el error:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>solución de problemas

Para recuperar los detalles de la directiva que bloqueó la implementación, use uno de los métodos siguientes:

### <a name="powershell"></a>PowerShell

En PowerShell, proporcione ese identificador de directiva como el parámetro `Id` para recuperar los detalles de la directiva que bloqueó la implementación.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

En la CLI de Azure 2.0, proporcione el nombre de la definición de directiva:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solución

Por motivos de seguridad o conformidad, los administradores de la suscripción deben asignar directivas que limiten la forma en que se implementan los recursos. Por ejemplo, la suscripción puede tener una directiva que impide crear direcciones IP públicas, grupos de seguridad de red, rutas definidas por el usuario o tablas de rutas. En el mensaje de error de la sección **Síntoma** aparece el nombre de la directiva.
Para resolver este problema, revise las directivas de recursos y determine cómo implementar los recursos que cumplen con dichas directivas.

Para más información, consulte los siguientes artículos.

- [¿Qué es Azure Policy?](../azure-policy/azure-policy-introduction.md)
- [Creación y administración de directivas para aplicar el cumplimiento](../azure-policy/create-manage-policy.md)
