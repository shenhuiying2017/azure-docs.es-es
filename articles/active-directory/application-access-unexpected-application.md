---
title: "Aplicación inesperada en mi lista de aplicaciones | Microsoft Docs"
description: "Cómo ver todas las aplicaciones de su inquilino y comprender cómo aparecen estas en la lista Todas las aplicaciones en Aplicaciones empresariales"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4765b71714e88ee91cb9938ad4bb34033cf12422
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="unexpected-application-in-my-applications-list"></a>Aplicación inesperada en mi lista de aplicaciones

Este artículo le ayudará comprender cómo aparecen las aplicaciones en la lista **Todas las aplicaciones** en **Aplicaciones empresariales**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Visualización de todas las aplicaciones del inquilino

Para ver todas las aplicaciones de su inquilino, debe usar el **Filtro** control para que aparezcan **todas las aplicaciones** en la lista **Todas las aplicaciones**. Para ello, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

6.  Haga clic en el control **Filtro** en la parte superior de la **lista Todas las aplicaciones**.

7.  En la hoja **Filtrar**, establezca la opción **Mostrar** en **Todas las aplicaciones**.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>¿Por qué aparece una aplicación específica en mi lista de todas las aplicaciones?

Cuando se filtra por **todas las aplicaciones**, la **lista** **Todas las aplicaciones** muestra todos los objetos de entidad de servicio del inquilino. Los objetos de entidad de servicio pueden aparecer en esta lista de varias formas:

1.  Cuando agrega cualquier aplicación desde la galería de aplicaciones, incluidas:

   1. **Aplicaciones de la Galería de Azure AD**: una aplicación que se ha integrado previamente para el inicio de sesión único con Azure AD.

   2. **Aplicaciones de proxy de aplicación**: una aplicación que se ejecuta en su entorno local en la que quiere que se proporcione inicio de sesión seguro externamente.

   3. **Aplicaciones personalizadas**: una aplicación que su organización desea desarrollar en la plataforma de desarrollo de aplicaciones de Azure AD, pero que no existe aún.

   4. **Aplicaciones que no son de la Galería**: ¡traiga sus propias aplicaciones! Cualquier vínculo web, o cualquier aplicación que represente un campo de nombre de usuario y contraseña, admita los protocolos SAML u OpenID Connect, o admita SCIM, que quiera integrar para el inicio de sesión único con Azure AD.

2.  Al registrarse o iniciar sesión en una aplicación de <sup>terceros</sup> integrada con Azure Active Directory. Un ejemplo de esto es [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).

3.  Al registrarse o agregar una licencia a un usuario o un grupo para una aplicación original de Microsoft, como puede ser [Microsoft Office 365](http://products.office.com/).

4.  Al agregar un nuevo registro de aplicación mediante la creación de una aplicación desarrollada de forma personalizada con el [Registro de aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration).

5.  Al agregar un nuevo registro de aplicación mediante la creación de una aplicación desarrollada de forma personalizada con el [Portal de registro de aplicaciones V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration#visit-the-microsoft-app-registration-portal).

6.  Al agregar una aplicación que está desarrollando con los [métodos de autenticación de ASP.net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) o los [servicios conectados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) de Visual Studio.

7.  Al crear un objeto de entidad de servicio mediante el [módulo de PowerShell de Azure AD](/powershell/azure/install-adv2?view=azureadps-2.0).

8.  Al [dar su consentimiento a una aplicación](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) como administrador para utilizar datos de su inquilino.

9.  Cuando un [usuario da su consentimiento a una aplicación](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) para utilizar datos de su inquilino.

10. Al habilitar determinados servicios que almacenan datos en el inquilino. Un ejemplo de esto es Password Reset, que se modela como entidad de servicio para almacenar la directiva de restablecimiento de contraseña de forma segura.

Para obtener más detalles sobre cómo las aplicaciones se agregan a su directorio, lea [Cómo y por qué se agregan aplicaciones a Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Deseo quitar la asignación de un usuario o grupo específicos a una aplicación

Para quitar la asignación de un usuario o grupo a una aplicación, siga los pasos indicados en el artículo [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal).

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Quiero deshabilitar el acceso a una aplicación para todos los usuarios

Para deshabilitar todos los inicios de sesión de usuarios a una aplicación, siga los pasos indicados en el artículo [Deshabilitación de los inicios de sesión de usuario de una aplicación empresarial en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal).

## <a name="i-want-to-delete-an-application-entirely"></a>Quiero eliminar una aplicación por completo

Para **eliminar una aplicación**, siga las instrucciones siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve que la aplicación que desea aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea eliminar.

7.  Una vez que se carga la aplicación, haga clic en el icono **Eliminar** de la hoja **Introducción** de la aplicación situada en la parte superior.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Quiero deshabilitar todas las operaciones de consentimiento de usuario futuras para todas las aplicaciones

Deshabilitar el consentimiento del usuario para todo el directorio impide que los usuarios finales den consentimiento a cualquier aplicación. A pesar de ello, los administradores podrán seguir dando el consentimiento en nombre de los usuarios. Para más información sobre el consentimiento de aplicación y los motivos por los que es posible que quiera o no quiera hacer esto, lea el artículo [Descripción del consentimiento de usuario y administrador](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).

Para **deshabilitar todas las operaciones de consentimiento de usuario futuras en todo el directorio**, siga las instrucciones siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **Más servicios** en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Usuarios y grupos** en el menú de navegación.

5.  Haga clic en **Configuración de usuario**.

6.  Deshabilite todas las operaciones de consentimiento de usuario futuras estableciendo la opción **Los usuarios pueden permitir que las aplicaciones accedan a sus datos** en **No**. Después, haga clic en el botón **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
[Administración de aplicaciones con Azure Active Directory](active-directory-enable-sso-scenario.md)
