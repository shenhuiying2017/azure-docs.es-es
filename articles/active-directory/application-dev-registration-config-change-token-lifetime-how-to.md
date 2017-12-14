---
title: "Modificación de los valores predeterminados de vigencia de los tokens en una aplicación personalizada | Microsoft Docs"
description: "Aprenda a actualizar las directivas de vigencia de los tokens para la aplicación que está desarrollando en Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8067ecf3e274f65abe2c82f20dd2f4469344f3b6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Modificación de los valores predeterminados de vigencia de los tokens en una aplicación personalizada

Azure AD Premium permite a los desarrolladores de aplicaciones y a los administradores de inquilinos configurar la vigencia de los tokens emitidos para clientes no confidenciales. Las directivas de vigencia de los tokens se establecen para todos los inquilinos o para los recursos a los que se va a acceder.

 * Para establecer una directiva de vigencia de tokens, debe descargar el [módulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

 * Ejecute el comando **Connect-AzureAD -Confirm**.

 * En esta directiva de ejemplo, se establece la actualización del token con arreglo a un único factor de antigüedad máxima. Cree la directiva: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Consulte el documento sobre la [configuración de la vigencia de los tokens](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) para aprender a crear una directiva personalizada.

## <a name="next-steps"></a>Pasos siguientes
[Configuración de la vigencia de los tokens](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referencia de tokens de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

