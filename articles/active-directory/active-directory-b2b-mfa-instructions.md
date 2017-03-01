---
title: "Autenticación multifactor para usuarios de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "La colaboración B2B de Azure Active Directory admite la autenticación multifactor (MF) para poder acceder de manera selectiva a las aplicaciones corporativas."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 32d0b45080d57712209e0c5a3e5adf981fb4b66e
ms.lasthandoff: 02/17/2017


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>La autenticación multifactor para usuarios de colaboración B2B de Azure Active Directory

Con esta actualización de la versión preliminar pública de la colaboración B2B de Azure AD, ofrecemos a las organizaciones la posibilidad de que también apliquen directivas de autenticación multifactor (MFA) para los usuarios de colaboración B2B. En esta actualización, MFA siempre se aplica en el espacio de recursos.

Esto significa lo siguiente:
1. El administrador o el trabajador de la información de la empresa A invita a un usuario de la empresa B a una aplicación Foo de la empresa A.
2. La aplicación *Foo* de la empresa A se configura para requerir MFA en el acceso.
3. Cuando el usuario de la empresa B trata de acceder a la aplicación Foo desde el espacio de la empresa A, se le pide que complete un desafío de MFA según sea necesario mediante la directiva de MFA de la empresa A.
4. Para que el usuario pueda configurar su MFA con la empresa A, elige su opción de MFA.
5. Esto funcionará para cualquier identidad (Azure AD o MSA, por ejemplo, si los usuarios de la empresa B se autentican con un identificador social).
6. La empresa A deberá tener SKU premium adecuados de Azure AD que sean compatibles con MFA. El usuario de la empresa B consumirá esta licencia de la empresa A.
7. En resumen, el espacio invitador *siempre* es responsable de la MFA de los usuarios de colaboración B2B de la organización del asociado, no la propia organización del asociado (incluso si tiene funcionalidades de MFA). En futuras versiones, permitiremos que la organización invitadora confíe en la MFA de las organizaciones de asociados específicos en lugar de usar la de la organización invitadora.

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configuración de Multi-Factor Authentication para los usuarios de colaboración B2B
Para descubrir lo fácil que es configurar MFA para los usuarios de colaboración B2B, vea el siguiente vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiencia de MFA de los usuarios B2B para el canje de ofertas
En la siguiente animación se puede ver el canje, como se muestra en este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

## <a name="mfa-reset-for-b2b-collaboration-users"></a>Restablecimiento de la MFA para los usuarios de colaboración B2B
Actualmente, el administrador puede requerir que se vuelvan a probar los usuarios de colaboración B2B solo mediante los siguientes cmdlets de PowerShell. Por lo tanto, los siguientes cmdlets de PowerShell deben usarse si desea restablecer el método de prueba de un usuario de la colaboración B2B.

> [!NOTE]
> Para usar el nuevo cmdlet, debe instalar el módulo de la versión&2; de Azure AD PowerShell, que puede obtener desde aquí: https://www.powershellgallery.com/packages/AzureADPreview.

1. Conectarse a Azure

  ```
  Connect-MsolService and login
  ```
2. Obtenga todos los usuarios con los métodos de prueba.

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Aquí tiene un ejemplo:

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Restablezca el método MFA para un usuario específico. Después, puede usar ese nombre principal de usuario para ejecutar el comando de restablecimiento con el fin de requerir al usuario de la colaboración B2B volver a establecer métodos de prueba. Ejemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

