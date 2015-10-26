<properties
   pageTitle="Integración del inicio de sesión único de Azure Active Directory con aplicaciones recientemente adquiridas | Microsoft Azure"
   description="Azure Active Directory admite el inicio de sesión único para aplicaciones de SaaS recientemente adquiridas para habilitar la administración de acceso centralizado en el Portal de Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Integración del inicio de sesión único de Azure Active Directory con aplicaciones recientemente adquiridas  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Para empezar a configurar el inicio de sesión único para una aplicación que lleva a su organización, va a usar un directorio existente en Azure Active Directory. Puede usar un directorio de Azure AD que obtenga a través de Microsoft Azure, Office 365 o Windows Intune. Si dispone de dos o más de estas aplicaciones, consulte [Administración del directorio de Azure AD](active-directory-administer.md) para determinar cuál de ellas usar.

## Otras consideraciones

### Autenticación

Para las aplicaciones que admiten SAML 2.0, WS-Federation, o protocolos OpenID Connect, usa Azure Active Directory usa la firma de certificados para establecer relaciones de confianza. Para obtener más información, consulte [Administración de certificados para inicio de sesión único federado](active-directory-sso-certs.md).

Para las aplicaciones que admiten únicamente el inicio de sesión basado en formularios HTML, Azure Active Directory utiliza el 'almacén de contraseñas' para establecer relaciones de confianza. Permite a los usuarios de su organización iniciar la sesión automáticamente en una aplicación SaaS de terceros en Azure AD, usando la información de la cuenta del usuario de la aplicación SaaS. Azure AD recopila y almacena de forma segura la información de la cuenta del usuario y la contraseña correspondiente. Para obtener más información, consulte [Inicio de sesión único con contraseña](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Autorización

Una cuenta aprovisionada permite al usuario tener autorización para usar una aplicación, después de haberse autenticado a través de un inicio de sesión único. El aprovisionamiento de usuarios puede realizarse manualmente o, en algunos casos, puede agregar y quitar la información de usuario de la aplicación de SaaS basándose en los cambios realizados en Azure Active Directory. Para obtener más información sobre el uso de conectores de Azure AD existentes para el aprovisionamiento automatizado, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

De lo contrario, puede agregar manualmente la información de usuario a una aplicación o usar otras soluciones de aprovisionamiento que están disponibles en Marketplace.

### Access

Azure AD proporciona varias maneras personalizables para implementar aplicaciones para los usuarios finales de su organización: No se bloquea en cualquier implementación concreta o solución de acceso. Puede usar [la solución que mejor se adapte a sus necesidades](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Pasos siguientes

Para las aplicaciones de SaaS que ya están en la organización y que desea habilitar para inicio de sesión único, consulte [Integración del inicio de sesión único de Azure Active Directory con aplicaciones existentes ](active-directory-sso-integrate-existing-apps.md)

Para las aplicaciones de SaaS que encuentre en la galería de aplicaciones, Azure Active Directory proporciona una serie de [tutoriales sobre cómo integrar las aplicaciones SaaS](active-directory-saas-tutorial-list.md).

Si la aplicación no está en la galería de aplicaciones, puede [agregarla a la galería de aplicaciones de Azure Active Directory como una aplicación personalizada](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Hay mucho más detalle en todos estos problemas en la biblioteca de Azure.com, a partir de [Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->