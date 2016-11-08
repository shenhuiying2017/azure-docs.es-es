---
title: 'Tutorial: Integración de Azure Active Directory con StatusPage | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y StatusPage.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integración de Azure Active Directory con StatusPage
El objetivo de este tutorial es mostrar cómo integrar StatusPage con Azure Active Directory (Azure AD).

La integración de StatusPage con Azure AD proporciona las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a StatusPage. 
* Puede permitir que los usuarios inicien sesión automáticamente en StatusPage (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con StatusPage, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en StatusPage

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. 

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de StatusPage desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-statuspage-from-the-gallery"></a>Incorporación de StatusPage desde la galería
Para configurar la integración de StatusPage en Azure AD, deberá agregar StatusPage desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar StatusPage desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **StatusPage**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_01.png)
7. En el panel de resultados, seleccione **StatusPage** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con StatusPage con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de StatusPage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de StatusPage.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en StatusPage.

Para configurar y probar el inicio de sesión único de Azure AD con StatusPage, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de StatusPage](#creating-a-statuspage-test-user)** : para tener un homólogo de Britta Simon en StatusPage que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación StatusPage. 

**Para configurar el inicio de sesión único de Azure AD con StatusPage, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **StatusPage**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en StatusPage?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_04.png) 
   
   > [!NOTE]
   > Póngase en contacto con el equipo de soporte técnico de StatusPage [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)a fin de solicitar los metadatos necesarios para configurar el inicio de sesión único.
   > 
   > 

    a. En los metadatos, copie el valor de Emisor y luego péguelo en el cuadro de texto **Identificador** .

    b. En los metadatos, copie el valor de URL de respuesta y luego péguelo en el cuadro de texto **URL de respuesta** .

    c. Haga clic en **Siguiente**.


1. En la página **Configurar inicio de sesión único en StatusPage** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_05.png) 
   
    a. Haga clic en **Descargar certificado**y después guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
2. En otra ventana del explorador, inicie sesión en su sitio de la empresa de StatusPage como administrador.
3. En la barra de herramientas principal, haga clic en **Administrar cuenta**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 
4. Haga clic en la pestaña **Inicio de sesión único** . 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 
5. En la página Configuración de SSO, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 
   
    a. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en StatusPage**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL de destino SSO**. 
   
    b. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado** . 
   
    c. Haga clic en **Guardar**.
6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Inicio de sesión único de Azure AD ][10]
7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_09.png)  
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_05.png)  
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-statuspage-test-user"></a>Creación de un usuario de prueba de StatusPage
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en StatusPage.
StatusPage admite el aprovisionamiento Just-In-Time. Ya lo ha habilitado en [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

**Para crear un usuario llamado Britta Simon en StatusPage, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la empresa StatusPage como administrador.
2. En el menú de la parte superior, haga clic en **Administrar cuenta**.
3. Haga clic en la pestaña Miembros del equipo. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 
4. Haga clic en **Add Team Member**(Agregar miembro del equipo). 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 
5. Escriba **Email Address** (Dirección de correo electrónico), **First Name** (Nombre) y **Surname** (Apellido) en los cuadros de texto correspondientes para un usuario válido que quiera aprovisionar. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 
6. En **Rol**, elija **Administrador de clientes**.
7. Click **Crear cuenta**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a StatusPage.

![Asignar usuario][200] 

**Para asignar a Britta Simon a StatusPage, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **StatusPage**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de StatusPage en el panel de acceso, debería iniciar sesión automáticamente en su aplicación StatusPage.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_205.png









<!--HONumber=Oct16_HO2-->


