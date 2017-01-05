---
title: "Tutorial: Integración de Azure Active Directory con Everbridge | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Everbridge."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5608f38de6ae78772499a40c4e2d449cd5efc79c
ms.openlocfilehash: 511a3883cadd9bf2fe1cc39a8626c49fa19a7d8e


---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integración de Azure Active Directory con EverBridge
El objetivo de este tutorial es mostrar cómo integrar Everbridge con Azure Active Directory (Azure AD).

La integración de Everbridge con Azure AD proporciona las siguientes ventajas:

* Le permite controlar en Azure AD quién tiene acceso a Everbridge
* Puede habilitar a los usuarios para que inicien sesión automáticamente en Everbridge (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Everbridge, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en Everbridge

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

1. Incorporación de Everbridge desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-everbridge-from-the-gallery"></a>Incorporación de Everbridge desde la galería
Para configurar la integración de Everbridge en Azure AD, es preciso agregar Everbridge desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Everbridge desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Everbridge**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_01.png)
7. En el panel de resultados, seleccione **Everbridge** y haga clic en **Completar** para agregar la aplicación.
   
    ![Selección de la aplicación en la galería](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Everbridge con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD tiene que saber cuál es el usuario homólogo de Everbridge para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Everbridge.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Everbridge.

Para configurar y probar el inicio de sesión único de Azure AD con Everbridge, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba para Everbridge](#creating-a-everbridge-test-user)** : para tener un homólogo de Britta Simon en Everbridge que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Everbridge.

**Para configurar el inicio de sesión único de Azure AD con Everbridge, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones de **Everbridge**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][6] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en Everbridge?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_03.png) 
3. En el cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_04.png)
   
    a. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: `https://sso.everbridge.net/{<company name>}`.
   
    b. En el cuadro de texto **URL de respuesta**, escriba la dirección URL con el siguiente patrón: `https://manager.everbridge.net/saml/SSO/{<company name>}/alias/defaultAlias`.
   
    c. Haga clic en **Siguiente**
4. En la página **Configurar inicio de sesión único en Everbridge**, lleve a cabo estos pasos y haga clic en **Siguiente**:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_05.png)
   
    a. Haga clic en **Descargar metadatos**y luego guarde el archivo en el equipo.
   
    b. Haga clic en **Siguiente**.
5. Para configurar SSO para la aplicación, tiene que iniciar sesión en su inquilino de Everbridge como administrador.
6. En el menú de la parte superior, haga clic en la pestaña **Settings** (Configuración) y seleccione **Single Sign-On** (Inicio de sesión único) en **Security** (Seguridad).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. En el cuadro de texto **Nombre** , escriba el nombre del proveedor de identidades (por ejemplo, el nombre de la compañía).
   
    b. En el cuadro de texto **API Name** (nombre de API), escriba el nombre de la API.
   
    C. Haga clic en el botón **Choose File**(Elegir archivo) para cargar el archivo de metadatos que descargó en el **Paso 4**.
   
    d. Como **SAML Identity Location**(Ubicación de identidad de SAML), seleccione "Identity is in the NameIdentifier element of the Subject statement" ("La identidad está en el elemento NameIdentifier de la instrucción de sujeto").
   
    e. Copie la dirección URL de SSO de SAML de Azure AD para **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades) en Everbridge.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. Para **Service Provider Initiated Request Binding**(Vinculación de solicitud iniciada por el proveedor de servicios), seleccione HTTP Redirect (Redirección HTTP).
7. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
   
    ![Inicio de sesión único de Azure AD ][10]
8. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.  
   
    ![Inicio de sesión único de Azure AD ][11]

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_09.png)
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png)
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png)
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_05.png)
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_06.png)
   
    a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
    b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
    d. En la lista **Rol**, seleccione **Usuario**.
   
    e. Haga clic en **Siguiente**.

7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_07.png)
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_08.png)
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Completo**.   

### <a name="creating-a-everbridge-test-user"></a>Creación de un usuario de prueba para Everbridge
En esta sección, creará una usuaria llamada Britta Simon en Everbridge. Colabore con el equipo de soporte técnico de Everbridge mediante <mailto:support@everbridge.com> para agregar los usuarios a la plataforma Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Everbridge.

![Asignar usuario][200]

**Para asignar a Britta Simon a Everbridge, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201]
2. En la lista de aplicaciones, seleccione **Everbridge**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_50.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203]
4. En la lista Usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Everbridge en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Everbridge.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


