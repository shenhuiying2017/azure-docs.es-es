---
title: "¿Qué es el panel de acceso de Azure Active Directory? | Microsoft Docs"
description: "Aprenda a usar las variaciones del panel de acceso (explorador web, aplicación Android, y aplicación iPhone y iPad) para tener acceso a las aplicaciones SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6b5c139766af9e166b12e8833c2ced8be08e743a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="what-is-the-access-panel"></a>¿Qué es el panel de acceso?

El panel de acceso es un portal web. Permite que un usuario con una cuenta profesional o educativa en Azure Active Directory (Azure AD) vea e inicie aplicaciones basadas en la nube a las que el administrador de Azure AD le concedió acceso. También se pueden usar las funcionalidades de administración de grupos de autoservicio y aplicaciones a través del panel de acceso.

El panel de acceso es independiente de Azure Portal y no requiere que los usuarios tengan una suscripción de Azure.

![Panel de acceso][1]

El panel de acceso permite que los usuarios editen parte de la configuración de su perfil, por ejemplo, para lo siguiente:

- Cambiar la contraseña asociada a una cuenta profesional o educativa.

- Editar configuración de restablecimiento de contraseña

- Editar la configuración de contacto y preferencias relacionada con la autenticación multifactor (en el caso de las cuentas para las que un administrador requirió su uso)

- Ver detalles de la cuenta, como el identificador de usuario, el correo electrónico alternativo, y los números de teléfono móvil y del trabajo.

- Ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD les concedió acceso. Para obtener más información sobre el panel de acceso desde la perspectiva de los usuarios, consulte Utilización del panel de acceso. 

