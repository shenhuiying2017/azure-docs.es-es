---
title: "Cambio del identificador de inquilino de Key Vault después de mover una suscripción | Microsoft Docs"
description: "Aprenda a cambiar el identificador de inquilino de Key Vault después de que una suscripción se haya movido a otro inquilino"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: 2f007dd4f877b48003cddcefa5f4321049853361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Cambio del identificador de inquilino de Key Vault después de mover una suscripción
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>P: Mi suscripción se ha movido del inquilino A al inquilino B. ¿Cómo cambio el identificador de inquilino de Key Vault existente y establezco las ACL correctas para las entidades de seguridad del inquilino B?
Cuando se crea un Key Vault nuevo en una suscripción, se asocia automáticamente a un identificador de inquilino de Azure Active Directory predeterminado de dicha suscripción. Las entradas de la directiva de acceso también se asocian con este identificador de inquilino. Al mover una suscripción de Azure del inquilino A al inquilino B, las entidades de seguridad (usuarios y aplicaciones) de este último inquilino no pueden acceder a las instancias de Key Vault existentes. Para corregir este problema, es preciso:

* Cambiar el identificador de inquilino asociado a todas las instancias de Key Vault existentes en esta suscripción al inquilino B.
* Quitar todas las entradas de la directiva de acceso existentes.
* Agregar nuevas entradas de la directiva de acceso asociadas al inquilino B.

Por ejemplo, si tiene el Key Vault 'myvault' en una suscripción que se ha movido del inquilino A al inquilino B, con el siguiente código podrá cambiar el identificador de inquilino de este Key Vault y quitar directivas de acceso antiguas.

<pre>
$Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Dado que este almacén estaba en el inquilino A antes de mover la suscripción original, el valor original de **$vault. Properties.TenantId** es el inquilino A, mientras que **(Get-AzureRmContext).Tenant.TenantId** es el inquilino B.

Ahora que el almacén está asociado al identificador de inquilino correcto y que se han quitado las entradas de la directiva de acceso antiguas, establezca nuevas entradas de la directiva de acceso con [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Pasos siguientes
Si le queda alguna duda al respecto de Azure Key Vault, visite los [foros de Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

