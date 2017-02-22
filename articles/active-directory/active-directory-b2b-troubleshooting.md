---
title: "Solución de problemas de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Recursos para solucionar problemas comunes con la colaboración B2B de Azure Active Directory"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solución de problemas de colaboración B2B de Azure Active Directory

Estos son algunos de los recursos para solucionar problemas comunes relacionados con la colaboración B2B de Azure Active Directory (Azure AD).

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>No puedo crear un usuario externo debido a un contacto que ya existe

Si el usuario externo al que está invitando ya tiene un objeto de contacto, no podrá invitarlo hasta que se resuelva el conflicto, normalmente, tras eliminar ese objeto de contacto. Hasta que la colaboración B2B no esté disponible de forma generalizada, debe resolver el conflicto manualmente.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>He agregado un usuario externo, pero no lo veo en mi libreta de direcciones global o en el selector de personas

En los casos donde los usuarios externos no se rellenan en la lista, el objeto puede tardar unos minutos en replicarse.

## <a name="invitations-have-been-disabled-for-directory"></a>Las invitaciones se han deshabilitado para el directorio

Si recibe un mensaje de error que indica que no tiene permisos para invitar a usuarios, compruebe que la cuenta de usuario está autorizada para invitar a usuarios externos. Esto puede hacerse en Configuración de usuario:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Si hace poco ha modificado estos valores o asignado el rol de invitador invitado a un usuario, podría haber un retraso de 15 a 60 minutos para que los cambios surtan efecto.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>El usuario al que he invitado está recibiendo un error durante el canje

Estos son algunos de los errores comunes:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>El administrador del invitado no permite que los usuarios de EmailVerified se creen en su inquilino:

Al invitar a los usuarios cuya organización está usando Azure Active Directory donde no existe cuenta del usuario concreto (no se encuentra en AAD contoso.com). El administrador de contoso.com puede tener una directiva en lugar de evitar que se creen usuarios. El usuario externo debe ponerse en contacto con su administrador para determinar si se permiten usuarios externos; el administrador de dicho usuario puede que tenga que permitir usuarios de EmailVerified en su dominio (consulte este [artículo](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters) sobre cómo permitir usuarios de EmailVerified).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>El usuario externo no se encuentra ya en un dominio federado

No se puede invitar al usuario en los casos donde el usuario externo esté usando un solución de federación en la que se realiza la autenticación local y el usuario aún no se encuentra en Azure Active Directory.

Para resolver este problema, el administrador del usuario externo debe sincronizar la cuenta del usuario con Azure Active Directory.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>¿Cómo "\#", que suele ser un carácter no válido, se sincroniza con Azure AD?

"\#" es un carácter reservado en los UPN para los usuarios externos o la colaboración B2B de Azure AD (es decir, el usuario &lt;user@contoso.com&gt; invitado, se convierte en &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;). Por tanto, no se permite usar \# en los UPN procedentes de entornos locales para iniciar sesión en Azure Portal.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recibo un error al agregar los usuarios externos a un grupo sincronizado

Los usuarios externos pueden agregarse únicamente a los grupos "Seguridad" o "Asignado", y no a aquellos que se controlan localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mi usuario externo no ha recibido un correo electrónico para realizar el canje

El invitado debe ponerse en contacto con su ISP o comprobar su filtro de correo no deseado para asegurarse de que se permite la siguiente dirección: &lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>Mi destinatario ha recibido varios mensajes de correo electrónico de mí

En algunos casos en los que el destinatario de la invitación tiene varios alias para su cuenta, podrían recibir dos invitaciones. En estos casos, el primer vínculo canjeado es la cuenta que se crea, y el segundo vínculo de canje no es válido.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


