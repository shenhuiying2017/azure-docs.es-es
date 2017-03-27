---
title: "Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory"
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
ms.date: 03/14/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4b7ed095729e810f7f1112d3b6becfaf186bf508
ms.lasthandoff: 03/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory

Las preguntas frecuentes se actualizan periódicamente para reflejar nuevos intereses.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>¿Esta funcionalidad está disponible en el Portal de Azure clásico?
Las nuevas funcionalidades de esta actualización de la versión preliminar pública de la colaboración B2B de Azure AD solo están disponibles a través de [Azure Portal](https://portal.azure.com) y el nuevo panel de acceso. ¡Pruébelo!

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>¿Pueden acceder los usuarios de colaboración B2B a SharePoint Online y OneDrive?
Los usuarios invitados de colaboración B2B están en el directorio. Puede agregarlos a grupos en los que puede tenga permiso de acceso a los sitios de SharePoint Online y OneDrive, o bien seleccionarlos del selector de personas de SharePoint Online. Dado que estos son los usuarios invitados, los sitios de SharePoint Online habilitado el uso compartido externo.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>¿Se sigue admitiendo el mecanismo de carga CSV?
Sí. Consulte el ejemplo de PowerShell que hemos incluido.

### <a name="how-can-i-customize-my-invitation-emails"></a>¿Cómo puedo personalizar mis correos electrónicos de invitación?
Puede personalizar prácticamente cualquier elemento del proceso invitador con las API de invitación de B2B.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>¿Puede dejar el usuario externo invitado la organización a la que se le invitó?
En estos momentos no está disponible en esta actualización de la versión preliminar pública.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>¿Ahora que la autenticación multifactor (MFA) está disponible para los usuarios invitados, también pueden restablecer su método MFA?
Sí, de la misma manera que los usuarios normales.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>¿Qué organización es responsable de las licencias de MFA?
La organización invitadora es la que interviene y realiza la MFA. Por lo tanto, la organización invitadora es responsable de asegurarse de que disponen de suficientes licencias para sus usuarios B2B que realizan la MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>¿Qué ocurre si la organización de mi asociado ya tiene configurado la MFA? ¿Podemos confiar en su MFA y no usar la nuestra?
No en esta actualización de la versión preliminar pública, pero se admitirá en próximas versiones. Cuando se publique, podrá seleccionar asociados concretos para excluirlos de la MFA de su organización invitadora.

### <a name="how-can-i-achieve-delayed-invitations"></a>¿Cómo puedo conseguir invitaciones diferidas?
Algunas organizaciones deseen agregar usuarios de colaboración B2B, aprovisionarlos a las aplicaciones que requieren aprovisionamiento y, luego, enviar las invitaciones. Si es el caso, puede usar la API de invitación de colaboración B2B para personalizar el flujo de trabajo de incorporación.

### <a name="can-guest-users-and-contacts-co-exist"></a>¿Pueden coexistir los contactos y usuarios invitados?
Es posible que su organización haya agregado contactos que representan colaboradores externos para que se muestren en la lista global de direcciones y como sugerencias de dirección de correo electrónico durante la redacción del mensaje. Tal vez se pregunte qué sucede al agregar ahora estos mismos colaboradores como usuarios de colaboración B2B en el directorio, ¿no? En una futura versión, los usuarios de colaboración B2B y los objetos de contacto podrán coexistir en el directorio de la empresa. Permanezca atento a nuestros anuncios.

### <a name="can-i-make-my-guest-users-limited-admins"></a>¿Puedo convertir a mis usuarios invitados en administradores limitados?
Totalmente. Si es lo que su organización necesita, puede averiguar cómo hacerlo en el artículo sobre cómo [agregar usuarios invitados a un rol](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>¿Admite la colaboración B2B de Azure AD que los usuarios B2B puedan acceder a Azure Portal?
Los usuarios de colaboración B2B no deberían necesitar acceso a Azure Portal, a menos que se les asigne un rol de administrador limitado o global. En este caso, pueden acceder al portal. Si un usuario invitado que no tiene estos roles accede al portal, es posible que pueda acceder a determinadas partes de la experiencia porque el rol de usuario invitado tiene determinados permisos en el directorio, como se describe en las secciones anteriores.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>¿Puedo bloquearles el acceso a Azure Portal a los usuarios invitados?
Sí. Pero tenga cuidado al configurar esta directiva para evitar que se bloquee accidentalmente el acceso a los administradores y miembros.
Puede bloquearles el acceso a [Azure Portal](https://portal.azure.com) a los usuarios invitados a través de la directiva de acceso condicional de Service Management API de Windows Azure siguiendo los tres pasos de a continuación.
1. Modifique el grupo **Todos los usuarios** para que solo pueda contener miembros. ![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Crear un grupo dinámico que contenga los usuarios invitados. ![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure una directiva de acceso condicional para evitar que los usuarios invitados accedan al portal, tal y como se muestra en el siguiente vídeo.

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>¿Cuál es la escala de tiempo por la cual la colaboración B2B de Azure AD empezará a admitir cuentas de correo electrónico de consumidores y MFA?
Ahora, en esta actualización de la versión preliminar pública se admiten las cuentas de correo electrónico de consumidores y MFA.

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>¿Cuál es la escala de tiempo de disponibilidad general para Azure AD B2B?
Depende de los comentarios que envíen los clientes sobre el conjunto de características actual.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>¿Hay un plan para admitir el restablecimiento de contraseñas para los usuarios de colaboración B2B de Azure AD?
Sí, los usuarios (invitados) de colaboración B2B podrán usar estas características.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>¿Está también habilitada para los usuarios de un inquilino viral?
Actualmente, no.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>¿Microsoft CRM proporciona soporte técnico en línea para la colaboración B2B de Azure AD?
CRM proporcionará soporte técnico para la colaboración B2B de Azure AD después de que esté generalmente disponible.

### <a name="are-b2b-collaboration-guest-users-visible-in-sharepoint-onlineonedrive-people-picker"></a>¿Los usuarios invitados de colaboración B2B son visibles en el selector de personas de SharePoint Online/OneDrive?
 
Sí. Sin embargo, la capacidad de buscar usuarios invitados existentes en el selector de personas de SharePoint Online está desactivada de manera predeterminada para así coincidir con el comportamiento heredado. Para habilitarla, use la configuración "ShowPeoplePickerSuggestionsForGuestUsers" en el nivel de inquilino y colección de sitios. Para hacerlo, use los cmdlets Set-SPOTenant y Set-SPOSite, que permiten que los miembros busquen a todos los usuarios invitados existentes en el directorio. Los cambios en el ámbito del inquilino no afectan los sitios de SharePoint Online que ya se aprovisionaron.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>¿Cuál es la duración de una contraseña inicial para un usuario de colaboración B2B recién creado?
Azure AD tiene un conjunto fijo de requisitos de bloqueo de cuentas, seguridad de la contraseña y caracteres que se aplican igualmente a todas las cuentas de usuario en la nube de Azure AD. Las cuentas de usuario en la nube son las cuentas que no se federan con otro proveedor de identidades como las cuentas Microsoft, Facebook, ADFS o incluso otro inquilino en la nube (en el caso de la colaboración B2B). En el caso de las cuentas federadas, la directiva de contraseñas depende de la directiva del espacio local y la configuración de cuenta Microsoft del usuario.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Las aplicaciones desean diferenciar su experiencia entre un usuario inquilino y un usuario invitado. ¿Hay instrucciones estándar para esto? ¿La presencia de la notificación del proveedor de identidades es el modelo adecuado para esto?
 
Un usuario invitado puede usar cualquier proveedor de identidades para autenticarse, tal como se analiza en [Propiedades de un usuario de colaboración B2B](active-directory-b2b-user-properties.md). Por lo tanto, la propiedad adecuada para determinar esto es UserType. La notificación UserType no se incluye actualmente en el token. Las aplicaciones deben usar API Graph para consultar el usuario en el directorio y obtener su UserType.

### <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Personalización y API de colaboración B2B de Active Directory Azure](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

