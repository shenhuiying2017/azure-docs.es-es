---
title: "Tutorial: Integración de Azure Active Directory con LockPath Keylight | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y LockPath Keylight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e64a966f24411818abc4cc4ab29a428b5577d012
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Integración de Azure Active Directory con LockPath Keylight

En este tutorial, obtendrá información sobre cómo integrar LockPath Keylight con Azure Active Directory (Azure AD).

La integración de LockPath Keylight con Azure AD le proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a LockPath Keylight.
- Puede permitir que los usuarios inicien sesión automáticamente en LockPath Keylight (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con LockPath Keylight, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LockPath Keylight

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de LockPath Keylight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Incorporación de LockPath Keylight desde la galería
Para configurar la integración de LockPath Keylight en Azure AD, es preciso agregar LockPath Keylight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LockPath Keylight desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **LockPath Keylight**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. En el panel de resultados, seleccione **LockPath Keylight** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con LockPath Keylight con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LockPath Keylight para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LockPath Keylight.

Para establecer la relación de vínculo, en LockPath Keylight, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con LockPath Keylight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Crear un usuario de prueba de LockPath Keylight](#creating-a-lockpath-keylight-test-user)** : para tener un homólogo de Britta Simon en LockPath Keylight que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación LockPath Keylight.

**Para configurar el inicio de sesión único de Azure AD con LockPath Keylight, realice los pasos siguientes:**

1. En la página de integración de la aplicación **LockPath Keylight** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. En la sección **Dominio y direcciones URL de LockPath Keylight**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company name>.keylightgrc.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company name>.keylightgrc.com`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<company name>.keylightgrc.com/Login.aspx`.
    
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de LockPath Keylight](https://www.lockpath.com/contact/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. En la sección **Configuración de LockPath Keylight**, haga clic en **Configurar LockPath Keylight** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Realice los pasos siguientes para habilitar el inicio de sesión único en LockPath Keylight:
   
    a. Inicie sesión en su cuenta de LockPath Keylight como administrador.
    
    b. En el menú de la parte superior, haga clic en el icono de la **persona** y seleccione **Keylight Setup** (Configuración de Keylight).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. En la vista de árbol de la izquierda, haga clic en **SAML**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. En el cuadro de diálogo **SAML Settings** (Configuración de SAML), haga clic en **Edit** (Editar).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/404.png) 

8. En la página del cuadro de diálogo **Edit SAML Settings** (Editar la configuración de SAML), realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Establezca **SAML authentication** (Autenticación SAML) como **Active** (Activada).

    b. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal en el cuadro de texto **Identity provider Login URL** (Dirección URL de inicio de sesión del proveedor de identidades).

    c. Pegue el valor de **Single Sign-Out Service URL** (Dirección URL del servicio de cierre de sesión único) que copió de Azure Portal en el cuadro de texto **Identity Provider Logout URL** (Dirección URL de cierre de sesión del proveedor de identidades).

    d. Haga clic en **Elegir archivo** para seleccionar el certificado de LockPath Keylight descargado y después haga clic en **Abrir** para cargarlo.

    e. Establezca **SAML User Id location** (Ubicación de id. de usuario de SAML) en **NameIdentifier element of the subject statement** (Elemento NameIdentifier de la instrucción Subject).
    
    f. Proporcione el **proveedor de servicios de Keylight** mediante el siguiente patrón: **https://&lt;NombreDeEmpresa&gt;.keylightgrc.com**.
    
    g. Establezca **Auto-provision users** (Usuarios de aprovisionamiento automático) en **Active** (Activado).

    h. Establezca **Auto-provision account type** (Tipo de cuenta de aprovisionamiento automático) en **Full User** (Usuario completo).

    i. Establezca **Auto-provision security role** (Rol de seguridad de aprovisionamiento automático) y seleccione **Standard User with SAML** (Usuario estándar con SAML).
    
    j. Establezca **Auto-provision security config** (Configuración de seguridad de aprovisionamiento automático) y seleccione **Standard User Configuration** (Configuración de usuario estándar).
     
    k. En el cuadro **Atributo de correo electrónico**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. En el cuadro de texto **Atributo de nombre**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. En el cuadro de texto **Atributo de apellido**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Creación de un usuario de prueba de LockPath Keylight

En esta sección, creará un usuario llamado Britta Simon en LockPath Keylight. LockPath Keylight admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Se crea un nuevo usuario al acceder a LockPath Keylight en caso de que el usuario todavía no exista. 

>[!NOTE]
>Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de cliente de LockPath Keylight](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a LockPath Keylight para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a LockPath Keylight, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **LockPath Keylight**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de LockPath Keylight en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación LockPath Keylight. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

