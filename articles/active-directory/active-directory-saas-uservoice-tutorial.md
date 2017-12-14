---
title: "Tutorial: Integración de Azure Active Directory con UserVoice | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UserVoice."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 8ec3fd40b04dc6590b80c643a4429f97f7b874d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: Integración de Azure Active Directory con UserVoice

En este tutorial, obtendrá información sobre cómo integrar UserVoice con Azure Active Directory (Azure AD).

La integración de UserVoice con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a UserVoice.
- Puede permitir que los usuarios inicien sesión automáticamente en UserVoice (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con UserVoice, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en UserVoice

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar UserVoice desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-uservoice-from-the-gallery"></a>Agregar UserVoice desde la galería
Para configurar la integración de UserVoice en Azure AD, deberá agregar UserVoice desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar UserVoice desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **UserVoice**, seleccione **UserVoice** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![UserVoice en la lista de resultados](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con UserVoice con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de UserVoice para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de UserVoice.

Para establecer la relación de vínculo, en UserVoice, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con UserVoice, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de UserVoice](#create-a-uservoice-test-user)**: para tener un homólogo de Britta Simon en UserVoice que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación UserVoice.

**Para configurar el inicio de sesión único de Azure AD con UserVoice, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **UserVoice**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. En la sección **Dominio y direcciones URL de UserVoice**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.UserVoice.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de UserVoice](https://www.uservoice.com/) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de UserVoice**, haga clic en **Configurar UserVoice** para abrir la ventana **Configurar inicio de sesión**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configuración de UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de UserVoice como administrador.

8. En la barra de herramientas de la parte superior, haga clic en **Configuración** y luego seleccione **Portal web** en el menú.
   
    ![Sección Configuración en la aplicación](./media/active-directory-saas-uservoice-tutorial/ic777519.png "Settings")

9. En la pestaña **Portal web** de la sección **Autenticación del usuario**, haga clic en **Editar** para abrir la página de diálogo **Editar autenticación de usuario**.
   
    ![Pestaña Portal web](./media/active-directory-saas-uservoice-tutorial/ic777520.png "Web portal")

10. En la página de diálogo **Editar autenticación del usuario** , realice los pasos siguientes:
   
    ![Editar autenticación de usuario](./media/active-directory-saas-uservoice-tutorial/ic777521.png "Editar autenticación de usuario")
   
    a. Haga clic en **Inicio de sesión único (SSO)**.
 
    b. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal en el cuadro de texto **SSO Remote Sign-In** (Inicio de sesión remoto de SSO).

    c. Pegue el valor de **Sign-Out URL** (Dirección URL de cierre de sesión) que copió de Azure Portal en el cuadro de texto **SSO Remote Sign-Out** (Cierre de sesión remoto de SSO).
 
    d. En el cuadro de texto **Current certificate SHA1 fingerprint** (Huella digital de SHA1 del certificado actual), pegue el valor de **Huella digital** que ha copiado de Azure Portal.
    
    e. Haga clic en **Guardar configuración de autenticación**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-uservoice-test-user"></a>Creación de un usuario de prueba de UserVoice

Para permitir que los usuarios de Azure AD inicien sesión en UserVoice, deben aprovisionarse en UserVoice. En el caso de UserVoice, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **UserVoice** .

2. Vaya a **Configuración**.
   
    ![Configuración](./media/active-directory-saas-uservoice-tutorial/ic777811.png "Configuración")

3. Haga clic en **General**.

4. Haga clic en **Agentes y permisos**.
   
    ![Agentes y permisos](./media/active-directory-saas-uservoice-tutorial/ic777812.png "Agentes y permisos")

5. Haga clic en **Agregar administradores**.
   
    ![Agregar administradores](./media/active-directory-saas-uservoice-tutorial/ic777813.png "Agregar administradores")

6. En el cuadro de diálogo **Invitar a administradores** , realice los pasos siguientes:
   
    ![Invitar a administradores](./media/active-directory-saas-uservoice-tutorial/ic777814.png "Invitar a administradores")
   
    a. En el cuadro de texto de correos electrónicos, escriba la dirección de correo electrónico de la cuenta que quiere aprovisionar y luego haga clic en **Agregar**.
   
    b. Haga clic en **Invitar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de UserVoice ofrecida por UserVoice para aprovisionar cuentas de usuario de AAD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a UserVoice.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a UserVoice, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **UserVoice**.

    ![Vínculo a UserVoice en la lista de aplicaciones](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de UserVoice en el panel de acceso, debería iniciar sesión automáticamente en su aplicación UserVoice.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png

