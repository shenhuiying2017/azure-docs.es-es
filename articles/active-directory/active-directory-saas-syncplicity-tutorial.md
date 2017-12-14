---
title: "Tutorial: integración de Azure Active Directory con Syncplicity | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Syncplicity."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: a42e17539df4d4c0e57f5a5541232968f897160e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: integración de Azure Active Directory con Syncplicity

En este tutorial, aprenderá a integrar Syncplicity con Azure Active Directory (Azure AD).

La integración de Syncplicity con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Syncplicity
- Puede habilitar que los usuarios inicien sesión automáticamente en Syncplicity (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Syncplicity, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Syncplicity

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Syncplicity desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-syncplicity-from-the-gallery"></a>Incorporación de Syncplicity desde la galería
Para configurar la integración de Syncplicity en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Syncplicity desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Syncplicity**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. En el panel de resultados, seleccione **Syncplicity** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Syncplicity con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Syncplicity para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Syncplicity.

Para establecer la relación de vínculo, en Syncplicity, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Syncplicity, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Syncplicity](#creating-a-syncplicity-test-user)**: para tener un homólogo de Britta Simon en Syncplicity vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Syncplicity.

**Para configurar el inicio de sesión único de Azure AD con Syncplicity, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Syncplicity**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. En la sección **Syncplicity Domain and URLs** (Dominio y direcciones URL de Syncplicity), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.syncplicity.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Syncplicity](https://www.syncplicity.com/contact-us) para obtener estos valores. 
 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. En la sección **Syncplicity Configuration** (Configuración de Syncplicity), haga clic en **Configure Syncplicity** (Configurar Syncplicity) para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. Inicie sesión en su inquilino de **Syncplicity** .

8. En el menú en la parte superior, haga clic en **admin** (Administración), seleccione **settings** (Configuración) y luego haga clic en **Custom domain and single sign-on** (Dominio personalizado e inicio de sesión único).
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. En la página del cuadro de diálogo **Single Sign-On (SSO)** (Configuración de inicio de sesión único [SSO]), siga estos pasos:
   
    ![Inicio de sesión único \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. En el cuadro de texto **Custom Domain** (Dominio personalizado), escriba el nombre de su dominio.
  
    b. Seleccione **Enabled** (Habilitado) como **Single Sign-On Status** (Estado de inicio de sesión único).

    c. En el cuadro de texto **Id. de entidad**, pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.

    d. Copie el valor de **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML) que ha copiado de Azure Portal en el cuadro de texto **Sign-in page URL** (Dirección URL de la página de inicio de sesión).

    e. Copie el valor de **Sign-out URL** (Dirección URL de cierre de sesión) que ha copiado de Azure Portal en el cuadro de texto **Logout page URL** (Dirección URL de la página de cierre de sesión).

    f. En **Identity Provider Certificate** (Certificado del proveedor de identidades), haga clic en **Elegir archivo** y cargue el certificado que ha descargado de Azure Portal. 

    g. Haga clic en **GURDAR CAMBIOS**.

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-syncplicity-test-user"></a>Creación de un usuario de prueba de Syncplicity
Para que los usuarios de AAD puedan iniciar sesión, deben aprovisionarse a Syncplicity. En esta sección se describe cómo crear cuentas de usuario de AAD en Syncplicity.

**Para aprovisionar cuentas de usuario en Syncplicity, realice los siguientes pasos:**

1. Inicie sesión en su inquilino de **Syncplicity** (por ejemplo, `https://company.Syncplicity.com`).

2. Haga clic en **admin** y seleccione **cuentas de usuario**.

3. Haga clic en **ADD A USER** (AGREGAR UN USUARIO).
   
    ![Administración de usuarios](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "Administración de usuarios")

4. Escriba las **Email Address** (Direcciones de correo electrónico) de una cuenta de AAD que quiera aprovisionar, seleccione **User** (Usuario) como **Role** (Rol) y haga clic en **NEXT** (SIGUIENTE).
   
    ![Información de la cuenta](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "Información de la cuenta")
   
    >[!NOTE]
    >El titular de la cuenta de AAD recibirá un mensaje de correo electrónico junto con un vínculo para confirmar y activar la cuenta. 
    > 

5. Seleccione un grupo de la compañía de la que debe convertirse en miembro su nuevo usuario y haga clic en **NEXT**(SIGUIENTE).
   
    ![Pertenencia a grupos](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "Pertenencia a grupos")
   
    >[!NOTE]
    >Si no se muestra ningún grupo, haga clic en **NEXT**(SIGUIENTE). 
    > 

6. Seleccione las carpetas que desea colocar bajo el control de Syncplicity en el equipo del usuario y haga clic en **NEXT**(SIGUIENTE).
   
    ![Carpetas de Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Carpetas de Syncplicity")

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Syncplicity ofrecida por Syncplicity para aprovisionar cuentas de usuario de AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Syncplicity.

![Asignar usuario][200] 

**Para asignar Britta Simon a Syncplicity, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Syncplicity**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Syncplicity en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Syncplicity.
## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png

