---
title: "Configuración del inicio de sesión único con contraseña para una aplicación ajena a la galería | Microsoft Docs"
description: "Configuración de una aplicación personalizada ajena para el inicio de sesión único con contraseña cuando no aparece en la Galería de aplicaciones de Azure AD"
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
ms.openlocfilehash: f629ec99824199306ebf825901beaa99d83d434d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Configuración del inicio de sesión único con contraseña para una aplicación ajena a la galería

Además de las opciones que se encuentran en la Galería de aplicaciones de Azure AD, también puede agregar una **aplicación ajena a la galería** cuando la aplicación que desea no aparece ahí. Con esta funcionalidad, puede agregar cualquier aplicación que ya exista en su organización, o bien cualquier aplicación de terceros que puedan usar desde un proveedor que aún no forme parte de la [Galería de aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

Una vez agregada una aplicación ajena a la galería, puede configurar el método de inicio de sesión único que usa esta aplicación seleccionando el elemento de navegación **Inicio de sesión único** en una aplicación de empresa en [Azure Portal](https://portal.azure.com/).

Uno de los métodos de inicio de sesión único que tiene a su disposición es la opción [Inicio de sesión único basado en contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work). Con la experiencia de **agregar una aplicación ajena a la galería**, puede integrar cualquier aplicación que represente un nombre de usuario basado en HTML y el campo de contraseña de entrada, incluso si no está en nuestro conjunto de aplicaciones previamente integradas.

El modo de funcionamiento es mediante una tecnología de barrido de páginas que forma parte de la extensión Access Panel que permiten la detección automática de los campos de entrada de nombre de usuario y contraseña, almacénelos de forma segura para la instancia de la aplicación específica. A continuación, reproduzca de forma segura los nombres de usuario y contraseñas en esos campos cuando un usuario navegue a esa aplicación en el panel de acceso de la aplicación.

Representa una excelente manera de empezar a integrar cualquier tipo de aplicación en Azure AD rápidamente, y permite lo siguiente:

-   Integrar **cualquier aplicación del mundo** con el inquilino de Azure AD, siempre y cuando represente un campo de entrada de usuario y contraseña HTML.

-   Habilitar el **inicio de sesión único para los usuarios** almacenando y reproduciendo de forma segura los nombres de usuario y contraseñas para la aplicación que ha integrado con Azure AD.

-   **Detectar automáticamente los campos de entrada** de cualquier aplicación y detectarlos manualmente con la extensión del explorador Access Panel, en caso de que la detección automática no los encuentre.

-   **Admitir aplicaciones que requieren varios campos de inicio de sesión** para aplicaciones que no solo requieren los campos de nombre de usuario y contraseña para iniciar sesión.

-   **Personalizar las etiquetas** de los campos de entrada de nombre de usuario y contraseña que los usuarios ven en el [panel de acceso de la aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) cuando escriben sus credenciales

-   Permitir a los **usuarios** proporcionar sus propios nombres de usuario y contraseñas para las cuentas de aplicación existentes que están escribiendo manualmente en el [panel de acceso de la aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que un **miembro del grupo de negocios** especifique los nombres de usuario y contraseñas que se asignan a un usuario mediante el uso de la característica [Acceso de autoservicio a las aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Permitir que un **administrador** especifique los nombres de usuario y contraseñas que se asignan a un usuario mediante la característica Actualizar credenciales al [asignar un usuario a una aplicación](#_How_to_configure_1)

-   Permitir que un **administrador** especifique el nombre de usuario o la contraseña compartidos que utilizan un grupo de personas mediante la característica Actualizar credenciales al [asignar un grupo a una aplicación](#assign-an-application-to-a-group-directly).

A continuación se describe cómo habilitar el [inicio de sesión único con contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) en una aplicación que agregue mediante la experiencia de **agregar una aplicación ajena a la galería**.

## <a name="overview-of-steps-required"></a>Información general sobre los pasos necesarios

Para configurar una aplicación desde la galería de Azure AD, necesita seguir estos pasos:

-   [Incorporación de una aplicación ajena a la galería](#add-a-non-gallery-application)

-   [Configuración de la aplicación para el inicio de sesión único con contraseña](#configure-the-application-for-password-single-sign-on)

-   [Asignar la aplicación a un usuario o un grupo](#assign-the-application-to-a-user-or-a-group)

    -   [Asignar un usuario a una aplicación directamente](#assign-a-user-to-an-application-directly)

    -   [Asignación de una aplicación a un grupo directamente](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Incorporación de una aplicación ajena a la galería

Para agregar una aplicación desde la galería de Azure AD, siga estos pasos:

1.  Abra [Azure Portal](https://portal.azure.com) e inicie sesión como **administrador global** o **coadministrador**

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en el botón **Agregar** situado en la esquina superior derecha de la hoja **Aplicaciones empresariales**.

6.  Haga clic en **Aplicación situada fuera de la galería**.

7.  Escriba el nombre de la aplicación en el cuadro de texto **Nombre**. Seleccione **Agregar**.

Tras un breve período, podrá ver la hoja de configuración de la aplicación.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configuración de la aplicación para el inicio de sesión único con contraseña

Para configurar el inicio de sesión único para una aplicación, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

  * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación que desea configurar para el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Seleccione el modo de **Inicio de sesión con contraseña**.

9.  Escriba la **dirección URL de inicio de sesión**. Es la dirección URL en la que los usuarios escriben su nombre de usuario y contraseña para iniciar sesión. Asegúrese de que los campos de inicio de sesión estén visibles en la dirección URL.

10. Asigne usuarios a la aplicación.

11. Además, también puede proporcionar credenciales en nombre del usuario; para ello, seleccione las filas de los usuarios, haga clic en **Actualizar credenciales** y escriba el nombre de usuario y la contraseña en nombre de los usuarios. En caso contrario, se solicitará a los usuarios que especifiquen ellos mismos las credenciales al inicio.

## <a name="assign-a-user-to-an-application-directly"></a>Asignar un usuario a una aplicación directamente

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

## <a name="assign-an-application-to-a-group-directly"></a>Asignar una aplicación a un grupo directamente

Para asignar uno o varios grupos a una aplicación directamente, siga los pasos siguientes:

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

10. Escriba el **nombre completo** del grupo al que quiere asignar en el cuadro de búsqueda **Buscar por nombre o dirección de correo**.

11. Mantenga el puntero sobre el **grupo** en la lista para que aparezca una **casilla**. Haga clic en la casilla situada junto a la foto o el logotipo del perfil del grupo para agregar ese usuario a la lista de **seleccionados**.

12. **Opcional**: si desea **agregar más de un grupo**, escriba otro **nombre de grupo completo** o en el cuadro de búsqueda **Buscar por nombre o dirección de correo** y haga clic en la casilla para agregar ese grupo a la lista de **seleccionados**.

13. Cuando haya terminado de seleccionar grupos, haga clic en el botón **Seleccionar** para agregarlos a la lista de usuarios y grupos que se asignarán a la aplicación.

14. **Opcional:** haga clic en el selector **Seleccionar rol** de la hoja **Agregar asignación** para seleccionar un rol que se asignará a los grupos que ha seleccionado.

15. Haga clic en el botón **Asignar** para asignar la aplicación a los grupos seleccionados.

Tras un breve período, los usuarios que seleccionó podrán iniciar estas aplicaciones en el panel de acceso.

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)
