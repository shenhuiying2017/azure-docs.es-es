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
ms.openlocfilehash: c9069cb0b46ddc1155c64bd63a7fcd8a685abbad
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-access-panel"></a>¿Qué es el panel de acceso?

El panel de acceso es un portal web. Si tiene una cuenta profesional o educativa en Azure Active Directory (Azure AD), puede utilizar el panel de acceso para ver e iniciar las aplicaciones basadas en la nube a las que el administrador de Azure AD le ha concedido acceso. También se pueden usar las funcionalidades de administración de grupos de autoservicio y aplicaciones a través del panel de acceso.

El panel de acceso es independiente de Azure Portal. No es necesario tener una suscripción a Azure.

![Panel de acceso][1] Mediante el panel de acceso, podrá editar algunas opciones de configuración del perfil y realizar las operaciones siguientes:

- Cambiar la contraseña asociada a una cuenta profesional o educativa.

- Editar la configuración de restablecimiento de contraseña.

- Editar la configuración de contacto y preferencias relacionada con la autenticación multifactor (en el caso de las cuentas para las que un administrador requirió su uso).

- Consultar los datos de la cuenta, como el identificador de usuario, el correo electrónico alternativo, los números de teléfono móvil y del trabajo, y los dispositivos.

- Ver e iniciar las aplicaciones basadas en la nube a las que el administrador de Azure AD le haya concedido acceso. 

