---
title: "Concesión de permisos para que muchas aplicaciones tengan acceso a un almacén de Azure Key Vault | Microsoft Docs"
description: "Obtenga información sobre cómo conceder permisos para que muchas aplicaciones tengan acceso a almacén de claves."
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14da9256def60d678ef5cae795fef1c373914b5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Concesión de permisos para que muchas aplicaciones tengan acceso a almacén de claves

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>P.: Tengo varias aplicaciones (más de 16) que necesitan tener acceso a un almacén de claves. Puesto que Key Vault solo acepta 16 entradas de control de acceso, ¿cómo puedo conseguirlo?

La directiva de control de acceso de Key Vault solo admite 16 entradas. Sin embargo, puede crear un grupo de seguridad de Azure Active Directory. Agregue todas las entidades de servicio asociadas a este grupo de seguridad y, luego, conceda acceso a este grupo de seguridad a Key Vault.

Instalación de los requisitos previos:
* [Instale el módulo PowerShell de Azure Active Directory V2](https://www.powershellgallery.com/packages/AzureAD).
* [Instale Azure PowerShell](/powershell/azure/overview).
* Para ejecutar los siguientes comandos, necesita permisos para crear y editar grupos en el inquilino de Azure Active Directory. Si no tiene permisos, debe ponerse en contacto con el administrador de Azure Active Directory.

Ejecute los siguientes comandos en PowerShell.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionToKeys all –PermissionToSecrets all –PermissionToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Si tiene que conceder un conjunto de permisos diferente para un grupo de aplicaciones, cree un grupo de seguridad de Azure Active Directory independiente para dichas aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [proteger el almacén de claves](key-vault-secure-your-key-vault.md).
