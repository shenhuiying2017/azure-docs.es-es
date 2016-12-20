---
title: "Tutorial: Integración de Azure Active Directory con RunMyProcess | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RunMyProcess."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 223a0abfcd1ae2997e4d2facfd065cf8dc721668


---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Integración de Azure Active Directory con RunMyProcess
El objetivo de este tutorial es mostrar cómo integrar RunMyProcess con Azure Active Directory (Azure AD).

La integración de RunMyProcess con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a RunMyProcess
* Puede permitir que los usuarios inicien sesión automáticamente en RunMyProcess (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con RunMyProcess se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en RunMyProcess

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

1. Adición de RunMyProcess desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-runmyprocess-from-the-gallery"></a>Adición de RunMyProcess desde la galería
Para configurar la integración de RunMyProcess en Azure AD, será preciso que agregue RunMyProcess desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar RunMyProcess desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **RunMyProcess**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)
7. En el panel de resultados, seleccione **RunMyProcess** y haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con RunMyProcess con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de RunMyProcess para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de RunMyProcess.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en RunMyProcess.

Para configurar y probar el inicio de sesión único de Azure AD con RunMyProcess, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de RunMyProcess](#creating-a-runmyprocess-test-user)**: para tener un homólogo de Britta Simon en RunMyProcess que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación RunMyProcess.

**Para configurar el inicio de sesión único de Azure AD con RunMyProcess, siga estos pasos:**

1. En el portal clásico, en la página de integración de aplicaciones de **RunMyProcess**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en RunMyProcess?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://live.runmyprocess.com/live/<tenant id>`.
   
    b. click **Siguiente**
   
   > [!NOTE]
   > Tenga en cuenta que tiene que actualizar el valor con el valores real de URL de inicio de sesión. Para obtener este valor, póngase en contacto con el equipo de soporte técnico de RunMyProcess mediante <mailto:support@runmyprocess.com>.
   > 
   > 
4. En la página **Configuración de inicio de sesión único en RunMyProcess**, haga clic en **Descargar certificado** y guarde el archivo en el equipo:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)
5. En otra ventana del explorador web, inicie sesión en su inquilino de RunMyProcess como administrador.
6. En el panel de navegación izquierdo, haga clic en **Account** (Cuenta) y seleccione **Configuration** (Configuración).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)
7. Vaya a la sección **Authentication method** (Método de autenticación) y realice los siguientes pasos:
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)
   
    a. En **Método**, seleccione **SSO con Samlv2**.
   
    b. En el cuadro de texto **SSO redirect** (Redireccionamiento de SSO), coloque el valor de **Dirección URL de inicio de sesión único de SAML** del Asistente para configuración de la aplicación Azure AD.
   
    c. En el cuadro de texto **Logout redirect** (Redireccionamiento de cierre de sesión), coloque el valor de **Dirección URL del servicio de cierre de sesión único** en el Asistente para configuración de la aplicación Azure AD.
   
    d. En el cuadro de texto **Name Id Format** (Formato de id. de nombre), coloque el valor de **Formato de identificador de nombre** del Asistente para configuración de la aplicación Azure AD.
   
    e. Copie el contenido del archivo de certificado descargado y péguelo en el cuadro de texto **Certificate** (Certificado). 
   
    f. Haga clic en el icono **Guardar**.
8. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 
5. En el cuadro de diálogo **Proporcione información sobre este usuario**, siga estos pasos:  ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En el cuadro de diálogo **Perfil de usuario**, siga estos pasos: ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-runmyprocess-test-user"></a>Creación de un usuario de prueba de RunMyProcess
Para permitir que los usuarios de Azure AD inicien sesión en RunMyProcess, deben aprovisionarse en RunMyProcess. En el caso de RunMyProcess, el aprovisionamiento es una tarea manual.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de RunMyProcess como administrador.
2. Haga clic en **Account** (Cuenta) y seleccione **Users** (Usuarios) en el panel de navegación izquierdo y, a continuación, haga clic en **New User** (Usuario nuevo).
   
   ![Nuevo usuario](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "New User")
3. En la sección **Configuración del usuario** , lleve a cabo estos pasos:
   
   ![Perfil](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profile")
   
   a. Escriba el **Nombre** y la **Dirección de correo electrónico** de una cuenta de AAD válida que quiera aprovisionar en los cuadros de texto correspondientes.
   
   b. Seleccione un **Lenguaje IDE**, un **Lenguaje** y un **Perfil**.
   
   c. Seleccione **Enviarme mensaje de correo electrónico de creación de cuenta**.
   
   d. Haga clic en **Guardar**.
   
   > [!NOTE]
   > Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de RunMyProcess que proporcione RunMyProcess para aprovisionar cuentas de usuario de Azure Active Directory.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a RunMyProcess.

![Asignar usuario][200] 

**Para asignar Britta Simon a RunMyProcess, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **RunMyProcess**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de RunMyProcess en el panel de acceso, debería iniciar sesión automáticamente en su aplicación RunMyProcess.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


