---
title: "Tutorial: Integración de Azure Active Directory con Salesforce Sandbox | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 16b60342106c69cb9959aad539b794cb8003acda
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integración de Azure Active Directory con Salesforce Sandbox

En este tutorial, aprenderá a integrar Salesforce Sandbox con Azure Active Directory (Azure AD).

La integración de Salesforce Sandbox con Azure AD proporciona las siguientes ventajas:

- Puede controlar quién tiene acceso a Salesforce Sandbox desde Azure AD.
- Puede permitir que los usuarios inicien sesión automáticamente en Salesforce Sandbox (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Salesforce Sandbox, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Salesforce Sandbox

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Salesforce Sandbox desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adición de Salesforce Sandbox desde la galería
Para configurar la integración de Salesforce Sandbox, deberá agregar Salesforce Sandbox desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Salesforce Sandbox desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Salesforce Sandbox**, seleccione **Salesforce Sandbox** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Salesforce Sandbox en la lista de resultados](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Salesforce Sandbox con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Salesforce Sandbox para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Salesforce Sandbox.

Para establecer la relación de vínculo, asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Salesforce Sandbox.

Para configurar y probar el inicio de sesión único de Azure AD con Salesforce Sandbox, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)**: el objetivo es tener un homólogo de Britta Simon en Salesforce Sandbox que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Salesforce Sandbox.

**Para configurar el inicio de sesión único de Azure AD con Salesforce Sandbox, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Salesforce Sandbox** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. En la sección **Dominio y direcciones URL de Salesforce Sandbox**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. En el cuadro de texto **Identificador**, escriba el valor con el siguiente patrón: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Salesforce](https://help.salesforce.com/support) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Salesforce Sandbox**, haga clic en **Configurar Salesforce Sandbox** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce Sandbox.

8. Haga clic en **Setup** (Configuración) en el **icono de configuración** de la esquina superior derecha de la página.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/configure1.png)

9. Desplácese hacia abajo hasta **SETTINGS** (CONFIGURACIÓN) en el panel de navegación y haga clic en **Identity** (Identidad) para expandir la sección relacionada. A continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso.png)

10. Seleccione **SAML habilitado** y haga clic en **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-enable-saml.png)

11. Para establecer la configuración de inicio de sesión único de SAML, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. En la sección Configuración del inicio de sesión único de SAML siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-saml-config.png)

    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración (por ejemplo, *SPSSOWAAD_Test*). 

    b. En el campo **Issuer** (Emisor), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que haya copiado de Azure Portal.

    c. En el cuadro de texto **Entity Id** (Id. de entidad), escriba `https://<instancename>--Sandbox.<entityid>.my.salesforce.com` si se trata de la primera instancia de Salesforce Sandbox que agregará a su directorio. Si ya agregó una instancia de Salesforce Sandbox, para el **Id. de entidad** escriba la **URL de inicio de sesión**, que debe tener este formato: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`  
 
    d. Para cargar el **certificado de proveedor de identidades**, haga clic en **Choose File** (Elegir archivo) para navegar y seleccionar el archivo de certificado que haya descargado de Azure Portal.  

    e. En **SAML Identity Type** (tipo de identidad SAML), elija una de las siguientes opciones:
    
      * Seleccione **Assertion contains the User's Salesforce username** (La aserción contiene el nombre del usuario en Salesforce) si el nombre de usuario en Salesforce se pasa en la aserción SAML.

      * Seleccione **Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto de usuario) si el id. de federación del objeto de usuario se pasa en la aserción SAML.

      * Seleccione **Assertion contains the Use ID from the User object** (La aserción contiene el id. de uso del objeto de usuario) si el id. de federación del objeto de usuario se pasa en la aserción SAML.
 
    f. Como **SAML Identity Location**(Ubicación de identidad de SAML), seleccione **Identity is in the NameIdentifier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción de sujeto).

    g. Para **Service Provider Initiated Request Binding** (Enlace de solicitud iniciada por el proveedor de servicio), seleccione **HTTP POST** (Método HTTP POST). 

    h. En el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión del proveedor de identidades), pegue el valor de **Dirección URL del servicio de inicio de sesión único** que haya copiado de Azure Portal. 

    i. SFDC no admite el cierre de sesión SAML.  Como solución alternativa, pegue `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` en el cuadro de texto **Identity Provider Logout URL** (URL de cierre de sesión del proveedor de identidades).

    j. Haga clic en **Guardar**.

### <a name="enable-your-domain"></a>Habilitación del dominio
En esta sección se supone que ya ha creado un dominio.  Para más información, consulte [Defining Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Definición del nombre de dominio).

**Realice los pasos siguientes para habilitar su dominio:**

1. En el panel de navegación izquierdo de Salesforce, haga clic en **Company Settings** (Configuración de la empresa) para expandir la sección relacionada y haga clic en **My Domain** (Mi dominio).
   
     ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-my-domain.png)
   
   >[!NOTE]
   >Asegúrese de que su dominio se ha configurado correctamente. 

2. En la sección **Authentication Configuration** (Configuración de autenticación), haga clic en**Edit** (Editar), luego, para **Authentication Service** (Servicio de autenticación), seleccione el nombre de la configuración de inicio de sesión único de SAML en la sección anterior y finalmente haga clic en **Save** (Guardar).
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-edit-auth-config.png)

Tan pronto como tenga un dominio configurado, los usuarios deberán usar la dirección URL de dominio para iniciar sesión en el espacio aislado de Salesforce.  

Para obtener el valor de la dirección URL, haga clic en el perfil de SSO que haya creado en la sección anterior.

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-salesforce-sandbox-test-user"></a>Creación de un usuario de prueba de Salesforce Sandbox

En esta sección, creará un usuario llamado a Britta Simon en Salesforce Sandbox. Salesforce Sandbox admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.
No hay ningún elemento de acción para usted en esta sección. Si un usuario ya no existe en Salesforce Sandbox, se crea uno nuevo cuando se intenta acceder a esta aplicación.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Salesforce Sandbox.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Salesforce Sandbox, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Salesforce Sandbox**.

    ![Vínculo de Salesforce Sandbox en la lista de aplicaciones](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Salesforce Sandbox en el Panel de acceso, iniciará sesión automáticamente en la aplicación Salesforce Sandbox.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_203.png