- Autoadministrar grupos. Más concretamente, el administrador puede crear y administrar grupos de seguridad y solicitar pertenencias a grupos de seguridad en Azure AD. Para más información, consulte [Configuración de Azure Active Directory para la administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md) y [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>Acceso al panel de acceso

Los usuarios pueden acceder al panel de acceso visitando la siguiente dirección URL en un explorador web: `http://myapps.microsoft.com`.

Con la configuración de marca personalizada en la página de inicio de sesión, se puede cargar esta personalización de marca de forma predeterminada anexando el dominio de su organización al final de la dirección URL: `http://myapps.microsoft.com/<your domain>.com`.

En este caso, se puede utilizar cualquier nombre de dominio activo o comprobado que se haya configurado en Azure Portal.

![Nombre de dominio de Wingtip Toys][2]  

La dirección URL se debe distribuir a todos los usuarios que iniciarán sesión en las aplicaciones integradas con Azure AD.

## <a name="authentication"></a>Autenticación

Para llegar al panel de acceso, hay que autenticarse con una cuenta profesional o educativa en Azure AD. Un usuario se puede autenticar en Azure AD directamente. Además, si una organización configuró la federación mediante Servicios de federación de Active Directory (AD FS) u otras tecnologías, Windows Server Active Directory puede autenticar a los usuarios.

Si un usuario tiene una suscripción para Azure u Office 365 y ha usado Azure Portal o una aplicación de Office 365, verá la lista de aplicaciones sin necesidad de volver a iniciar sesión. A los usuarios que no se han autenticado se les solicitará que inicien sesión con el nombre de usuario y la contraseña de su cuenta en Azure AD. Si la organización configuró la federación, bastará con escribir el nombre de usuario.

Después de la autenticación, los usuarios pueden interactuar con las aplicaciones que el administrador integró con el directorio. Para obtener información sobre cómo integrar las aplicaciones con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="web-browser-requirements"></a>Requisitos del explorador web

Como mínimo, el panel de acceso requiere un explorador que admita JavaScript y que tenga CSS habilitado. Para que el usuario inicie sesión en aplicaciones mediante el inicio de sesión único (SSO) basado en contraseña, debe instalarse la extensión del panel de acceso en el explorador. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

La extensión del panel de acceso está actualmente disponible para:
-   Edge en Windows 10 Anniversary Edition o posterior 

-   Chrome (en Windows 7 o posterior y en Mac OS X o posterior)

-   Firefox 26.0 o posterior (en Windows XP SP2 o posterior y en Mac OS X 10.6 o posterior)

-   Internet Explorer 8, 9, 10, 11 en Windows 7 o posterior (compatibilidad limitada)

## <a name="my-apps-secure-sign-in-extension"></a>Extensión de inicio de sesión seguro de Mis aplicaciones
La extensión es necesaria para que los usuarios inicien sesión con contraseña de inicio de sesión único. Una vez instalada, los usuarios también pueden habilitar características adicionales al iniciar sesión en la extensión, al hacer clic en **Iniciar sesión para comenzar**. 

- Los usuarios pueden iniciar sesión en las aplicaciones al visitar su **dirección URL de inicio de sesión**. Cuando el usuario va a la dirección URL de inicio de sesión de la aplicación, la extensión lo detecta y proporciona la opción de iniciar sesión de allí.
- Los usuarios también pueden iniciar cualquiera de sus aplicaciones desde el panel de acceso con la característica de **búsqueda rápida** de la extensión. 
- La extensión también mostrará a los usuarios las tres últimas aplicaciones en las que iniciaron sesión en la sección **Usados recientemente**.

> [!NOTE]
> Hay características adicionales disponibles solo para Edge, Chrome y Firefox.
>
>

Si está utilizando una dirección URL de Mis aplicaciones diferente de https://myapps.microsoft.com, debe seguir estos pasos para configurar la dirección URL predeterminada:
1. Sin iniciar sesión en la extensión, **haga clic con el botón derecho** en el icono de la extensión.
2. Haga clic en **Select My Apps URL**  (Seleccionar dirección URL de Mis aplicaciones) en el menú.
3. **Seleccione** la dirección URL predeterminada.
4. Haga clic en el icono de la extensión.
5. Inicie sesión en la extensión seleccionando **Iniciar sesión para comenzar**.

## <a name="mobile-app-support"></a>Compatibilidad para aplicación móvil

El equipo de Azure Active Directory publica la aplicación móvil Mis aplicaciones. Cuando los usuarios instalan esta aplicación, pueden iniciar sesión en aplicaciones SSO basadas en contraseña en dispositivos iOS y Android.

> [!NOTE]
> Los usuarios pueden iniciar sesión en aplicaciones que admitan la federación con Azure AD (como Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 y otras 70 más) prácticamente en cualquier explorador web, en cualquier dispositivo, sin necesidad de instalar complementos o una aplicación móvil. Tampoco necesitará usar la aplicación móvil Mis aplicaciones en un dispositivo móvil para disfrutar del resto de la [experiencia del panel de acceso](https://myapps.microsoft.com/).
>
>

### <a name="my-apps-for-android"></a>Mis aplicaciones para Android

Mis aplicaciones para Android es compatible con cualquier dispositivo Android que ejecute la versión 4.1 o posterior de Android.  
Está disponible actualmente en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Mis aplicaciones para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Mis aplicaciones para iPhone y iPad

Mis aplicaciones para iOS es compatible con cualquier dispositivo iPhone o iPad que ejecute la versión 7 o posterior de iOS.  
Está disponible actualmente en el [App Store de Apple](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Mis aplicaciones para iOS][4]    



## <a name="managed-browser-for-my-apps"></a>Explorador administrado para Mis aplicaciones

Mis aplicaciones también se integra en Intune Managed Browser. La solución Intune Managed Browser para dispositivos iOS y Android desempeña un rol clave a la hora de garantizar que los datos de los dispositivos móviles se mantengan seguros. Asimismo, permite ver y explorar páginas web que podrían contener información de la compañía, además de proporcionar una experiencia de exploración web segura.  
Puede encontrar un acceso rápido a Mis aplicaciones en la página principal de Managed Browser y en los marcadores; de este modo, llegará más rápido a cualquier aplicación a la que quiera acceder.

Está disponible en el [App Store de Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) y en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Explorador administrado para Mis aplicaciones][5]    





## <a name="tips-for-testing-the-user-experience"></a>Sugerencias para probar la experiencia del usuario

Los administradores de Azure que hayan iniciado sesión en Azure Portal con una cuenta en el directorio iniciarán la sesión automáticamente en el panel de acceso con la cuenta de administrador actual. En este caso, puede ver todas las aplicaciones que se han asignado a esta cuenta.

**Para probar con *otra* cuenta de usuario, siga estos pasos:**

1. Haga clic en el menú de usuario de la esquina superior derecha de Azure Portal o del panel de acceso; luego, seleccione **Cerrar sesión**. 
2. Vaya al [panel de acceso](http://myapps.microsoft.com).
3. En la página de inicio de sesión, escriba el nombre de usuario y la contraseña de la cuenta del directorio que desea probar.


## <a name="starting-applications"></a>Inicio de aplicaciones

Pueden aparecer varios tipos de aplicaciones en el panel de acceso.

### <a name="office-365-applications"></a>Aplicaciones de Office 365

Si una organización usa aplicaciones de Office 365 y el usuario dispone de licencia para ellas, esas aplicaciones aparecen en el panel de acceso del usuario.

Cuando un usuario hace clic en un icono de una aplicación de Office 365, se redirige a esa aplicación e inicia sesión automáticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicaciones de Microsoft y de terceros configuradas con SSO basado en federación

El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único de Azure AD**. Un usuario ve estas aplicaciones solo si el administrador concedió expresamente acceso a las aplicaciones a ese usuario.

Cuando un usuario hace clic en el icono de una de estas aplicaciones, se le redirige a esa aplicación e inicia sesión automáticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO basado en contraseña sin aprovisionamiento de identidad

El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único basado en contraseña**. Todos los usuarios del directorio ven todas las aplicaciones que se han configurado en este modo.

La primera vez que un usuario haga clic en el icono de una de estas aplicaciones, se le pedirá que instale el complemento SSO de contraseña para Internet Explorer o Chrome. La instalación podría requerir que el usuario reinicie el explorador web. Cuando el usuario vuelve al panel de acceso y hace clic de nuevo en el icono de la aplicación, se le pide el nombre de usuario y la contraseña de dicha aplicación. Una vez que escribe el nombre de usuario y la contraseña, estas credenciales se almacenan de forma segura en Azure AD y se vinculan con su cuenta en Azure AD.

La próxima vez que el usuario haga clic en el icono de la aplicación, iniciará sesión automáticamente en la aplicación.  
No tendrá que volver a escribir las credenciales ni instalar otra vez el complemento SSO de contraseña.

Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. 

**Para actualizar las credenciales, siga estos pasos:**

1. Seleccione el icono en el icono de la aplicación.
2. Seleccione **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de la aplicación.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO basado en contraseña con aprovisionamiento de identidad

El administrador puede agregar aplicaciones en la sección **Active Directory** de Azure Portal con el modo de SSO establecido en **Inicio de sesión único basado en contraseña**, además del aprovisionamiento de identidad.

La primera vez que un usuario haga clic en el icono de una de estas aplicaciones, se le pedirá que instale el **complemento SSO de contraseña para Internet Explorer o Chrome**. La instalación podría requerir que el usuario reinicie el explorador web.  
Cuando vuelva al panel de acceso y haga clic otra vez en el icono de la aplicación, iniciará sesión automáticamente en ella.

Algunas aplicaciones pueden requerir que el usuario cambie su contraseña en el primer inicio de sesión. Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, este también debe actualizar sus credenciales almacenadas en Azure AD. 

**Para actualizar las credenciales, siga estos pasos:**

1. Seleccione el icono en el icono de la aplicación.
2. Seleccione **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de la aplicación.


### <a name="application-with-existing-sso-solutions"></a>Aplicación con soluciones SSO existentes

Para configurar SSO en una aplicación, Azure Portal proporciona una tercera opción: **Inicio de sesión único existente**. Esta opción permite que el administrador cree un vínculo a una aplicación y lo coloque en el panel de acceso de los usuarios seleccionados.

Por ejemplo, si hay una aplicación configurada para autenticar usuarios mediante AD FS 2.0, el administrador puede usar la opción **Inicio de sesión único existente** y crearle un vínculo en el panel de acceso. Cuando los usuarios tienen acceso al vínculo, se autentican mediante AD FS 2.0 o cualquier solución SSO existente que brinde la aplicación.


## <a name="next-steps"></a>pasos siguientes

- Para ver una lista de todos los temas relacionados con la administración de aplicaciones, consulte [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md).
 
- Si quiere saber cómo integrar una aplicación SaaS en Azure AD, vea [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md).
 
- En el caso de que necesite más detalles sobre cómo administrar aplicaciones con Azure AD, consulte [Introducción al inicio de sesión único y la administración del acceso a las aplicaciones con Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Para obtener más información sobre el aprovisionamiento de usuarios, vea [Aprovisionamiento y desaprovisionamiento automático de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
