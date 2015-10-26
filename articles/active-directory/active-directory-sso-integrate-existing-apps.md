<properties
   pageTitle="Integración del inicio de sesión único de Azure Active Directory con aplicaciones existentes | Microsoft Azure"
   description="Administre las aplicaciones de SaaS que ya usa habilitando la autenticación de inicio de sesión único y el aprovisionamiento de usuarios en Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Integración del inicio de sesión único de Azure Active Directory con aplicaciones existentes

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Otras consideraciones

La configuración del inicio de sesión único para las aplicaciones que ya usa es un proceso diferente al de crear nuevas cuentas en nuevas aplicaciones. Los administradores y usuarios experimentarán un cambio cuando la aplicación se integra en Azure AD.

### ¿Qué tienen que saber los administradores para configurar el inicio de sesión único para las aplicaciones que ya está en uso?

Para configurar el inicio de sesión único para una aplicación existente, debe tener derechos de administrador global tanto en Azure AD como en la aplicación de SaaS.

Puesto que la aplicación ya está en uso, deberá vincular las identidades de aplicación establecidas para el usuario con sus respectivas identidades de Azure AD. Es importante saber qué usa esta aplicación como identificador único para el inicio de sesión, ya sea una dirección de correo electrónico, el nombre personal universal (UPN) o el nombre de usuario. Se vinculará al identificador único del usuario en Azure AD. Para obtener más información acerca de la vinculación de identidades de la aplicación con identidades de Azure AD, consulte [Personalización de notificaciones emitidas en el token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) y [Personalización de asignaciones de atributos para aprovisionamiento](active-directory-saas-customizing-attribute-mappings.md).

### ¿Cómo afecta todo esto a los usuarios finales?

Al integrar el inicio de sesión único para una aplicación que ya está en uso, es importante tener en cuenta que la experiencia del usuario se verá afectada. Para todas las aplicaciones, los usuarios comenzarán a usar sus credenciales de Azure AD para iniciar sesión. Además, pueden tener que usar otro portal para acceder a las aplicaciones. El inicio de sesión único para algunas aplicaciones puede realizarse en el propio sitio web de la aplicación, pero para otras aplicaciones el usuario tendrá que acceder a un portal de la aplicación central ([Mis aplicaciones](myapps.microsoft.com) o [Office365](portal.office.com/myapps)) para iniciar sesión. Obtenga más información acerca de los diferentes tipos de inicio de sesión único y sus experiencias de usuario en [Qué es acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Pasos siguientes
- Consulte como [integrar el inicio de sesión único de Azure Active Directory con aplicaciones recientemente adquiridas](active-directory-sso-newly-acquired-saas-apps.md).
- Obtenga más información sobre [el acceso a aplicaciones e inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- Encuentre [tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md).
-	Agregue una aplicación personalizada con la [configuración de Self-Service SAML para Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->