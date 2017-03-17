---
title: "Tutorial: Integración de Azure Active Directory con Tableau Online | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tableau Online."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: de8292e6d3642776fc684ded731f48845de846d4
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integración de Azure Active Directory con Tableau Online
En este tutorial, obtendrá información sobre cómo integrar Tableau Online con Azure Active Directory (Azure AD).

Integrar Tableau Online con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Tableau Online.
* Puede permitir que los usuarios inicien sesión automáticamente en Tableau Online (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Tableau Online, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en **Tableau Online**

>[!NOTE]
>Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción. 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único (SSO) de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tableau Online desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-tableau-online-from-the-gallery"></a>Incorporación de Tableau Online desde la galería
Para configurar la integración de Tableau Online en Azure AD, será preciso que agregue Tableau Online desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tableau Online desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Tableau Online**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)
7. En el panel de resultados, seleccione **Tableau Online** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Tableau Online con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tableau Online para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tableau Online.

Para establecer esta relación de vínculo, se toma el valor del **nombre de usuario** en Azure AD y se asigna como valor del **nombre de usuario** en Tableau Online.

Para configurar y probar el inicio de sesión único de Azure AD con Tableau Online, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de Tableau Online](#creating-a-Tableau-Online-test-user)** : para tener un homólogo de Britta Simon en Tableau Online que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurarlo en la aplicación Tableau Online.

**Para configurar el inicio de sesión único de Azure AD con Tableau Online, realice los pasos siguientes:**

1. En el menú de la parte superior, haga clic en **Inicio rápido**.
   
    ![Configurar inicio de sesión único][6]
2. En el portal clásico, en la página de integración de aplicaciones de **Tableau Online**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][7] 
3. En la página **¿Cómo desea que los usuarios inicien sesión en Tableau Online?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)
4. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)
  1. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL con el siguiente patrón: `https://sso.online.tableau.com`
  2. Haga clic en **Siguiente**.
5. En la página **Configuración de inicio de sesión único en Tableau Online**, haga clic en **Descargar metadatos** y guarde el archivo en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)
6. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]
8. En una ventana de explorador diferente, inicie sesión en su aplicación de Tableau Online como administrador. Vaya a **Settings** (Configuración) y luego a **Authentication** (Autenticación).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
9. En la sección **Tipos de autenticación** . Marque la casilla **Inicio de sesión único con SAML** para habilitar SAML.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)
10. Desplácese hacia abajo hasta la sección **Importar archivo de metadatos en Tableau Online** .  Haga clic en Examinar e importe el archivo de metadatos que ha descargado desde Azure AD. A continuación, haga clic en **Aplicar**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)
11. En la sección **Match assertions** (Aserciones de coincidencia), inserte el nombre de aserción del proveedor de identidades correspondiente para la dirección de correo electrónico, el nombre y los apellidos. Para obtener esta información a partir de Azure AD: 
  1. Vuelva a Azure AD. Vaya al Portal de Azure clásico, a la página de integración de aplicaciones de **Tableau Online**.
  2. En el menú en la parte superior, haga clic en **Atributos**. 
  3. Copie el nombre de los valores: userprincipalname, givenname y surname.
   
     ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
  4. Abra la aplicación Tableau Online, a continuación, establezca la sección **Tableau Online Attributes** (Atributos de Tableau Online) como sigue:
     * Correo electrónico: **mail** o **userprincipalname**
     * Nombre: **givenname**
     * Apellidos: **surname**
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 
  1. En Tipo de usuario, seleccione Nuevo usuario de la organización.
  2. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
  3. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 
  1. En el cuadro de texto **Nombre**, escriba **Britta**.  
  2. En el cuadro de texto **Apellidos**, escriba **Simon**.
  3. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
  4. En la lista **Rol**, seleccione **Usuario**. 
  5. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 
  1. Anote el valor del campo **Nueva contraseña**.
  2. Haga clic en **Completo**.   

### <a name="create-a-tableau-online-test-user"></a>Creación de un usuario de prueba de Tableau Online
En esta sección, creará una usuaria llamada Britta Simon en Tableau Online.

1. En **Tableau Online**, haga clic en **Configuración** y en la sección **Autenticación**. Desplácese hacia abajo a la sección **Seleccionar usuarios** . Haga clic en **Agregar usuarios** y en **Especificar direcciones de correo electrónico**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Seleccione **Add users for single sign-on (SSO) authentication**[Agregar usuarios para la autenticación mediante inicio de sesión único (SSO)]. En el cuadro de texto **Especificar direcciones de correo electrónico** agregue britta.simon@contoso.com
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Tableau Online.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tableau Online, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **Tableau Online**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Todos los usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Tableau Online en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tableau Online.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png