- Autoadministrar grupos. Los administradores pueden crear y administrar grupos de seguridad, además de solicitar la pertenencia a grupos de seguridad en Azure AD. Para más información, consulte [Configuración de Azure Active Directory para la administración de grupos de autoservicio](active-directory-accessmanagement-self-service-group-management.md) y [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Acceso al panel de acceso

Para acceder al panel de acceso, vaya a `http://myapps.microsoft.com`.

Si la página de inicio de sesión está configurada con su marca personalizada, puede cargar la personalización de marca agregando el dominio de la organización a la dirección URL (por ejemplo, `http://myapps.microsoft.com/<your domain>.com`).

Puede utilizar cualquier nombre de dominio activo o verificado que se haya configurado en Azure Portal, tal y como se muestra aquí:

![Nombre de dominio de Wingtip Toys][2]  

Distribuya la dirección URL a todos los usuarios que inicien sesión en las aplicaciones integradas con Azure AD.

## <a name="authentication"></a>Autenticación

Para llegar al panel de acceso, hay que autenticarse con una cuenta profesional o educativa en Azure AD. Un usuario se puede autenticar en Azure AD directamente. Además, si una organización configuró la federación mediante Servicios de federación de Active Directory (AD FS) u otras tecnologías, Windows Server Active Directory puede autenticar a los usuarios.

Si tiene una suscripción de Azure u Office 365 y ha usado Azure Portal o una aplicación de Office 365, podrá consultar la lista de aplicaciones sin necesidad de iniciar sesión de nuevo. Si no está autenticado, se le pedirá que inicie sesión con el nombre de usuario y la contraseña de la cuenta de Azure AD. Si la organización configuró la federación, bastará con escribir el nombre de usuario.

Después de la autenticación, los usuarios pueden interactuar con las aplicaciones que el administrador integró con el directorio. Para obtener información sobre cómo integrar las aplicaciones con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="web-browser-requirements"></a>Requisitos del explorador web

Como mínimo, el panel de acceso requiere un explorador que admita JavaScript y que tenga CSS habilitado. Para iniciar sesión en las aplicaciones con el inicio de sesión único (SSO) basado en contraseña, debe instalar una extensión del panel de acceso en el explorador. Esta extensión se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

La extensión del panel de acceso está actualmente disponible para:
- **Edge**: en Windows 10 Anniversary Edition, o cualquier versión posterior. 
- **Chrome**: en Windows 7 o versiones posteriores y en MacOS X o versiones posteriores.
- **Firefox 26.0 o versiones posteriores**: en Windows XP SP2 o versiones posteriores y en Mac OS X 10.6 o versiones posteriores.
- **Internet Explorer 8, 9, 10, 11**: en Windows 7, o cualquier versión posterior (compatibilidad limitada).

## <a name="my-apps-secure-sign-in-extension"></a>Extensión de inicio de sesión seguro de Mis aplicaciones
Para poder iniciar sesión con el inicio de sesión único basado en contraseña, debe disponer de esta extensión. Después de instalar la extensión, puede seleccionar **Iniciar sesión para comenzar**. 

- Puede iniciar sesión directamente en una aplicación utilizando la **URL de inicio de sesión**. Cuando se usa la dirección URL de la aplicación, la extensión detecta la operación y permite iniciar sesión desde la propia extensión.
- Puede iniciar cualquier aplicación desde el panel de acceso utilizando la característica *Búsqueda rápida* de la extensión. 
- En la extensión, en la sección **Usados recientemente**, aparecen las tres últimas aplicaciones que se han iniciado.

> [!NOTE]
> Hay características adicionales disponibles solo para Edge, Chrome y Firefox.
>

Si va utilizar una dirección URL de Mis aplicaciones que no sea `https://myapps.microsoft.com`, configure la dirección URL predeterminada siguiendo estos pasos:
1. *Sin* iniciar sesión en la extensión, haga clic con el botón derecho en el icono de la extensión.
2. En el menú, seleccione **My Apps URL** (URL de Mis aplicaciones).
3. Seleccione la dirección URL predeterminada.
4. Seleccione el icono de la extensión.
5. Seleccione **Iniciar sesión para comenzar**.

## <a name="mobile-app-support"></a>Compatibilidad para aplicación móvil

El equipo de Azure Active Directory publica la aplicación móvil Mis aplicaciones. Cuando los usuarios instalan esta aplicación, pueden iniciar sesión en aplicaciones SSO basadas en contraseña en dispositivos iOS y Android.

> [!NOTE]
> Los usuarios pueden iniciar sesión en aplicaciones que admitan la federación con Azure AD (como Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 y otras 70 más) prácticamente en cualquier explorador web, en cualquier dispositivo, sin necesidad de instalar complementos o una aplicación móvil. Tampoco necesitará la aplicación móvil My Apps para disfrutar de las demás [funcionalidades del panel de acceso](https://myapps.microsoft.com/) en un dispositivo móvil.
>
>

### <a name="my-apps-for-android"></a>Mis aplicaciones para Android

My Apps para Android es compatible con cualquier dispositivo Android que ejecute Android 4.1 o una versión posterior.  

Está disponible en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Mis aplicaciones para Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Mis aplicaciones para iPhone e iPad

My Apps para iOS es compatible con cualquier dispositivo iPhone o iPad que ejecute iOS 7 o una versión posterior.  

Está disponible en el [App Store de Apple](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![My Apps para iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Managed Browser para My Apps

My Apps también puede integrarse con Intune Managed Browser. La solución Intune Managed Browser para dispositivos iOS y Android desempeña un papel clave a la hora de garantizar que los datos de los dispositivos móviles se mantengan seguros. El explorador permite ver y explorar con seguridad páginas web que podrían contener información de la compañía, además de proporcionar una experiencia de exploración web segura.  

Puede encontrar un acceso rápido a My Apps en la página principal de Managed Browser y en los marcadores; de este modo, necesitará menos clics para acceder a cualquier aplicación.

Intune Managed Browser está disponible en el [App Store de Apple ](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) y en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Managed Browser para My Apps][5]    


## <a name="tips-for-testing-the-user-experience"></a>Sugerencias para probar la experiencia del usuario

Los administradores de Azure que hayan iniciado sesión en Azure Portal con una cuenta en el directorio iniciarán la sesión automáticamente en el panel de acceso con la cuenta de administrador actual. En esta vista, se muestran todas las aplicaciones que tiene asignadas.

Para probar con una cuenta de usuario *diferente*, siga estos pasos:

1. En la parte superior derecha de Azure Portal o del panel de acceso, seleccione **Cerrar sesión**. 
2. Vaya al [panel de acceso](http://myapps.microsoft.com).
3. En la página de inicio de sesión, escriba el nombre de usuario y la contraseña de la cuenta del directorio que desea probar.


## <a name="starting-applications"></a>Inicio de aplicaciones

En esta sección, vamos a explicar varios tipos de aplicaciones que pueden aparecer en el panel de acceso.

### <a name="office-365-applications"></a>Aplicaciones de Office 365

Si una organización usa aplicaciones de Office 365 y el usuario dispone de licencia para ellas, esas aplicaciones aparecen en el panel de acceso del usuario.

Cuando seleccione el icono de una aplicación de Office 365, se le redirigirá a dicha aplicación y la sesión se iniciará automáticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplicaciones de Microsoft y de terceros configuradas con SSO basado en federación

El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único de Azure AD**. Solo podrá ver estas aplicaciones si el administrador la ha concedido acceso expresamente.

Cuando seleccione el icono de una aplicación, se le redirigirá a dicha aplicación y la sesión se iniciará automáticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>SSO basado en contraseña sin aprovisionamiento de identidad

El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo de SSO establecido en **Inicio de sesión único basado en contraseña**. Todos los usuarios del directorio ven todas las aplicaciones que se han configurado en este modo.

La primera vez que seleccione el icono de una aplicación, se le pedirá que instale el complemento de inicio de sesión único con contraseña para Internet Explorer o Chrome. La instalación podría requerir que el usuario reinicie el explorador web. Cuando el usuario vuelve al panel de acceso y selecciona de nuevo el icono de la aplicación, se le pide el nombre de usuario y la contraseña de dicha aplicación. Una vez que escribe el nombre de usuario y la contraseña, estas credenciales se almacenan de forma segura y se vinculan con su cuenta en Azure AD.

La próxima vez que el usuario seleccione el icono de la aplicación, iniciará sesión automáticamente en la aplicación.  

No tendrá que volver a escribir las credenciales ni instalar otra vez el complemento SSO de contraseña.

Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. 

Para actualizar las credenciales, siga estos pasos:

1. Seleccione el icono en el icono de la aplicación.
2. Seleccione **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de la aplicación.


### <a name="password-based-sso-with-identity-provisioning"></a>SSO basado en contraseña con aprovisionamiento de identidad

El administrador puede agregar aplicaciones en la sección Active Directory de Azure Portal con el modo SSO establecido en **Inicio de sesión único basado en contraseña**, además del aprovisionamiento de identidad.

La primera vez que seleccione el icono de una aplicación, se le pedirá que instale el complemento de inicio de sesión único con contraseña para Internet Explorer o Chrome. La instalación podría requerir que el usuario reinicie el explorador web.  

Cuando vuelva al panel de acceso y seleccione otra vez el icono de la aplicación, iniciará sesión automáticamente en ella.

Algunas aplicaciones pueden pedirle que cambie la contraseña al iniciar sesión por primera vez. Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, este también debe actualizar sus credenciales almacenadas en Azure AD. 

Para actualizar las credenciales, siga estos pasos:

1. Seleccione el icono en el icono de la aplicación.
2. Seleccione **Actualizar credenciales** para volver a escribir el nombre de usuario y la contraseña de la aplicación.


### <a name="application-with-existing-sso-solutions"></a>Aplicación con soluciones SSO existentes

Para configurar SSO en una aplicación, Azure Portal cuenta con una tercera opción llamada Inicio de sesión único existente. Esta opción permite que el administrador cree un vínculo a una aplicación y lo coloque en el panel de acceso de los usuarios seleccionados.

Por ejemplo, si una aplicación está configurada de modo que puede autenticar usuarios con AD FS 2.0, el administrador puede usar la opción Inicio de sesión único existente para crear un vínculo en el panel de acceso. Cuando los usuarios tienen acceso al vínculo, se autentican mediante AD FS 2.0 o cualquier solución SSO existente que brinde la aplicación.


## <a name="next-steps"></a>pasos siguientes

- Para ver una lista de todos los temas relacionados con la administración de aplicaciones, consulte el [índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md).
 
- Si quiere aprender a integrar una aplicación SaaS en Azure AD, consulte la [lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md).
 
- En el caso de que necesite más detalles sobre cómo administrar aplicaciones con Azure AD, consulte [Introducción al inicio de sesión único y la administración del acceso a las aplicaciones con Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Para obtener más información sobre el aprovisionamiento de usuarios, vea [Aprovisionamiento y desaprovisionamiento automático de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
