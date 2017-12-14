---
title: "Tutorial: Integración de Azure Active Directory con @Task | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 2443c7e2e57bd9c36af292658ff85a973d5ec224
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Tutorial: integración de Azure Active Directory con @Task
El objetivo de este tutorial es mostrar cómo integrar @Task con Azure Active Directory (Azure AD).  
La integración de @Task con Azure AD le proporciona las siguientes ventajas: 

* En Azure AD se puede controlar quién tiene acceso a @Task
* Puede permitir que los usuarios inicien sesión automáticamente en @Task (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con @Task, se necesitan los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para inicio de sesión único en @Task

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.  
El escenario descrito en este tutorial consta de tres bloques de creación principales:

1. Adición de @Task desde la galería 
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-task-from-the-gallery"></a>Adición de @Task desde la galería
Para configurar la integración de @Task en Azure AD, será preciso agregar @Task desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar @Task desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1] 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2] 
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3] 
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4] 
6. En el cuadro de búsqueda, escriba **@Task**.
   
    ![Aplicaciones][5] 
7. En el panel de resultados, seleccione **@Task** y haga clic en **Completar** para agregar la aplicación.
   
    ![Aplicaciones][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con @Task utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de @Task para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de @Task.   
Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en @Task.

Para configurar y probar el inicio de sesión único de Azure AD con @Task, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de prueba de @Tasktest](#creating-a-halogen-software-test-user)** : para tener un homólogo de Britta Simon en @Taskthat que esté vinculado a la representación de esta en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación @Task.

**Para configurar el inicio de sesión único de Azure AD con @Task, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **@Task**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en @Task**?, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][7] 
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar las opciones de la aplicación][8] 
   
     a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en la aplicación @Task (p. ej.:*https://<Tenant name>.attask-ondemand.com*).
   
     b. Haga clic en **Siguiente**.
4. En la página **Configuración de inicio de sesión único en @Task**, haga clic en **Descargar metadatos**, guarde el archivo de metadatos localmente en el equipo y luego haga clic en **Siguiente**.
   
    ![Qué es Azure AD Connect][9] 
5. Inicie sesión en su sitio de la compañía de @Task como administrador.
6. Vaya a **Single Sign On Configuration**(Configuración de inicio de sesión único).
7. En el cuadro de diálogo **Inicio de sesión único** , siga estos pasos.
   
    ![Configurar inicio de sesión único][23]
   
    a. En **Tipo**, seleccione **SAML 2.0**.
   
    b. Seleccione **Service Provider ID** (Id. del proveedor de servicios).
   
    c. En el Portal de Azure clásico, copie el valor de **Dirección URL de inicio de sesión remoto** y péguela en el cuadro de texto **Login Portal URL** (Dirección URL del portal de inicio de sesión).
   
    d. En el Portal de Azure clásico, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguela en el cuadro de texto **Sign-Out URL** (Dirección URL de cierre de sesión).
   
    e. En el Portal de Azure clásico, copie el valor de **Cambiar dirección URL de contraseña** y péguelo en el cuadro de texto **Change Password URL** (Cambiar dirección URL de contraseña).
   
    f. Haga clic en **Guardar**.
8. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**. 
   
    ![Qué es Azure AD Connect][10]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Qué es Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.  

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-an-task-test-user"></a>Creación de un usuario de prueba de @Task
El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en @Task.

**Para crear un usuario llamado Britta Simon en @Task, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de @Task como administrador.
2. En el menú de la parte superior, haga clic en **People**(Personas).
3. Haga clic en **New Person**(Nueva persona). 
4. En el cuadro de diálogo Nueva persona, realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de @Task][21] 
   
    a. En el cuadro de texto **Nombre** , escriba "Britta".
   
    b. En el cuadro de texto **Apellidos** , escriba "Simon".
   
    c. En el cuadro de texto **E-mail Address** (Dirección de correo electrónico), escriba la dirección de correo electrónico de Britta Simon en Azure Active Directory.
   
    d. Haga clic en **Add Person**(Agregar persona).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a @Task.

![Asignar usuario][200] 

**Para asignar a Britta Simon a @Task, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **@Task**.
   
    ![Asignar usuario][202] 
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  
Al hacer clic en el icono de @Task en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación @Task.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






