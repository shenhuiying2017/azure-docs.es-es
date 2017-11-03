---
title: "Configuración del inicio de sesión único federado para una aplicación ajena a la galería | Microsoft Docs"
description: "Cómo configurar un inicio de sesión único federado para una aplicación ajena a la galería que desee integrar con Azure AD"
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
ms.openlocfilehash: 781d06d2a8234d4cedf546ef059d49b7ece8783d
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Configuración del inicio de sesión único federado para una aplicación ajena a la galería

Para configurar un inicio de sesión único para una aplicación que no esté en la galería no *sin escribir código*, debe tener una suscripción o Azure AD Premium, y la aplicación debe ser compatible con SAML 2.0. Para más información acerca de las versiones de Azure AD, visite [Precios de Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Introducción a los pasos necesarios
A continuación se muestra información general sobre los pasos necesarios para configurar un inicio de sesión único federado con SAML 2.0 para una aplicación ajena a la galería (p. ej. una aplicación personalizada).

-   [Configuración de los valores de los metadatos de la aplicación en Azure AD (URL de inicio de sesión, identificador, URL de respuesta)](#_Configuring_single_sign-on)

-   [Selección del identificador de usuario e incorporación de los atributos de usuario para enviarlos a la aplicación](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperación de los metadatos y el certificado de Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configuración de los valores de los metadatos de Azure AD en la aplicación (URL de inicio de sesión, emisor, URL de cierre de sesión y certificado)](#_Configuring_single_sign-on)

-   [Asignación de usuarios a la aplicación](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configuración del inicio de sesión único en aplicaciones ajenas a la galería

Para configurar el inicio de sesión único para una aplicación ajena a la galería de Azure AD, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **Administrador global** o **Coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en el botón **Agregar** situado en la esquina superior derecha de la hoja **Aplicaciones empresariales**.

6.  En la sección **Agregar aplicación propia**, haga clic en **Aplicación situada fuera de la galería**

7.  Escriba el nombre de la aplicación en el cuadro de texto **Nombre**.

8.  Haga clic en el botón **Agregar** para agregar la aplicación.

9.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

10. En la lista desplegable **Modo**, seleccione **Inicio de sesión basado en SAML**.

11. Especifique los valores obligatorios en **Dominio y direcciones URL**. El proveedor de la aplicación le proporcionará estos valores.

   1. Para configurar la aplicación como SSO iniciado por el IdP, escriba la dirección URL de respuesta y el identificador.

   2. **Opcional:** para configurar la aplicación como SSO iniciado por el SP, la dirección URL de inicio de sesión es obligatoria.

12. En **Atributos de usuario**, seleccione el identificador único para los usuarios de la lista desplegable **Identificador de usuario**.

13. **Opcional:** haga clic en **Ver y editar todos los demás atributos de usuario** para editar los atributos que se enviarán a la aplicación en el token de SAML cuando el usuario inicie sesión.

   Para agregar un atributo:

   1. Haga clic en **Agregar atributo**. Escriba el **Nombre** y seleccione el **Valor** de la lista desplegable.

   2. Haga clic en **Guardar**. En la tabla verá el nuevo atributo.

14. Haga clic en **Configurar &lt;nombre de la aplicación&gt;** para acceder a documentación sobre cómo configurar el inicio de sesión único en la aplicación. Además, tiene las direcciones URL de Azure AD y los certificados necesarios para la aplicación.

15. [Asigne usuarios a la aplicación.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selección del identificador de usuario e incorporación de los atributos de usuario para enviarlos a la aplicación

Para seleccionar el identificador de usuario o agregar atributos de usuario, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **Administrador global** o **Coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación para la que ha configurado el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  En la sección **Atributos de usuario**, seleccione el identificador único para los usuarios de la lista desplegable **Identificador de usuario**. La opción seleccionada debe coincidir con el valor esperado de la aplicación para autenticar al usuario.

 >[!NOTA} AD Azure selecciona el formato del atributo NameID (Identificador de usuario) en función del valor seleccionado o del formato que solicite la aplicación en el elemento AuthRequest de SAML. Para más información, visite la sección NameIDPolicy del artículo [Protocolo SAML de inicio de sesión único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest).
 >
 >

9.  Para agregar atributos de usuario, haga clic en **Ver y editar todos los demás atributos de usuario** para editar los atributos que se enviarán a la aplicación en el token de SAML cuando el usuario inicie sesión.

   Para agregar un atributo:

   1. Haga clic en **Agregar atributo**. Escriba el **Nombre** y seleccione el **Valor** de la lista desplegable.

   2. Haga clic en **Guardar**. En la tabla verá el nuevo atributo.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Descarga del certificado o los metadatos de Azure AD

Para descargar el certificado o los metadatos de la aplicación de Azure AD, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **Administrador global** o **Coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación para la que ha configurado el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Vaya a la sección **Certificado de firma de SAML** y haga clic en el valor de la columna **Descargar**. Según lo que necesite la aplicación para configurar el inicio de sesión único, verá la opción para descargar el archivo XML de metadatos o el certificado.

Azure AD no proporciona una dirección URL para obtener los metadatos. Solo se pueden recuperar como un archivo XML.

## <a name="assign-users-to-the-application"></a>Asignación de usuarios a la aplicación

Para asignar uno o varios usuarios a una aplicación directamente, siga los pasos siguientes:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **Más servicios** en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea asignar a un usuario de la lista.

7.  Cuando se cargue la aplicación, haga clic en **Usuarios y grupos** desde el menú de navegación izquierdo de la aplicación.

8.  Haga clic en el botón **Agregar** en la parte superior de la lista **Usuarios y grupos** para abrir la hoja **Agregar asignación**.

9.  Haga clic en el selector **Usuarios y grupos** de la hoja **Agregar asignación**.

10. Escriba el **nombre completo** o la **dirección de correo electrónico** del usuario al que quiere asignar en el cuadro de búsqueda **Buscar por nombre o dirección de correo**.

11. Mantenga el puntero sobre el **usuario** en la lista para que aparezca una **casilla**. Haga clic en la casilla situada junto a la foto o el logotipo del perfil del usuario para agregar ese usuario a la lista de **seleccionados**.

12. **Opcional:** si desea **agregar más de un usuario**, escriba otro **nombre completo** o **dirección de correo electrónico** en el cuadro de búsqueda **Buscar por nombre o dirección de correo** y haga clic en la casilla para agregar ese usuario a la lista de **seleccionados**.

13. Cuando haya terminado de seleccionar usuarios, haga clic en el botón **Seleccionar** para agregarlos a la lista de usuarios y grupos que se asignarán a la aplicación.

14. **Opcional:** haga clic en el selector **Seleccionar rol** de la hoja **Agregar asignación** para seleccionar un rol que se asignará a los usuarios que ha seleccionado.

15. Haga clic en el botón **Asignar** para asignar la aplicación a los usuarios seleccionados.

Tras un breve período de tiempo, los usuarios seleccionados podrán iniciar estas aplicaciones mediante los métodos descritos en la sección de descripción de la solución.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalización de las notificaciones SAML que se han enviado a una aplicación

Para obtener información sobre cómo personalizar las notificaciones de atributo SAML que se han enviado a su aplicación, vea [Asignación de notificaciones en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)
