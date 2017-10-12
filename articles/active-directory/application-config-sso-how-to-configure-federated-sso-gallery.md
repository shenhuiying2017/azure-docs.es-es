---
title: "Configuración del inicio de sesión único federado para una aplicación de la galería de Azure AD | Microsoft Docs"
description: "Cómo configurar un inicio de sesión único federado para una aplicación existente de la galería de Azure AD y usar los tutoriales para empezar a trabajar rápidamente"
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
ms.openlocfilehash: 1b1d00718981b2c7d11f5b88428d02e16dd0b34d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Configuración del inicio de sesión único federado para una aplicación de la galería de Azure AD

Todas las aplicaciones de la galería de Azure AD habilitadas con funcionalidad de Enterprise Single Sign-On tienen un tutorial paso a paso disponible. Para instrucciones detalladas paso a paso, acceda a la [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).

## <a name="overview-of-steps-required"></a>Introducción a los pasos necesarios
Para configurar una aplicación desde la galería de Azure AD, realice los siguientes pasos:

-   [Incorporación de una aplicación desde la galería de Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Configuración de los valores de los metadatos de la aplicación en Azure AD (URL de inicio de sesión, identificador, URL de respuesta)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Selección del identificador de usuario e incorporación de los atributos de usuario para enviarlos a la aplicación](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperación de los metadatos y el certificado de Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Configuración de los valores de los metadatos de Azure AD en la aplicación (URL de inicio de sesión, emisor, URL de cierre de sesión y certificado)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Asignación de usuarios a la aplicación](#assign-users-to-the-application)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Incorporación de una aplicación desde la galería de Azure AD

Para agregar una aplicación desde la galería de Azure AD, siga estos pasos:

1.  Abra [Azure Portal](https://portal.azure.com) e inicie sesión como **administrador global** o **coadministrador**

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en el botón **Agregar** situado en la esquina superior derecha de la hoja **Aplicaciones empresariales**.

6.  En el cuadro de texto **Escriba un nombre** de la sección **Agregar desde la galería**, escriba el nombre de la aplicación.

7.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

8.  Antes de agregar la aplicación, puede cambiar su nombre en el cuadro de texto **Nombre**.

9.  Haga clic en el botón **Agregar** para agregar la aplicación.

Tras un breve período, podrá ver la hoja de configuración de la aplicación.

## <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Configuración del inicio de sesión único para una aplicación de la galería de Azure AD

Para configurar el inicio de sesión único para una aplicación, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **Administrador global** o **Coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  En la lista desplegable **Modo**, seleccione **Inicio de sesión basado en SAML**.

9.  Especifique los valores obligatorios en **Dominio y direcciones URL**. El proveedor de la aplicación le proporcionará estos valores.

   1. Para configurar la aplicación como SSO iniciado por el SP, la dirección URL de inicio de sesión es obligatoria. En algunas aplicaciones, el identificador también es obligatorio.

   2. Para configurar la aplicación como SSO iniciado por el IdP, la dirección URL de respuesta es obligatoria. En algunas aplicaciones, el identificador también es obligatorio.

10. **Opcional:** haga clic en **Mostrar configuración avanzada de URL** si desea ver los valores opcionales.

11. En **Atributos de usuario**, seleccione el identificador único para los usuarios de la lista desplegable **Identificador de usuario**.

12. **Opcional:** haga clic en **Ver y editar todos los demás atributos de usuario** para editar los atributos que se enviarán a la aplicación en el token de SAML cuando el usuario inicie sesión.

  Para agregar un atributo:
   
   1. Haga clic en **Agregar atributo**. Escriba el **Nombre** y seleccione el **Valor** de la lista desplegable.

   1. Haga clic en **Guardar**. En la tabla verá el nuevo atributo.

13. Haga clic en **Configurar &lt;nombre de la aplicación&gt;** para acceder a documentación sobre cómo configurar el inicio de sesión único en la aplicación. Además, tiene las direcciones URL de los metadatos y el certificado necesario para la instalación de SSO con la aplicación.

14. Para guardar la configuración, haga clic en **Guardar**.

15. Asigne usuarios a la aplicación.

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

  >[!NOTE] 
  >AD Azure selecciona el formato del atributo NameID (Identificador de usuario) en función del valor seleccionado o del formato que solicite la aplicación en el elemento AuthRequest de SAML. Para más información, visite la sección NameIDPolicy del artículo [Protocolo SAML de inicio de sesión único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest).
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

  *  Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones**.

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



