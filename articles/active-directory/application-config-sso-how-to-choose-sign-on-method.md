---
title: "Cómo determinar el método de inicio de sesión único que se debe usar | Microsoft Docs"
description: "Conozca los modos de inicio de sesión único que se admiten en Azure AD y cómo seleccionar uno para la aplicación que le interesa."
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 80f4a965920fec9cb578c1bee235c7857f37431e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Cómo determinar el método de inicio de sesión único que se debe usar

Este artículo le ayuda a entender los modos de inicio de sesión único que se admiten en Azure AD y a seleccionar uno para la aplicación que le interesa.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modos de inicio de sesión único y de aprovisionamiento admitidos por tipos de aplicaciones específicas

En la tabla siguiente se describen los diferentes modos de inicio de sesión único y aprovisionamiento admitidos por cada uno de los tipos de aplicaciones anteriores. Puede usar esta tabla para ayudarle a comprender qué aplicación debe agregar para admitir un objetivo específico.

  ![Tabla de tipos de aplicaciones](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Cómo elegir un modo de inicio de sesión único

Los modos de **inicio de sesión único** admitidos para aplicaciones de Azure AD se enumeran a continuación.

-   **Se desactivó el inicio de sesión único de Azure AD.**: elija el **modo de inicio de sesión único** Se desactivó el inicio de sesión único de Azure AD, si aún no está preparado para integrar esta aplicación con el inicio de sesión único de Azure AD, o simplemente la está probando.

-   **Inicio de sesión vinculado**: elija el **modo de inicio de sesión**[Inicio de sesión vinculado](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si tiene una aplicación que ya está conectada con una solución de inicio de sesión único existente, o si solo desea publicar un simple vínculo para sus usuarios en su [Panel de acceso a las aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) o en su [selector de aplicaciones de Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none).

-   **Inicio de sesión basado en contraseña**: elija el **modo de inicio de sesión único**[Inicio de sesión con contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si su aplicación representa un campo de nombre de usuario y contraseña HTML y quiere almacenar ese nombre de usuario y contraseña de forma segura para reproducirlos en la aplicación más adelante.

-   **Inicio de sesión basado en SAML**: elija el modo de inicio de sesión único [Inicio de sesión basado en SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) si su aplicación admite los protocolos SAML o OpenID Connect, o si desea poder asignar usuarios a roles de aplicación específicos en función de reglas definidas en sus notificaciones SAML *(**Nota:**Esta opción no está disponible cuando el servidor proxy de aplicación está configurado para una aplicación)*.

-   **Inicio de sesión basado en el encabezado**: elija el modo de inicio de sesión único [Inicio de sesión basado en el encabezado](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) si tiene una aplicación que usa PingAccess que admite la autenticación basada en encabezados HTTP y en la que quiere realizar el inicio de sesión único *(**Nota:** Esta opción solo está disponible cuando el servidor proxy de aplicación y PingAccess están configurados para una aplicación)*.

-   **Autenticación de Windows integrada**: elija el modo de inicio de sesión único [Autenticación de Windows integrada](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) al exponer una aplicación WIA local en la que quiere realizar el inicio de sesión único *(**Nota:** Esta opción solo está disponible cuando el servidor proxy de aplicación está configurado para una aplicación)*.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de inicio de sesión únicos para aplicaciones personalizadas

Las aplicaciones que ha desarrollado de forma personalizada mediante la experiencia de [aplicación de desarrollo personalizado](#_Custom-Developed_Applications) también admiten modos de inicio de sesión único adicionales que no se indican en la lista anterior. Entre ellos se incluyen los siguientes:

-   Inicio de sesión basado en [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

-   Inicio de sesión basado en [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

-   Inicio de sesión basado en [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

-   Inicio de sesión basado en [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Lea la [guía del desarrollador de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para aprender a crear una aplicación de desarrollo personalizado que admite estos modos de inicio de sesión único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Cómo establecer el modo de inicio de sesión único de una aplicación

Para establecer el modo de **inicio de sesión único** de una aplicación, siga las instrucciones siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)

