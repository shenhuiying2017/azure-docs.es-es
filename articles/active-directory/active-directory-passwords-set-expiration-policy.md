---
title: "Establecimiento de directivas de caducidad de contraseña en Azure Active Directory | Microsoft Docs"
description: "Obtenga información sobre cómo comprobar las directivas de caducidad y cambiar la caducidad de contraseñas de usuario individualmente o de forma masiva para contraseñas de Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: f744a29e12fb693dd422f359d0faacaae004351b


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Establecimiento de directivas de caducidad de contraseña en Azure Active Directory
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).
>
>

Como administrador global de un servicio en la nube de Microsoft, puede usar el módulo de Microsoft Azure Active Directory para Windows PowerShell para configurar las contraseñas de usuario de modo que no caduquen. También puede usar cmdlets de Windows PowerShell para quitar la configuración de nunca caduca o para ver qué contraseñas de usuario están configuradas para que no caduquen. En este tema se proporciona contenido de ayuda para servicios en la nube, como Microsoft Intune y Office 365, que dependen de Microsoft Azure Active Directory para los servicios de identidad y directorio.

> [!NOTE]
> Solo las contraseñas de cuentas de usuario que no están sincronizadas a través de la sincronización de directorios pueden configurarse para que no caduquen. Para más información sobre la sincronización de directorios, consulte la lista de temas de [Integración de las identidades locales con Azure Active Directory](https://msdn.microsoft.com/library/azure/hh967642.aspx).
>
>

Para usar los cmdlets de Windows PowerShell, primero debe instalarlos.

## <a name="what-do-you-want-to-do"></a>¿Qué desea hacer?
* [Comprobación de la directiva de caducidad de una contraseña](#how-to-check-expiration-policy-for-a-password)
* [Configuración de una contraseña para que caduque](#set-a-password-to-expire)
* [Configuración de una contraseña para que no caduque](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Comprobación de la directiva de caducidad de una contraseña
1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Realice una de las operaciones siguientes:

   * Para ver si se ha configurado una sola contraseña de usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) (por ejemplo, aprilr@contoso.onmicrosoft.com)) o el identificador de usuario del usuario que desea comprobar: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Para ver la configuración "La contraseña nunca caduca" de todos los usuarios, ejecute el siguiente cmdlet: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Configuración de una contraseña para que caduque
1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Realice una de las operaciones siguientes:

   * Para configurar la contraseña de un usuario para que caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Para configurar las contraseñas de todos los usuarios de la organización de modo que caduquen, use el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Configure una contraseña para que no caduque nunca
1. Conéctese a Windows PowerShell con sus credenciales de administrador de empresa.
2. Realice una de las operaciones siguientes:

   * Para configurar la contraseña de un usuario para que nunca caduque, ejecute el cmdlet siguiente con el nombre principal de usuario (UPN) o el identificador de usuario del usuario: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Para configurar las contraseñas de todos los usuarios de una organización para que nunca caduquen, ejecute el siguiente cmdlet: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Pasos siguientes
* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md).



<!--HONumber=Feb17_HO2-->


