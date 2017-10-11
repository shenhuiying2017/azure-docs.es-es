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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 2009cfc956a2703e268c9364996aa2d0fbd8f279
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solución de problemas de colaboración B2B de Azure Active Directory

Estos son algunos de los recursos para solucionar problemas comunes relacionados con la colaboración B2B de Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>He agregado un usuario externo, pero no lo veo en mi libreta de direcciones global o en el selector de personas

En los casos donde los usuarios externos no se rellenan en la lista, el objeto puede tardar unos minutos en replicarse.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Un usuario invitado de B2B no aparece en el selector de personas de SharePoint Online/OneDrive 
 
La posibilidad de buscar usuarios invitados existentes en el selector de personas de SharePoint Online (SPO) está desactivada de manera predeterminada para así coincidir con el comportamiento heredado.

Para habilitarla, use la configuración "ShowPeoplePickerSuggestionsForGuestUsers" en el nivel de inquilino y colección de sitios. Para configurarla, use los cmdlets Set-SPOTenant y Set-SPOSite, que permiten que los miembros busquen a todos los usuarios invitados existentes en el directorio. Los cambios en el ámbito del inquilino no afectan los sitios de SPO que ya se aprovisionaron.

## <a name="invitations-have-been-disabled-for-directory"></a>Las invitaciones se han deshabilitado para el directorio

Si recibe una notificación de que no tiene permisos para invitar a usuarios, vaya a User Settings (Configuración de usuario) y compruebe que la cuenta de usuario está autorizada para invitar a usuarios externos.

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Si hace poco ha modificado estos valores o asignado el rol de invitador invitado a un usuario, podría haber un retraso de 15 a 60 minutos para que los cambios surtan efecto.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>El usuario al que he invitado está recibiendo un error durante el canje

Estos son algunos de los errores comunes:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>El administrador del invitado no permite que los usuarios de EmailVerified se creen en su inquilino

Cuando se invita a usuarios cuya organización usa Azure Active Directory, pero donde no existe la cuenta de usuario en concreto (por ejemplo, el usuario no existe en Azure AD contoso.com). El administrador de contoso.com puede tener una directiva en lugar de evitar que se creen usuarios. El usuario debe consultar al administrador y determinar si se permiten los usuarios externos. Es posible que el administrador del usuario externo necesite permitir usuarios comprobados por correo electrónico en el dominio (consulte este [artículo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sobre cómo permitir usuarios comprobados por correo electrónico).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>El usuario externo no se encuentra ya en un dominio federado

Si va a usar la autenticación de federación y el usuario no existe en Azure Active Directory, no se puede invitar al usuario.

Para resolver este problema, el administrador del usuario externo debe sincronizar la cuenta del usuario con Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>¿Cómo "\#", que habitualmente no es un carácter válido, se sincroniza con Azure AD?

"\#" es un carácter reservado en los UPN para la colaboración B2B de Azure AD o los usuarios externos, porque la cuenta de invitado user@contoso.com se convierte en user_contoso.com#EXT@fabrikam.onmicrosoft.com. Por lo tanto, los UPN con \# que proceden del entorno local no tienen permiso para iniciar sesión en Azure Portal. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recibo un error al agregar los usuarios externos a un grupo sincronizado

Los usuarios externos pueden agregarse únicamente a los grupos "Seguridad" o "Asignado", y no a aquellos que se controlan localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mi usuario externo no ha recibido un correo electrónico para realizar el canje

El invitado debe ponerse en contacto con su ISP o comprobar su filtro de correo no deseado para asegurarse de que se permite la siguiente dirección: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Tenga en cuenta que, en ocasiones, el mensaje personalizado no se incluye en los mensajes de invitación

Para cumplir con las leyes de privacidad, nuestras API no incluye mensajes personalizados en la invitación por correo electrónico cuando:

- El invitador no tiene una dirección de correo electrónico en el inquilino que invita.
- Cuando una entidad de seguridad de App Service envía la invitación

Si este escenario es importante para usted, puede suprimir nuestro correo electrónico de invitación de API y enviarlo a través del mecanismo de correo electrónico de su elección. Consulte al asesor legal de su organización para asegurarse de que cualquier correo electrónico que envíe de esta forma también cumple las leyes de privacidad.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
