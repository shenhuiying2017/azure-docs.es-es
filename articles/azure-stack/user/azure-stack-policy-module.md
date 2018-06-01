---
title: Uso del módulo de directivas de Azure Stack | Microsoft Docs
description: Aprenda a restringir una suscripción de Azure para que se comporte como una suscripción de Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259090"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Administración de la directiva de Azure con el módulo de directivas de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

El módulo de directivas de Azure Stack le permite configurar una suscripción de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack.  El módulo utiliza el cmdlet **New-AzureRMPolicyAssignment** para crear una directiva de Azure, lo que limita los tipos de recursos y los servicios disponibles en una suscripción.  Después de configurar la directiva, puede usar su suscripción de Azure para desarrollar aplicaciones destinadas a Azure Stack.

## <a name="install-the-module"></a>Instalación del módulo

1. Instale la versión requerida del módulo de PowerShell AzureRM, tal y como se describe en el paso 1 de [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).
2. [Descargue las herramientas de Azure Stack desde GitHub](azure-stack-powershell-download.md).
3. [Configure PowerShell for use with Azure Stack](azure-stack-powershell-configure-user.md) (Configuración de PowerShell para usarlo con Azure Stack)

4. Importe el módulo AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicación de la directiva a la suscripción de Azure

Puede utilizar el comando siguiente para aplicar una directiva predeterminada de Azure Stack con su suscripción de Azure. Antes de ejecutar este comando, reemplace *Azure Subscription Name* por el nombre de su suscripción de Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Aplicación de la directiva a un grupo de recursos

Puede querer aplicar directivas que sean más granular. Por ejemplo, puede tener otros recursos que se ejecuten en la misma suscripción. Puede delimitar la aplicación de directivas a un grupo de recursos específico, lo que le permite probar sus aplicaciones para Azure Stack con recursos de Azure. Antes de ejecutar el comando siguiente, reemplace *Azure Subscription Name* por el nombre de su suscripción de Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Directiva en acción

Una vez haya implementado la directiva de Azure, recibe un error al intentar implementar un recurso prohibido por la directiva.

![Resultado del error de implementación de un recurso debido a la restricción de la directiva](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
