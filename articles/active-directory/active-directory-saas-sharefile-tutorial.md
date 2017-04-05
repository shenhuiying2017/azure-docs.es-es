---
title: "Tutorial: Integración de Azure Active Directory con Citrix ShareFile | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ddf6c69b-4a76-4b42-8619-6f2a22800a23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9dfbf4bcdcd580773a368b90c869bf9c4fefbd77
ms.lasthandoff: 03/25/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Tutorial: Integración de Azure Active Directory con Citrix ShareFile

En este tutorial, aprenderá a integrar Citrix ShareFile con Azure Active Directory (Azure AD).

La integración de Citrix ShareFile con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Citrix ShareFile
- Puede permitir que los usuarios inicien sesión automáticamente en Citrix ShareFile (Inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Citrix ShareFile, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Citrix ShareFile


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Citrix ShareFile desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-citrix-sharefile-from-the-gallery"></a>Adición de Citrix ShareFile desde la galería
Para configurar la integración de Citrix ShareFile en Azure AD, deberá agregar Citrix ShareFile desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Citrix ShareFile desde la galería, realice los siguientes pasos:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Citrix ShareFile**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_001.png)

5. En el panel de resultados, seleccione **Citrix ShareFile** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Citrix ShareFile con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo en Citrix ShareFile de un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Citrix ShareFile.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Citrix ShareFile.

Para configurar y probar el inicio de sesión único de Azure AD con Citrix ShareFile, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Citrix ShareFile ](#creating-a-citrix-sharefile-test-user)**: para tener un homólogo de Britta Simon en Citrix ShareFile que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Citrix ShareFile.

**Para configurar el inicio de sesión único de Azure AD con Citrix ShareFile, realice los siguientes pasos:**

1. En el nuevo Portal de administración de Azure, en la página de integración de la aplicación **Citrix ShareFile**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_01.png)

3. En la sección **Dominio y direcciones URL de Citrix ShareFile**, lleve a cabo los siguientes pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_02.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.sharefile.com/saml/login`.    

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Si no conoce estas direcciones URL, escriba direcciones URL de ejemplo con el patrón de ejemplo y, luego, obtendrá las direcciones URL reales después de completar el paso 13.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_05.png) 

5. En la sección **Configuración de Citrix ShareFile**, haga clic en **Configurar Citrix ShareFile** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_06.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_07.png)

6. En otra ventana del explorador web, inicie sesión como administrador en su sitio de empresa Citrix ShareFile.

7. En la barra de herramientas de la parte superior, haga clic en **Administración**.

8. En el panel de navegación izquierdo, haga clic en **Configurar inicio de sesión único**.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_002.png)

9. En la página del cuadro de diálogo **Configuración de inicio de sesión único/SAML 2.0** en **Configuración básica**, realice los pasos siguientes:

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-sharefile-tutorial/sso_configuration_2.png)

    a. Haga clic en **Enable SAML**(Habilitar SAML).

    b. En el cuadro de texto **Your IDP Issuer/ Entity ID** (Emisor de IDP/Id. de entidad), coloque el valor de **SAML Entity ID** (Id. de identidad de SAML) de la ventana de configuración de la aplicación de Azure AD.

    c. Haga clic en **Cambiar** junto al campo **Certificado X.509** y luego cargue el archivo de certificado descargado. 

    d. En el cuadro de texto **URL de inicio de sesión**, coloque el valor de **Dirección URL del servicio de inicio de sesión único** en la ventana de configuración de aplicaciones de Azure AD.

    e. En el cuadro de texto **URL de cierre de sesión**, coloque el valor de la **dirección URL de cierre de sesión** de la ventana de configuración de la aplicación de Azure AD.

10. Haga clic en **Guardar** en el portal de administración de Citrix ShareFile.
    
 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-citrix-sharefile-test-user"></a>Creación de un usuario de prueba de Citrix ShareFile

Para permitir que los usuarios de Azure AD inicien sesión en Citrix ShareFile, tienen que aprovisionarse en Citrix ShareFile. En el caso de Citrix ShareFile, el aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de empresa Citrix ShareFile como administrador.

2. En la barra de herramientas de la parte superior, haga clic en **Manage Users** (Administrar usuarios). A continuación vaya a **Manage Users Home** (Inicio de administración de usuarios) y haga clic en **Create Employee** (Crear empleado).

    ![Nuevo usuario](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_010.png "Nuevo usuario")

3. En la sección **Basic Information** (Información básica), siga los siguientes pasos:

    ![Nuevo usuario](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_011.png "Nuevo usuario")

    a. En el cuadro de texto **E-mail Address** (Dirección de correo electrónico), escriba la dirección de correo electrónico de la cuenta de Britta Simon.  

    b. En el cuadro de texto **Nombre**, escriba **Britta**.  

    c. En el cuadro de texto **Apellidos**, escriba **Simon**.

4. Haga clic en **Agregar usuario**.

    > [!NOTE]
    > El titular de la cuenta de AAD recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Citrix ShareFile ofrecida por Citrix ShareFile para aprovisionar cuentas de usuario de AAD.



### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure, para lo cual le concederá acceso a Citrix ShareFile.

![Asignar usuario][200] 

**Para asignar Britta Simon a Citrix ShareFile, lleve a cabo los siguientes pasos:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Citrix ShareFile**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Citrix ShareFile en el Panel de acceso, debe iniciar sesión automáticamente en la aplicación Citrix ShareFile.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
