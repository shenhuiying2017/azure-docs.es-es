---
title: "Tutorial: Integración de Azure Active Directory con Salesforce | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: ed127afbca5135ade21f6ac53d18d46e88939fd9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integración de Azure Active Directory con Salesforce

En este tutorial, aprenderá a integrar Salesforce con Azure Active Directory (Azure AD).

La integración de Salesforce con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Salesforce.
- Puede permitir que los usuarios inicien sesión automáticamente en Salesforce (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Salesforce, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Salesforce

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Salesforce desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Adición de Salesforce desde la galería
Para configurar la integración de Salesforce en Azure AD, deberá agregar Salesforce desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Salesforce desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Salesforce**, seleccione **Salesforce** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Salesforce en la lista de resultados](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Salesforce con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Salesforce para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Salesforce.

Para establecer la relación de vínculo, asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Salesforce.

Para configurar y probar el inicio de sesión único de Azure AD con Salesforce, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Salesforce](#create-a-salesforce-test-user)**: el objetivo es tener un homólogo de Britta Simon en Salesforce que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Salesforce.

**Para configurar el inicio de sesión único de Azure AD con Salesforce, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Salesforce** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. En la sección **Dominio y direcciones URL de Salesforce**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón:
    
    Cuenta de empresa: `https://<subdomain>.my.salesforce.com`

    Cuenta de desarrollador: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. En el cuadro de texto **Identificador**, escriba el valor con el siguiente patrón:
    
    Cuenta de empresa: `https://<subdomain>.my.salesforce.com`

    Cuenta de desarrollador: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de Salesforce](https://help.salesforce.com/support) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Salesforce**, haga clic en **Configurar Salesforce** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Salesforce](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce.

8. Haga clic en **Setup** (Configuración) en el **icono de configuración** de la esquina superior derecha de la página.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/configure1.png)

9. Desplácese hacia abajo hasta **SETTINGS** (CONFIGURACIÓN) en el panel de navegación y haga clic en **Identity** (Identidad) para expandir la sección relacionada. A continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

10. En la página **Configuración de inicio de sesión único**, haga clic en el botón **Editar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Si no puede habilitar la configuración de inicio de sesión único para su cuenta de Salesforce, puede que necesite ponerse en contacto con el [equipo de soporte técnico de Salesforce](https://help.salesforce.com/support). 

11. Seleccione **SAML habilitado** y haga clic en **Guardar**.

      ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
12. Para establecer la configuración de inicio de sesión único de SAML, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

13. En la página **Edición de la configuración de inicio de sesión único de SAML** , realice las siguientes configuraciones:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. En el campo **Nombre** , escriba un nombre descriptivo para esta configuración. Si se proporciona un valor para **Name** (Nombre), se completa automáticamente el cuadro de texto **API Name** (Nombre de API).

    b. En el campo **Issuer** (Emisor), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que haya copiado de Azure Portal.

    c. En el **Cuadro de texto de identificador de entidades**, escriba el nombre de dominio de Salesforce con el siguiente patrón:
      
      * Cuenta de empresa: `https://<subdomain>.my.salesforce.com`
      * Cuenta de desarrollador: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Para cargar el **certificado de proveedor de identidades**, haga clic en **Choose File** (Elegir archivo) para navegar y seleccionar el archivo de certificado que haya descargado de Azure Portal.

    e. En **SAML Identity Type** (tipo de identidad SAML), elija una de las siguientes opciones:
    
      * Seleccione **Assertion contains the User's Salesforce username** (La aserción contiene el nombre del usuario en Salesforce) si el nombre de usuario en Salesforce se pasa en la aserción SAML.

      * Seleccione **Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto de usuario) si el id. de federación del objeto de usuario se pasa en la aserción SAML.

      * Seleccione **Assertion contains the Use ID from the User object** (La aserción contiene el id. de uso del objeto de usuario) si el id. de federación del objeto de usuario se pasa en la aserción SAML.

    f. En **SAML Identity Location** (Ubicación de identidad de SAML), seleccione **Identity is in the NameIdentifier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción de asunto).

    g. En **Service Provider Initiated Request Binding** (Vinculación de solicitud iniciada del proveedor de servicios), seleccione **HTTP Redirect** (Redirección HTTP).

    h. En el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión del proveedor de identidades), pegue el valor de **Dirección URL del servicio de inicio de sesión único** que haya copiado de Azure Portal.
    
    i. Por último, haga clic en **Guardar** para aplicar la configuración de inicio de sesión único de SAML.

14. En el panel de navegación izquierdo de Salesforce, haga clic en **Company Settings** (Configuración de la empresa) para expandir la sección relacionada y haga clic en **My Domain** (Mi dominio).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

15. Desplácese hacia abajo hasta la sección **Authentication Configuration** (Configuración de autenticación) y haga clic en el botón **Edit** (Editar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

16. En la sección **Authentication Configuration** (Configuración de autenticación), seleccione **Login Page** (Página de inicio de sesión) como **Authentication Service** (Servicio de autenticación) de su configuración del inicio de sesión único de SAML y haga clic en **Save** (Guardar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Si se selecciona más de un servicio de autenticación, cuando los usuarios intentan realizar un inicio de sesión único para el entorno Salesforce, se les pedirá que seleccionen el servicio de autenticación con el que les gustaría iniciar sesión. Si no desea que esto ocurra, **deje sin activar todos los demás servicios de autenticación**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-salesforce-test-user"></a>Creación de un usuario de prueba de Salesforce

En esta sección, creará un usuario llamado a Britta Simon en Salesforce. Salesforce admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.
No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe aún en Salesforce, se crea uno nuevo cuando se intenta acceder a esta aplicación.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Salesforce.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Salesforce, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Salesforce**.

    ![Vínculo de Salesforce en la lista de aplicaciones](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Salesforce en el Panel de acceso, iniciará sesión automáticamente en la aplicación Salesforce.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png

