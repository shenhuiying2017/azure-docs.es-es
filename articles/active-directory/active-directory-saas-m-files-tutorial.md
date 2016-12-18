---
title: "Tutorial: integración de Azure Active Directory con M-Files | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y M-Files."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bb78abd45760c17e94f35be0a6c78ab5d91253d3


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: integración de Azure Active Directory con M-Files
En este tutorial, aprenderá a integrar M-Files con Azure Active Directory (Azure AD).

La integración de M-Files con Azure AD le proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a M-Files
* Puede permitir que los usuarios inicien sesión automáticamente en M-Files (inicio de sesión único) con sus cuentas de Azure AD
* Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con M-Files, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Una suscripción habilitada para el inicio de sesión único en **M-Files**

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.
> 
> 

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de M-Files desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-m-files-from-the-gallery"></a>Incorporación de M-Files desde la galería
Para configurar la integración de M-Files en Azure AD, es preciso agregar M-Files desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar M-Files desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **M-Files**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. En el panel de resultados, seleccione **M-Files** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y prueba el inicio de sesión único de Azure AD con M-Files con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de M-Files para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de M-Files.
Dicha relación se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** en M-Files. Para configurar y probar el inicio de sesión único de Azure AD con M-Files, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de M-Files](#creating-a-m-file-test-user)**: para tener un homólogo de Britta Simon en M-Files que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación M-Files.

**Para configurar el inicio de sesión único de Azure AD con M-Files, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de la aplicación **M-Files**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único][7] 
2. En la página **¿Cómo desea que los usuarios inicien sesión en M-Files?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. En la página de diálogo **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)

    a. En el cuadro de texto URL de inicio de sesión, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`.

    b. Haga clic en **Next**.


1. En la página **Configurar inicio de sesión único en Jostle**, haga clic en **Descargar metadatos** y guarde el archivo en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
2. Para que SSO se configure para su aplicación, póngase en contacto con el equipo de soporte técnico de M-Files a través de <mailto:support@m-files.com> y proporciónele los metadatos descargados.
   
   > [!NOTE]
   > Siga estos pasos si desea configurar SSO para la aplicación de escritorio de archivo M-Files. Si solo desea configurar SSO para la versión de web de M-Files, no se necesitan pasos adicionales. 
   > 
   > 
3. Siga estos pasos para configurar la aplicación de escritorio M-Files para habilitar SSO con Azure AD. Para descargar M-Files, vaya a la página de [descarga de M-Files](https://www.m-files.com/en/download-latest-version).
4. Abra la ventana **M-Files Desktop Settings** (Configuración de escritorio de M-Files). A continuación, haga clic en **Add** (Agregar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
5. En la ventana **Document Vault Connection Properties** (Propiedades de conexión del almacén de documentos), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)
   
    En la sección **Server** (Servidor), escriba los valores como indica a continuación:
   
    a. En **Name** (Nombre), escriba `<tenant-name>.cloudvault.m-files.com`.
   
    b. En **Port Number** (Número de puerto), escriba **4466**.
   
    c. En **Protocol** (Protocolo), seleccione **HTTPS**.
   
    d. En el campo **Authentication** (Autenticación), seleccione **Specific Windows user** (Usuario específico de Windows). Luego, se le solicitará una página de firma. Inserte sus credenciales de Azure AD.
   
    e. En **Vault on Server** (Almacén en servidor),  seleccione el almacén correspondiente en el servidor.
   
    f. Haga clic en **OK**.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. En la página de diálogo **Proporcione información sobre este usuario** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
   
    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.
   
    b. En el cuadro de texto **Nombre de usuario**, escriba**BrittaSimon**.
   
    c. Haga clic en **Siguiente**.
6. En la página de diálogo **Perfil de usuario** , realice los pasos siguientes:
   
   ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png) 
   
   a. En el cuadro de texto **Nombre**, escriba **Britta**.  
   
   b. En el cuadro de texto **Apellidos**, escriba **Simon**.
   
   c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.
   
   d. En la lista **Rol**, seleccione **Usuario**.
   
   e. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. En la página de diálogo **Obtener contraseña temporal** , realice los pasos siguientes:
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png) 
   
    a. Anote el valor del campo **Nueva contraseña**.
   
    b. Haga clic en **Complete**.   

### <a name="creating-a-m-files-test-user"></a>Creación de un usuario de prueba en M-Files
En esta sección, creará un usuario llamado Britta Simon en M-Files. Si no sabe cómo crear un usuario en M-Files, póngase en contacto con el equipo de soporte técnico de M-Files en <mailto:support@m-files.com>.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a M-Files.

![Asignar usuario][200]

**Para asignar el usuario Britta Simon a M-Files, siga estos pasos:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
   
    ![Asignar usuario][201] 
2. En la lista de aplicaciones, seleccione **M-Files**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. En el menú de la parte superior, haga clic en **Usuarios**.
   
    ![Asignar usuario][203] 
4. En la lista Todos los usuarios, seleccione **Britta Simon**.
5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
   
    ![Asignar usuario][205]

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 
El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de M-Files en el panel de acceso, debería iniciar sesión automáticamente en su aplicación M-Files.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


