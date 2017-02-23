---
title: "Configuración de aplicaciones de SaaS para la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Ejemplos de código y PowerShell para la colaboración B2B de Azure Active Directory"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configuración de aplicaciones de SaaS para la colaboración B2B

La colaboración B2B de Active Directory (Azure AD) de Azure funciona con la mayoría de las aplicaciones que se integran con Azure AD. En esta sección, veremos las instrucciones sobre cómo configurar algunas aplicaciones populares de SAS para usarse con Azure AD B2B.
Antes de adentrarnos en las instrucciones concretas de la aplicación, estas son algunas reglas generales:

* Recuerde que, para la mayoría de las aplicaciones, el aprovisionamiento de usuarios debe producirse manualmente (es decir, los usuarios deben crearse de forma manual en la aplicación).

* Para las aplicaciones que admiten el aprovisionamiento (por ejemplo, Dropbox) automático, se crearán invitaciones aparte a partir de las aplicaciones. El usuario debe estar seguro de aceptar cada invitación.

* En los atributos de usuario, siempre establezca el identificador de usuario en user.mail (para mitigar los problemas con UPD alterados en usuarios invitados).


##<a name="dropbox-for-business"></a>Dropbox para Empresas

Para que los usuarios puedan iniciar sesión con su cuenta de organización, se debe configurar manualmente Dropbox para Empresas para usar Azure AD como proveedor de identidades SAML. Dropbox para Empresas no puede solicitar o, en caso contrario, permitir a los usuarios que inicien sesión con Azure AD si no se ha configurado para ello.

1. Agregue la aplicación de Dropbox para Empresas en Azure AD como se muestra en la captura de pantalla.

  ![Incorporación de Azure AD a Dropbox](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![Incorporación de Azure AD a Dropbox](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. Configure la aplicación.

  ![Configuración del inicio de sesión único de la aplicación](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Seleccione la configuración de inicio de sesión único y cambie el identificador de usuario en user.mail (su UPN de forma predeterminada)

4. Descargue el certificado que se va a usar para la configuración de Dropbox.

  ![Descarga del certificado](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Use la opción de configurar Dropbox (el proceso se explica con detalle en las capturas de pantalla siguientes).

6. Obtenga el inicio de sesión único de SAML en la URL que se usó en la configuración.

7. Obtenga la URL de inicio de sesión desde la página de configuración de Dropbox.

  ![Inicio de sesión en Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![Menú de Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![Cuadro de diálogo de autenticación de Dropbox contraído](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![Cuadro de diálogo de autenticación de Dropbox expandido](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. Cargue el certificado y pegue la URL de inicio de sesión único de SAML aquí.

  ![Pegado de la URL de SSO de SAML](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Configure el aprovisionamiento automático de usuarios en Azure Portal.

  ![Configuración del aprovisionamiento automático de usuarios](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. Una vez aprovisionados en la aplicación de Dropbox, el usuario invitado o miembro obtendrá una invitación independiente de Dropbox. Los invitados deben aceptarla haciendo clic en el vínculo para usar el inicio de sesión único en Dropbox.

## <a name="box"></a>Box
En esta sección se describe cómo habilitar usuarios para que se autentiquen usuarios invitados en Box con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario cargar metadatos en Box.com.

1. Incorporación de Box desde las aplicaciones empresariales

2. Configurar inicio de sesión único

  ![Configuración del inicio de sesión único de Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. En primer lugar, asegúrese de que la URL de inicio de sesión está establecida correctamente para Box en el Portal de administración de Azure. Esta es la URL de su inquilino de Box.com y debe seguir el formato: https://.box.com.

4. Debe tener en cuenta que el identificador no sirve para esta aplicación, pero todavía se muestra como campo obligatorio.

5. Identificador de usuario en user.mail (asegúrese de que se use SSO en las cuentas de invitado)

6. Creación de un certificado SAML

7. Para empezar a configurar el inquilino de Box.com con el fin de usar Azure Active Directory como proveedor de identidades, empiece descargando el siguiente archivo de metadatos y guardándolo localmente en el equipo: Descarga del archivo de metadatos (asegúrese de que esté activo)

8. Reenvíe el archivo de metadatos al equipo de soporte técnico de Box. El equipo de soporte técnico configura un inicio de sesión único para usted.

9. Configure el aprovisionamiento automático de usuarios de Azure AD.

  ![Autorización a Azure AD para conectarse a Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Los invitados también deben canjear su invitación en la aplicación de Box.

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propiedades de usuario de la colaboración B2B](active-directory-b2b-user-properties.md)
* [Incorporación de usuarios de colaboración B2B a un rol](active-directory-b2b-add-guest-to-role.md)
* [Delegación de las invitaciones de colaboración B2B](active-directory-b2b-delegate-invitations.md)
* [Grupos dinámicos y colaboración B2B](active-directory-b2b-dynamic-groups.md)
* [Código de colaboración B2B y ejemplos de PowerShell](active-directory-b2b-code-samples.md)
* [Tokens de usuario de colaboración B2B](active-directory-b2b-user-token.md)
* [Asignación de notificaciones de usuario de colaboración B2B](active-directory-b2b-claims-mapping.md)
* [Uso compartido externo de Office 365](active-directory-b2b-o365-external-user.md)
* [Limitaciones actuales de la colaboración B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


