---
title: "Uso del módulo de directivas de Azure Stack | Microsoft Docs"
description: "Aprenda a restringir una suscripción de Azure para que se comporte como una suscripción de Azure Stack"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: mabrigg
ms.openlocfilehash: 71f17a460f4a81a98e2cdef183acb29f721d584e
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Administración de la directiva de Azure con el módulo de directivas de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

El módulo de directivas de Azure Stack le permite configurar una suscripción de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack.  El módulo utiliza el cmdlet **New-AzureRMPolicyAssignment** para crear una directiva de Azure, lo que limita los tipos de recursos y los servicios disponibles en una suscripción.  Una vez completado, puede usar su suscripción de Azure para desarrollar aplicaciones destinadas a Azure Stack.  

## <a name="install-the-module"></a>Instalación del módulo
1. Instale la versión requerida del módulo de PowerShell AzureRM, tal y como se describe en el paso 1 de [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).   
2. [Descargue las herramientas de Azure Stack desde GitHub](azure-stack-powershell-download.md).  
3. [Configure PowerShell for use with Azure Stack](azure-stack-powershell-configure-user.md) (Configuración de PowerShell para usarlo con Azure Stack)

4. Importe el módulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>Aplicación de la directiva a la suscripción
El comando siguiente puede utilizarse para aplicar una directiva predeterminada de Azure Stack con su suscripción de Azure. Antes de ejecutarla, reemplace *Azure Subscription Name* por el nombre de su suscripción de Azure.

```PowerShell
Login-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Aplicación de la directiva a un grupo de recursos
Puede aplicar las directivas de una forma más específica.  Por ejemplo, puede tener otros recursos que se ejecuten en la misma suscripción.  Puede delimitar la aplicación de directivas a un grupo de recursos específico, lo que le permite probar sus aplicaciones para Azure Stack con recursos de Azure. Antes de ejecutarla, reemplace *Azure Subscription Name* por el nombre de su suscripción de Azure.

```PowerShell
Login-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Directiva en acción
Una vez haya implementado la directiva de Azure, recibe un error al intentar implementar un recurso prohibido por la directiva.  

![Resultado del error de implementación de un recurso debido a la restricción de la directiva](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Pasos siguientes
[Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)

[Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)

[Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
