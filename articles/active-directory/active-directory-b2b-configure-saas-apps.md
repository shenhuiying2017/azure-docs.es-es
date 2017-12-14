---
title: "Configuración de aplicaciones de SaaS para la colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Ejemplos de código y PowerShell para la colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
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
ms.openlocfilehash: dc0bb3950f30b2ea676de406538e3a463ad21429
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Configuración de aplicaciones de SaaS para la colaboración B2B

La colaboración de B2B de Azure Active Directory (Azure AD) funciona con la mayoría de las aplicaciones que se integran con Azure AD. En esta sección, se proporcionan las instrucciones necesarias para configurar varias aplicaciones populares de SAS para usarlas con B2B de Azure AD.

Antes de examinar las instrucciones específicas de la aplicación, estas son algunas reglas generales:

* En la mayoría de las aplicaciones, la instalación del usuario se debe realizar de forma manual. Es decir, los usuarios deben crearse de forma manual también en la aplicación.

* En el caso de las aplicaciones que admiten la instalación automática, como Dropbox, se crean invitaciones independientes desde las aplicaciones. Los usuarios debe asegurarse de aceptar cada invitación.

* En los atributos del usuario, para mitigar cualquier problema de alteración del disco de perfil de usuario (UPD) en usuarios invitados, en **Identificador de usuario**, seleccione siempre **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Para permitir que los usuarios inicien sesión con su cuenta de organización, es preciso configurar manualmente Dropbox Business para que use Azure AD como proveedor de identidades del lenguaje de marcado de aserción de seguridad (SAML). Si Dropbox Business no se ha configurado para ello, no puede solicitar o, en caso contrario, permitir a los usuarios que inicien sesión con Azure AD.

1. Para agregar la aplicación Dropbox Business a Azure AD, seleccione **Aplicaciones empresariales** en el panel izquierdo y, después, haga clic en **Agregar**.

  ![El botón "Agregar" de la página Aplicaciones empresariales](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. En la ventana **Agregar una aplicación**, escriba **dropbox** en el cuadro de búsqueda y seleccione **Dropbox for Business** en la lista de resultados.

  ![Busque "dropbox" en la página Agregar una aplicación](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. En la página **Inicio de sesión único**, seleccione **Inicio de sesión único** en el panel izquierdo y, después, escriba **user.mail** en el cuando **Identificador de usuario** (de manera predeterminada se establece como UPN).

  ![Configuración del inicio de sesión único para la aplicación](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. Para descargar el certificado que se va a usar para la configuración de Dropbox, seleccione **Configurar DropBox**y, después, seleccione **SAML Single Sign On Service URL** (URL del servicio de inicio de sesión único de SAML) en la lista.

  ![Descarga del certificado para la configuración de Dropbox](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Inicie sesión en Dropbox con la URL de inicio de sesión desde la página **Inicio de sesión único**.

  ![La página de inicio de sesión de Dropbox](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. En el menú, seleccione **Admin Console** (Consola de administración).

  ![El vínculo "Admin Console" (Consola de administración) en el menú de Dropbox](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. En el cuadro de diálogo **Authentication** (Autenticación), seleccione **More** (Más), cargue el certificado y, después, en el cuadro **Sign in URL** (URL de inicio de sesión), escriba la URL de inicio de sesión único de SAML.

  ![El vínculo "More" (Más) en el cuadro de diálogo Authentication (Autenticación) contraído](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![El vínculo "Sign in URL" (URL de inicio de sesión) en el cuadro de diálogo Authentication (Autenticación) expandido](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Para configurar la instalación automática del usuario en Azure Portal, seleccione **Aprovisionamiento** en el panel izquierdo, después **Automático** en el cuadro **Modo de aprovisionamiento** y, finalmente, seleccione **Autorizar**.

  ![Configuración del aprovisionamiento automático de usuarios en Azure Portal](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

Una vez que los usuarios miembros o invitados se hayan configurado en la aplicación de Dropbox, reciben una invitación independiente de Dropbox. Para usar el inicio de sesión único de Dropbox, los invitados deben aceptar dicha invitación haciendo clic en un vínculo de ella.

## <a name="box"></a>Box
Puede permitir que los usuarios autentiquen usuarios invitados de Box con su cuenta de Azure AD mediante el uso de una federación basada en el protocolo SAML. En este procedimiento, se cargan metadatos en Box.com.

1. Agregue la aplicación Box desde las aplicaciones empresariales.

2. Configure el inicio de sesión único en el orden siguiente:

  ![Configuración del inicio de sesión único de Box](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. En el cuadro **URL de inicio de sesión**, asegúrese de que la dirección URL de inicio de sesión se ha establecido correctamente para Box en Azure Portal. Esta es la dirección URL de su inquilino de Box.com. Debe seguir la convención de nomenclatura *https://.box.com*.  
 **Identificador** no se aplica a esta aplicación, pero sigue apareciendo como campo obligatorio.

 b. En el cuadro **Identificador de usuario**, escriba **user.mail** (para el SSO de las cuentas de invitado).

 c. En **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

 d. Para empezar a configurar el inquilino de Box.com para que use Azure AD como proveedor de identidades, descargue el archivo de metadatos y guárdelo en una unidad local.

 e. Reenvíe el archivo de metadatos al equipo de soporte técnico de Box para que configuren el inicio de sesión único.

3. Para la instalación automática de usuarios de Azure AD, en el panel izquierdo, seleccione **Aprovisionamiento**y, después, **Autorizar**.

  ![Autorizar a Azure AD para conectarse a Box](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Los invitados de Box deben canjear su invitación desde la aplicación de Box tal como lo hacen los de Dropbox.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos sobre la colaboración de B2B de Azure AD:

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
