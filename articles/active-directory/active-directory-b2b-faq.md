---
title: "Preguntas más frecuentes acerca de la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Vea las respuestas a las preguntas más frecuentes acerca de la colaboración B2B de Azure Active Directory."
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
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: bfedbbf8b26e1b129584a6a644e64a15635f5723
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Preguntas más frecuentes acerca de la colaboración B2B de Azure Active Directory

Las preguntas más frecuentes (P+F) acerca de la colaboración negocio a negocio (B2B) de Azure Active Directory (Azure AD) se actualizan periódicamente para incluir nuevos temas.

### <a name="is-azure-ad-b2b-collaboration-available-in-the-azure-classic-portal"></a>¿Está disponible la colaboración B2B de Azure AD en el Portal de Azure clásico?
No. Las características de la colaboración B2B de Azure AD solo están disponibles en [Azure Portal](https://portal.azure.com) y en el [panel de acceso](https://myapps.microsoft.com/). 

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>¿Se puede personalizar la página de inicio de sesión para que sea más intuitiva para los usuarios invitados de colaboración de B2B?
Por supuesto. Consulte nuestra [entrada del blog relativa a esta característica](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para más información acerca de cómo personalizar la página de inicio de sesión de una organización, consulte [Incorporación de la personalización de marca de empresa a sus páginas de inicio de sesión y panel de acceso](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>¿Pueden acceder los usuarios de colaboración B2B a SharePoint Online y OneDrive?
Sí. Sin embargo, la capacidad de buscar usuarios invitados existentes en SharePoint Online mediante el uso del selector de personas está **desactivada**. Para activar la opción de búsqueda de usuarios invitados existentes, establezca **ShowPeoplePickerSuggestionsForGuestUsers** en **Activado**. Esta configuración se puede activar en el nivel del inquilino o en el nivel de colección de sitios. Esta configuración se puede cambiar mediante los cmdlets Set-SPOTenant y SPOSite. Con estos cmdlets, los miembros pueden buscar todos los usuarios invitados existentes en el directorio. Los cambios en el ámbito del inquilino no afectan a los sitios de SharePoint Online que ya se han aprovisionado.

### <a name="is-the-csv-upload-feature-still-supported"></a>¿Aún se admite la característica de carga de CSV?
Sí. Para más información acerca de cómo utilizar la característica de carga de archivos .csv, vea [este ejemplo de PowerShell](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>¿Cómo puedo personalizar mis correos electrónicos de invitación?
Mediante las [API de invitación de B2B](active-directory-b2b-api.md) puede personalizar casi todos los elementos del proceso del invitador.

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>¿Puede un usuario externo invitado dejar la organización después de que se le haya invitado?
El administrador de la organización que invita puede eliminar un usuario invitado de colaboración B2B de su directorio, pero el usuario invitado no puede abandonar el directorio de la organización que invita por sí mismo. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>¿Pueden restablecer los usuarios invitados su método de Multi-Factor Authentication?
Sí. Los usuarios invitados pueden restablecer su método de Multi-Factor Authentication de la misma manera que los usuarios normales.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>¿Qué organización es la responsable de las licencias de Multi-Factor Authentication?
La organización que invita realiza la autenticación multifactor. La organización que invita debe asegurarse de que la organización tiene suficientes licencias para sus usuarios B2B que utilizan Multi-Factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>¿Qué ocurre si una organización asociada ya tiene Multi-Factor Authentication configurado? ¿Podemos confiar en su Multi-Factor Authentication y no utilizar nuestro propio Multi-Factor Authentication?
Esta característica está prevista para una versión futura, de modo que pueda seleccionar asociados específicos para excluir de su autenticación multifactor (la organización que invita).

### <a name="how-can-i-use-delayed-invitations"></a>¿Cómo se usan las invitaciones diferidas?
Algunas organizaciones pueden desear agregar usuarios de colaboración B2B, aprovisionarlos en las aplicaciones cuando sea necesario y, luego, enviar las invitaciones. Puede usar la API de invitación de colaboración B2B para personalizar el flujo de trabajo de incorporación.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>¿Puedo hacer que un usuario invitado sea un administrador limitado?
Totalmente. Para más información, consulte [Asignación de roles de administrador en la versión preliminar de Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>¿Permite la colaboración B2B de Azure AD que los usuarios B2B accedan a Azure Portal?
Salvo que a un usuario se le asigne el rol de administrador limitado o administrador global, los usuarios de colaboración B2B no necesitarán acceso a Azure Portal. Sin embargo, los usuarios de colaboración B2B a los que se asigna el rol de administrador limitado o administrador global pueden acceder al portal. Además, si un usuario invitado al que no se ha asignado estos roles de administrador accede al portal, podría acceder a determinadas partes de la experiencia. El rol de usuario invitado tiene algunos permisos en el directorio.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>¿Puedo bloquear el acceso a Azure Portal a los usuarios invitados?
Sí. Cuando configure esta directiva tenga cuidado de evitar que se bloquee accidentalmente el acceso a los administradores y miembros.
Para bloquear el acceso de un usuario invitado a [Azure Portal](https://portal.azure.com), use una directiva de acceso condicional en la API del modelo de implementación clásica de Azure:
1. Modifique el grupo **Todos los usuarios** para que contenga solo miembros.
  ![modificar la captura de pantalla del grupo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Cree un grupo dinámico que contenga usuarios invitados.
  ![crear captura de pantalla del grupo](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure una directiva de acceso condicional para bloquear el acceso de los usuarios invitados al portal, tal como se muestra en el siguiente vídeo:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>¿Admite la colaboración B2B de Azure AD admite la autenticación multifactor y las cuentas de correo electrónico de consumidor?
Sí. Tanto la autenticación multifactor como las cuentas de correo electrónico de consumidor admiten para la colaboración B2B de Azure AD.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>¿Planea admitir el restablecimiento de contraseñas para los usuarios de colaboración B2B de Azure AD?
Sí. Estos son los detalles importantes para el autoservicio de restablecimiento de contraseña (SSPR) para un usuario de B2B a quien invita una organización asociada:
 
* SSPR solo tiene lugar en el inquilino de identidad del usuario de B2B.
* Si el inquilino de identidad es una cuenta Microsoft, se usa el mecanismo SSPR de esta.
* Si el inquilino de identidad es un inquilino Just-In-Time (JIT) o "viral", se envía un correo electrónico de restablecimiento de la contraseña.
* En el caso de otros inquilinos, se sigue el proceso SSPR estándar para los usuarios de B2B. Como SSPR de miembro de los usuarios de B2B, en el contexto del recurso, el inquilino se bloquea. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>¿Está disponible el restablecimiento de contraseña para los usuarios invitados en un inquilino Just-In-Time (JIT) o "viral" que ha aceptado invitaciones con una dirección de correo electrónico laboral o académica, pero que no tiene una cuenta existente de Azure AD?
Sí. Se puede enviar un correo electrónico de restablecimiento de contraseña que permite que un usuario restablezca su contraseña en el inquilino JIT.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>¿Proporciona Microsoft Dynamics CRM compatibilidad en línea con la colaboración B2B de Azure AD?
En la actualidad, Microsoft Dynamics CRM no proporciona compatibilidad en línea con la colaboración B2B de Azure AD. Sin embargo, planeamos que sea compatible en el futuro.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>¿Cuál es la duración de una contraseña inicial para un usuario de colaboración B2B recién creado?
Azure AD tiene un conjunto fijo de requisitos de bloqueo de cuentas, seguridad de la contraseña y caracteres que se aplican igualmente a todas las cuentas de usuario en la nube de Azure AD. Las cuentas de usuario de nube son cuentas que no se federan con otro proveedor de identidades, como 
* Cuenta Microsoft
* Facebook
* Servicios de federación de Active Directory
* Otro inquilino de nube (para la colaboración B2B)

En el caso de las cuentas federadas, la directiva de contraseñas depende de la directiva que se aplica en el inquilino local y la configuración de la cuenta Microsoft del usuario.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Una organización puede tener distintas experiencias en sus aplicaciones para los usuarios inquilinos y los usuarios invitados. ¿Hay instrucciones estándar para esto? ¿La presencia de la notificación del proveedor de identidades es el modelo más adecuado para usarlo?
 Un usuario invitado puede utilizar cualquier proveedor de identidades para realizar la autenticación. Para más información, consulte [Propiedades de un usuario de colaboración B2B de Azure Active Directory](active-directory-b2b-user-properties.md). Use la propiedad **UserType** para determinar la experiencia del usuario. La notificación **UserType** no se incluye actualmente en el token. Las aplicaciones deben usar API Graph para consultar el usuario en el directorio y obtener UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>¿Dónde puedo encontrar una comunidad de colaboración B2B para compartir soluciones y enviar ideas?
Escuchamos constantemente sus comentarios para mejorar la colaboración B2B. Le invitamos a compartir los escenarios de usuario, los procedimientos recomendados y lo que le gusta de la colaboración B2B de Azure AD. Únase al debate en [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
También le invitamos a enviar sus ideas y a votar las características futuras en el sitio de [ideas para la colaboración B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>¿Podemos enviarle una invitación que se canjee automáticamente para que el usuario pueda empezar en cualquier momento? ¿O bien, el usuario siempre tiene que hacer clic para desplazarse a la dirección URL de canje?
Las invitaciones que envía un usuario de la organización que invita, que también es miembro de la organización asociada, no precisan que el usuario de B2B realice el canje.

Se recomienda invitar a un usuario de la organización asociada a unirse a la organización que invita. [Agregue este usuario al rol de invitador de usuarios en la organización de recursos](active-directory-b2b-add-guest-to-role.md). Este usuario puede invitar a otros usuarios de la organización asociada mediante la interfaz de usuario de inicio de sesión, los scripts de PowerShell o las API. De este modo, los usuarios de colaboración B2B de esa organización no necesitan volver a canjear sus invitaciones.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>¿Cómo funciona la colaboración B2B cuando el asociado invitado utiliza la federación para agregar su propia autenticación local?
Si el asociado tiene un inquilino de Azure AD que está federado en la infraestructura de autenticación local, se consigue automáticamente el inicio de sesión único (SSO) local. Si el asociado no tiene ningún inquilino de Azure AD, se crea una cuenta de Azure AD para los usuarios nuevos. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Creía que B2B de Azure AD no aceptaba direcciones de correo electrónico gmail.com y outlook.com, y que se usaba B2C para esos tipos de cuentas
Estamos eliminando las diferencias entre B2B y colaboración de negocio a la empresa (B2C) en cuanto a las identidades que se admiten. La identidad utilizada no es un buen motivo para decidir entre usar B2B o B2C. Para obtener información acerca de cómo elegir la opción de colaboración, consulte [Comparación de la colaboración B2B y B2C de Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>¿Qué aplicaciones y los servicios admiten los usuarios invitados de Azure B2B?
Todas las aplicaciones integradas en Azure AD admiten usuarios de invitados de B2B para Azure. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>¿Podemos forzar la autenticación multifactor para usuarios invitados de B2B si nuestros asociados no la tienen?
Sí. Puede encontrar información en [Acceso condicional para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>En SharePoint puede definir una lista de "permitidos" o "denegados" para usuarios externos. ¿Se puede hacer esto en Azure?
Sí. La colaboración B2B de Azure AD admite listas de permitidos y de denegados. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>¿Qué licencias se necesitan para usar B2B de Azure AD?
Para obtener información acerca de las licencias que necesita tener una organización para usar B2B de Azure AD, consulte [Guía de concesión de licencias de colaboración B2B de Azure Active Directory](active-directory-b2b-licensing.md).

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
