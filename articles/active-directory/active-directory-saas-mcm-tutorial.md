---
title: "Tutorial: Integración de Azure Active Directory con MCM | Microsoft Docs"
description: "Aprenda a usar MCM con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f00799d-e3e9-4ba9-ae4a-fbca843ac5db
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51a736c051bef8550662060fdde89fc50e32928a


---
# <a name="tutorial-azure-active-directory-integration-with-mcm"></a>Tutorial: Integración de Azure Active Directory con MCM
El objetivo de este tutorial es mostrar cómo integrar MCM con Azure Active Directory (Azure AD).

La integración de MCM con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a MCM.
* Puede permitir que los usuarios inicien sesión automáticamente en MCM (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con MCM, necesita los siguientes elementos:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en MCM

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

1. Adición de MCM desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mcm-from-the-gallery"></a>Adición de MCM desde la galería
Para configurar la integración de MCM en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar MCM desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Add application")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Add an application from gallerry")

6. En el **cuadro de búsqueda**, escriba **MCM**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Application gallery")

7. En el panel de resultados, seleccione **MCM** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![MCM](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con MCM utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de MCM para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de MCM.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en MCM.

Para configurar y probar el inicio de sesión único de Azure AD con MCM, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de usuario de prueba de MCM](#creating-a-mcm-test-user)** : para tener un homólogo de Britta Simon en MCM que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación MCM.

**Para configurar el inicio de sesión único de Azure AD con MCM, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **MCM**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configure single sign-on")

2. En la página **¿Cómo desea que los usuarios inicien sesión en MCM?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3. En la página de diálogo Configurar las opciones de la aplicación, siga estos pasos:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configure App URL")
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba `https://myaba.co.uk/client-access/<company name>/saml.php`.
   
    b. click **Siguiente**

4. En la página **Configurar inicio de sesión único en MCM**, haga clic en **Descargar metadatos** y, después, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configure Single Sign-On")

5. Si quiere configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de soporte técnico de MCM. Adjunte el archivo de metadatos descargado y compártalo con el equipo de MCM para que le configure el inicio de sesión único.

6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configure Single Sign-On")

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.

6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-mcm-test-user"></a>Creación de usuario de prueba de MCM
En esta sección, creará un usuario llamado Britta Simon en MCM. Colabore con el equipo de soporte técnico de MCM para agregar los usuarios en esta plataforma.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de MCM que ofrezca esta plataforma para aprovisionar cuentas de usuario de AAD.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a MCM.

![Asignar usuarios](./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assign users")

**Para asignar el usuario Britta Simon a MCM, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuarios](./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assign users")

2. En la lista de aplicaciones, seleccione **MCM**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assign users")

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuarios](./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de MCM del panel de acceso, debería iniciar sesión automáticamente en la aplicación MCM.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


