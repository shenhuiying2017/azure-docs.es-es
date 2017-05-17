---
title: "Introducción al panel de acceso | Microsoft Docs"
description: "Aprenda a usar las variaciones del Panel de acceso (explorador web, aplicación Android, aplicación iPhone y iPad) para tener acceso a las aplicaciones SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.contentlocale: es-es
ms.lasthandoff: 04/06/2017


---
# <a name="what-is-the-access-panel"></a>¿Qué es el Panel de acceso?
El Panel de acceso es un portal basado en la Web. Permite que un usuario con una cuenta profesional o educativa en Azure Active Directory (Azure AD) para ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD le concedió acceso. Un usuario que posee ediciones de Azure AD también puede usar las funcionalidades de administración de grupos de autoservicio a través del Panel de acceso.

El Panel de acceso es independiente de Azure Portal y no requiere que los usuarios tengan una suscripción de Azure.

![Panel de acceso][1]

El Panel de acceso permite que los usuarios editen parte de la configuración de su perfil, incluida la capacidad para:

* Cambiar la contraseña asociada a una cuenta profesional o educativa.
* Editar la configuración de restablecimiento de contraseña.
* Editar la configuración de contacto y preferencias relacionada con la autenticación multifactor (en el caso de las cuentas para las que un administrador requirió su uso).
* Ver detalles de la cuenta, como el id. de usuario, el correo electrónico alternativo y los números de teléfono de la oficina y de móvil.
* Ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD les concedió acceso. Para más información sobre el Panel de acceso desde la perspectiva de los usuarios, consulte [Utilización del Panel de acceso](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* Autoadministrar grupos. Más concretamente, el administrador puede crear y administrar grupos de seguridad y solicitar pertenencias a grupos de seguridad en Azure AD. Para más información, consulte [Configuración de Azure Active Directory para la administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md) y [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md).

## <a name="accessing-the-access-panel"></a>Acceso al Panel de acceso
Los usuarios pueden acceder al Panel de acceso visitando la siguiente dirección URL en un explorador web:  <br>
**http://myapps.microsoft.com**

Si tiene la configuración de marca personalizada en su página de inicio de sesión, puede cargar esta personalización de marca de forma predeterminada anexando el dominio de su organización al final de la dirección URL:  <br>
**http://myapps.microsoft.com/contosobuild.com**

En este caso, puede usar cualquier nombre de dominio activo o comprobado que se haya configurado en la pestaña **Dominios** del directorio en Azure Portal, tal como se muestra en la captura de pantalla siguiente:

![Nombre de dominio de Wingtip Toys][2]  

Esta dirección URL se debe distribuir a todos los usuarios que iniciarán sesión en las aplicaciones integradas con Azure AD.

## <a name="authentication"></a>Autenticación
Para llegar al Panel de acceso, un usuario se debe autenticar con una cuenta profesional o educativa en Azure AD. Un usuario se puede autenticar en Azure AD directamente. Además, si una organización configuró la federación mediante Servicios de federación de Active Directory (AD FS) u otras tecnologías, Windows Server Active Directory puede autenticar a los usuarios.

Si un usuario tiene una suscripción para Azure u Office 365 y ha usado Azure Portal o una aplicación de Office 365, verá la lista de aplicaciones sin necesidad de volver a iniciar sesión. A los usuarios que no se han autenticado se les solicitará que inicien sesión utilizando el nombre de usuario y la contraseña de su cuenta en Azure AD. Si la organización configuró la federación, bastará con escribir el nombre de usuario.

Después de la autenticación, los usuarios pueden interactuar con las aplicaciones que el administrador integró con el directorio. Para obtener información sobre cómo integrar las aplicaciones con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="web-browser-requirements"></a>Requisitos del explorador web
Como mínimo, el Panel de acceso requiere un explorador que admita JavaScript y que tenga CSS habilitado. Para que el usuario inicie sesión en aplicaciones mediante el inicio de sesión único (SSO) basado en contraseña, debe instalarse la extensión del Panel de acceso en el explorador del usuario. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

La extensión de panel de acceso actualmente está disponible para los exploradores Internet Explorer 8 y posteriores, Edge, Chrome y Firefox.

## <a name="mobile-app-support"></a>Compatibilidad para aplicación móvil
El equipo de Azure Active Directory publica la aplicación móvil Mis aplicaciones. Cuando los usuarios instalan esta aplicación, pueden iniciar sesión en aplicaciones SSO basadas en contraseña en dispositivos iOS y Android.

> [!NOTE]
> Los usuarios pueden iniciar sesión en aplicaciones que admitan federación con Azure AD (como Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 y otras 70 más) prácticamente en cualquier explorador web, en cualquier dispositivo, sin necesidad de instalar complementos o una aplicación móvil. Tampoco necesitará usar la aplicación móvil Mis aplicaciones en un dispositivo móvil para disfrutar del resto de la [experiencia del Panel de acceso](https://myapps.microsoft.com/).
>
>

### <a name="my-apps-for-android"></a>Mis aplicaciones para Android
Mis aplicaciones para Android es compatible con cualquier dispositivo Android que ejecute la versión 4.1 o posterior de Android. Está disponible actualmente en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Pantalla de Mis aplicaciones para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Mis aplicaciones para iPhone e iPad
Mis aplicaciones para iOS es compatible con cualquier dispositivo iPhone o iPad que ejecute la versión 7 o posterior de iOS. Está disponible actualmente en Apple App Store.

![Pantalla de Mis aplicaciones para iOS][4]    

## <a name="tips-for-testing-the-user-experience"></a>Sugerencias para probar la experiencia del usuario
Si es un administrador de Azure e inició sesión en Azure Portal con una cuenta en el directorio, inició sesión automáticamente en el Panel de acceso como la cuenta de administrador actual. En este caso, puede ver todas las aplicaciones que se han asignado a esta cuenta.

Para probar con una cuenta de usuario *diferente*:

1. Haga clic en el menú de usuario en la esquina superior derecha de Azure Portal o del Panel de acceso y seleccione **Cerrar sesión**. De este modo se cierra la sesión de Azure AD.
2. Vaya al [Panel de acceso](http://myapps.microsoft.com).
3. En la página de inicio de sesión, escriba el nombre de usuario y la contraseña de la cuenta del directorio que desea probar.

## <a name="starting-applications"></a>Inicio de aplicaciones
Pueden aparecer varios tipos de aplicaciones en el Panel de acceso.

### <a name="office-365-applications"></a>Aplicaciones de Office 365
Si una organización usa aplicaciones de Office 365 y el usuario dispone de licencia para ellas, esas aplicaciones aparecen en el Panel de acceso del usuario.

Cuando un usuario hace clic en un icono de aplicación para una aplicación de Office 365, se redirige a esa aplicación e inicia sesión automáticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicaciones de Microsoft y de terceros configuradas con SSO basado en federación
El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único de Azure AD**. Un usuario ve estas aplicaciones solo si el administrador concedió explícitamente acceso a las aplicaciones a ese usuario.

Cuando un usuario hace clic en el icono de una de estas aplicaciones, se le redirige a esa aplicación e inicia sesión automáticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO basado en contraseña sin aprovisionamiento de identidad
El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único basado en contraseña**. Todos los usuarios del directorio ven todas las aplicaciones que se han configurado en este modo.

La primera vez que un usuario haga clic en el icono de una de estas aplicaciones, se le pedirá que instale el complemento SSO de contraseña para Internet Explorer o Chrome. La instalación podría necesitar que el usuario reinicie el explorador web. Cuando el usuario vuelve al Panel de acceso y vuelve a hacer clic en el icono de la aplicación, se le pide un nombre de usuario y contraseña para la aplicación. Una vez que el usuario escribe el nombre de usuario y la contraseña, estas credenciales se almacenan de forma segura en Azure AD y se vinculan con su cuenta en Azure AD.

La próxima vez que el usuario haga clic en el icono de la aplicación, iniciará sesión automáticamente en la aplicación. No será necesario que vuelva a escribir las credenciales ni instalar nuevamente el complemento SSO de contraseña.

Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. Para actualizar las credenciales, el usuario selecciona el icono de la aplicación y, luego, selecciona **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de esa aplicación.

### <a name="password-based-sso-with-identity-provisioning"></a>SSO basado en contraseña con aprovisionamiento de identidad
El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único basado en contraseña**, además del aprovisionamiento de identidad.

La primera vez que un usuario haga clic en el icono de una de estas aplicaciones, se le pedirá que instale el complemento SSO de contraseña para Internet Explorer o Chrome. La instalación podría necesitar que el usuario reinicie el explorador web. Cuando vuelva al Panel de acceso y haga clic nuevamente en el icono de aplicación, iniciará sesión automáticamente en la aplicación.

Algunas aplicaciones pueden requerir que el usuario cambie su contraseña en el primer inicio de sesión. Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. Para actualizar las credenciales, el usuario selecciona el icono de la aplicación y, luego, selecciona **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de esa aplicación.

### <a name="application-with-existing-sso-solutions"></a>Aplicación con soluciones SSO existentes
Para configurar SSO en una aplicación, Azure Portal proporciona una tercera opción, **Inicio de sesión único existente**. Esta opción permite que el administrador cree un vínculo a una aplicación y lo coloque en el Panel de acceso de los usuarios seleccionados.

Por ejemplo, si hay una aplicación configurada para autenticar usuarios mediante AD FS 2.0, el administrador puede usar la opción **Inicio de sesión único existente** y crearle un vínculo en el Panel de acceso. Cuando los usuarios tienen acceso al vínculo, se autentican mediante AD FS 2.0 o cualquier solución SSO existente que brinde la aplicación.

## <a name="related-articles"></a>Artículos relacionados
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)
* [Introducción al inicio de sesión único y la administración del acceso a las aplicaciones con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

