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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Integración del inicio de sesión único de Azure Active Directory con aplicaciones existentes

## Información general

La configuración de inicio de sesión único (SSO) para una aplicación de su organización ya usa un proceso diferente al de crear nuevas cuentas para una nueva aplicación. Hay un par de pasos preliminares entre los que se incluyen: la asignación de identidades de usuario de la aplicación para las identidades de Azure Active Directory (AD) y la comprensión de cómo los usuarios experimentarán el inicio de sesión en una aplicación una vez que está integrada.

> [AZURE.NOTE]Para configurar el inicio de sesión único para una aplicación existente, debe tener derechos de administrador global tanto en Azure AD como en la aplicación de SaaS.

## Asignación de cuentas de usuario

Normalmente, la identidad de un usuario tiene un identificador único que podría ser una dirección de correo electrónico o un nombre universal personal (UPN). Tendrá que vincular (asignar) la identidad de la aplicación de cada usuario a sus respectivas identidades de Azure AD. Hay dos maneras de lograrlo en función del requisito de autenticación de la aplicación.

Para obtener más información sobre la vinculación de identidades de la aplicación con identidades de Azure AD, consulte [Personalización de notificaciones emitidas en el token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) y [Personalización de asignaciones de atributos para aprovisionamiento](active-directory-saas-customizing-attribute-mappings.md).

## Descripción de la experiencia de inicio de sesión del usuario

Al integrar el inicio de sesión único para una aplicación que ya está en uso, es importante tener en cuenta que la experiencia del usuario se verá afectada. Para todas las aplicaciones, los usuarios comenzarán a usar sus credenciales de Azure AD para iniciar sesión. Además, pueden tener que usar otro portal para acceder a las aplicaciones.

El inicio de sesión único para algunas aplicaciones puede realizarse en la interfaz de inicio de sesión de la aplicación, pero para otras aplicaciones el usuario tendrá que obtener acceso a un portal de la aplicación central ([Mis aplicaciones](http://myapps.microsoft.com) u [Office365](http://portal.office.com/myapps)) para iniciar sesión. Obtenga más información acerca de los diferentes tipos de inicio de sesión único y sus experiencias de usuario en [Qué es acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Vea también *Supresión del consentimiento del usuario* en el artículo [Guiar a los desarrolladores](active-directory-applications-guiding-developers-for-lob-applications.md).

## Artículos relacionados
vínculos para la supresión del consentimiento del usuario y otros subartículos para guiar a los desarrolladores

<!---HONumber=Oct15_HO4-->