---
title: "Tutorial: Integración de Azure Active Directory con SAML SSO for Bamboo by resolution GmbH | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Bamboo by resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: c677161defb04091566ae608c3edfb9f25af801b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Bamboo by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Bamboo by resolution GmbH con Azure Active Directory (Azure AD).

La integración de SAML SSO for Bamboo by resolution GmbH con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SAML SSO for Bamboo by resolution GmbH.
- Puede permitir que los usuarios inicien sesión automáticamente en SAML SSO for Bamboo by resolution GmbH (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAML SSO for Bamboo by resolution GmbH, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en SAML SSO for Bamboo by resolution GmbH

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAML SSO for Bamboo by resolution GmbH desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adición de SAML SSO for Bamboo by resolution GmbH desde la galería
Para configurar la integración de SAML SSO for Bamboo by resolution GmbH en Azure AD, debe agregar esta aplicación desde la galería a la lista de aplicaciones administradas de SaaS.

**Para agregar SAML SSO for Bamboo by resolution GmbH desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **SAML SSO for Bamboo by resolution GmbH**, seleccione **SAML SSO for Bamboo by resolution GmbH** en el panel de resultados y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![SAML SSO for Bamboo by resolution GmbH en la lista de resultados](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con SAML SSO for Bamboo by resolution GmbH con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAML SSO for Bamboo by resolution GmbH para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAML SSO for Bamboo by resolution GmbH.

Para establecer la relación de vínculo, en SAML SSO for Bamboo de Resolution GmbH, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SAML SSO for Bamboo de Resolution GmbH, se deben completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAML SSO for Bamboo de Resolution GmbH](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**: para tener un homólogo de Britta Simon en SAML SSO for Bamboo de Resolution GmbH que esté vinculado a la representación de este usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SAML SSO for Bamboo by resolution GmbH.

**Para configurar el inicio de sesión único de Azure AD con SAML SSO for Bamboo by resolution GmbH, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **SAML SSO for Bamboo by resolution GmbH**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlbase.png)

3. En la sección **Dominio y direcciones URL de SAML SSO for Bamboo by resolution GmbH**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por IDP:

    ![Dominio y direcciones URL de SAML SSO for Bamboo by resolution GmbH: información de inicio de sesión](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Dominio y direcciones URL de SAML SSO for Bamboo by resolution GmbH: información de inicio de sesión](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de atención al cliente de SAML SSO for Bamboo by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-bamboo-tutorial/tutorial_general_400.png)

7. Inicie sesión en el sitio de empresa de SAML SSO for Bamboo by resolution GmbH como administrador.

8. En el lado derecho de la barra de herramientas principal, haga clic en **Settings** >  (Configuración) **Add-ons** (Complementos).

    ![La configuración](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_setings.png)

9. Vaya a la sección SECURITY (SEGURIDAD) y haga clic en **SAML SingleSignOn** (Inicio de sesión único de SAML) en la barra de menús.

    ![El inicio de sesión único de SAML](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlsingle.png)

10. En la página **SAML SIngleSignOn Plugin Configuration** (Configuración de complementos de inicio de sesión único de SAML), haga clic en **Add idp** (Agregar IdP). 

    ![Add idp (Agregar IdP)](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addidp.png)

11. En la página **Choose your SAML Identity Provider** (Elegir el proveedor de identidades SAML), siga estos pasos:

    ![El proveedor de identidades](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Para **Idp Type** (Tipo de IdP), elija **AZURE AD**.

    b. En el cuadro de texto **Name** (Nombre), escriba un nombre.

    c. En el cuadro de texto **Description** (Descripción), escriba la descripción.

    d. Haga clic en **Siguiente**.

12. En la página **Identity provider configuration** (Configuración del proveedor de identidades), haga clic en el botón **Next** (Siguiente).

    ![La configuración de identidad](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityconfig.png)

13.  En la página **Import SAML Idp Metadata** (Importar metadatos de IdP de SAML), haga clic en **Load File** (Cargar archivo) para cargar el archivo **METADATA XML** que ha descargado de Azure Portal.

    ![Los metadatos de IdP](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

14. Haga clic en **Siguiente**.

15. Haga clic en **Save settings** (Guardar configuración).

    ![La operación de guardar](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-bamboo-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-bamboo-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-bamboo-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-bamboo-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Creación del usuario de prueba de SAML SSO for Bamboo by resolution GmbH

El objetivo de esta sección es crear un usuario llamado Britta Simon en SAML SSO for Bamboo by resolution GmbH. SAML SSO for Bamboo by resolution GmbH admite aprovisionamiento Just-In-Time y creación manual de usuarios, según sus necesidades. Para más información póngase en contacto con el [equipo de atención al cliente de SAML SSO for Bamboo by resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo cual le concederá acceso a SAML SSO for Bamboo by resolution GmbH.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a SAML SSO for Bamboo by resolution GmbH, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAML SSO for Bamboo by resolution GmbH**.

    ![El vínculo de SAML SSO for Bamboo by resolution GmbH en la lista de aplicaciones](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de SAML SSO for Bamboo by resolution GmbH en el Panel de acceso, debería iniciar sesión automáticamente en dicha aplicación.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_203.png

