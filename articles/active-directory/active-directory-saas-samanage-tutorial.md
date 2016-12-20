---
title: "Tutorial: integración de Azure Active Directory con Samanage | Microsoft Docs"
description: "Aprenda cómo usar Samanage con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: da49f1d9f8aa5fbbaf6e16f1db03a16f80a2bc92


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Tutorial: integración de Azure Active Directory con Samanage
El objetivo de este tutorial es mostrar cómo integrar Samanage con Azure Active Directory (Azure AD).

La integración de Samanage con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Samanage.
* Puede habilitar que los usuarios inicien sesión automáticamente en Samanage (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Samanage, necesita los siguientes elementos:

* Una suscripción de Azure válida
* Un inquilino de Samanage

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

1. Adición de Samanage desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-samanage-from-the-gallery"></a>Adición de Samanage desde la galería
Para configurar la integración de Samanage en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Samanage desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Samanage**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "Application gallery")
7. En el panel de resultados, seleccione **Samanage** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Samanage con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Samanage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Samanage.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en Samanage.

Para configurar y probar el inicio de sesión único de Azure AD con Samanage, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Samanage](#creating-a-Samanage-test-user)** : para tener un homólogo de Britta Simon en Samanage que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Samanage.

**Para configurar el inicio de sesión único de Azure AD con Samanage, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **Samanage**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Samanage?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-On")
3. En la página de diálogo Configurar las opciones de la aplicación, siga estos pasos:
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "Configure App URL")
   
   a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.
   
   b. click **Siguiente**
   
   > [!NOTE]
   > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar estos valores con la dirección URL de inicio de sesión real. Para obtener estos valores, consulte el paso 8.c o póngase en contacto con Samanage.
   > 
   > 
4. En la página **Configuración de inicio de sesión único en Samanage**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Samanage como administrador.
6. Haga clic en **Dashboard** (Panel) y seleccione **Setup** (Configuración) en el panel de navegación izquierdo.
   
   ![Panel](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Dashboard")
7. Haga clic en **Inicio de sesión único**.
   
   ![Inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. Navegue hasta la sección **Login using SAML** (Iniciar sesión con SAML), realice los pasos siguientes:
   
   ![Login using SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
   
   a.  Haga clic en **Enable Single Sign-On with SAML**(Habilitar inicio de sesión único con SAML).
   
   b.  En el cuadro de texto **Identity Provider URL** (URL de proveedor de identidad), coloque el valor de **Id. de proveedor de identidad** del Asistente para configuración de aplicaciones de Azure AD.    
   
   c.  Confirme que el valor de **Login URL** (URL de inicio de sesión) coincide con el de **URL de inicio de sesión** del paso 3.
   
   d.  En el cuadro de texto **Logout URL** (URL de cierre de sesión), coloque el valor de **Dirección URL de cierre de sesión remoto** del Asistente para configuración de aplicaciones de Azure AD.
   
   e. En el cuadro de texto **SAML Issuer** (Emisor de SAML) escriba el identificador URI del identificador de la aplicación establecido en el proveedor de identidades.
   
   f.  Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y péguelo en el cuadro de texto **Paste your Identity Provider x.509 Certificate below** (Pegue a continuación el certificado x.509 del proveedor de identidades).
   
   g.  Haga clic en **Create users if they do not exist in Samanage**(Crear usuarios si no existen en Samanage).
   
   h.  Haga clic en **Actualizar**.
9. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Configure Single Sign-On")
10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Configure Single Sign-On")

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png)
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-samanage-test-user"></a>Creación de un usuario de prueba en Samanage
Para habilitar que los usuarios de Azure AD inicien sesión en Samanage, deben aprovisionarse en Samanage. En este caso concreto, el aprovisionamiento es una tarea manual.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:
1. Inicie sesión en su sitio de la compañía de Samanage como administrador.
2. Haga clic en **Dashboard** (Panel) y seleccione **Setup** (Configuración) en el panel de navegación izquierdo.
   
   ![Configuración](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Setup")
3. Haga clic en la pestaña **Usuarios** .
   
   ![Usuarios](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Users")
4. Haga clic en **Nuevo usuario**.
   
   ![Nuevo usuario](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "New User")
5. Escriba el **nombre** y la **dirección de correo electrónico** de la cuenta de Azure AD que desea aprovisionar y haga clic en **Create user** (Crear usuario).
   
   ![Crear usuario](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Creat User")
   
   > [!NOTE]
   > El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta para que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Samanage que proporcione Samanage para aprovisionar cuentas de usuario de AAD.
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es habilitar que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Samanage.

![Asignar usuarios](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "Assign users")

**Para asignar a Britta Simon a Samanage, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuarios](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "Assign users")
2. En la lista de aplicaciones, seleccione **Samanage**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "Assign users")
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuarios](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "Assign users")

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Samanage en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Samanage.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


