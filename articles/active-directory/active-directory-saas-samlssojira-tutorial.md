---
title: "Tutorial: Integración de Azure Active Directory con SAML SSO for Jira by resolution GmbH | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Jira by resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: cde5983710185d1e46a5601b16bbfb1c0fcae382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Jira by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Jira by resolution GmbH con Azure Active Directory (Azure AD).

La integración de SAML SSO for Jira by resolution GmbH con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SAML SSO for Jira by resolution GmbH.
- Puede permitir que los usuarios inicien sesión automáticamente en SAML SSO for Jira by resolution GmbH (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAML SSO for Jira by resolution GmbH, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de SAML SSO for Jira by resolution GmbH

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAML SSO for Jira by resolution GmbH desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Adición de SAML SSO for Jira by resolution GmbH desde la galería
Para configurar la integración de SAML SSO for Jira by resolution GmbH en Azure AD, debe agregar SAML SSO for Jira by resolution GmbH desde la galería a la lista de aplicaciones administradas de SaaS.

**Para agregar SAML SSO for Jira by resolution GmbH desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **SAML SSO for Jira by resolution GmbH**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. En el panel de resultados, seleccione **SAML SSO for Jira by resolution GmbH** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAML SSO for Jira by resolution GmbH para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAML SSO for Jira by resolution GmbH.

Para establecer la relación de vínculo, en SAML SSO for Jira by resolution GmbH, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH, se deben completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAML SSO for Jira by resolution GmbH](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**: para tener un homólogo de Britta Simon en SAML SSO for Jira by resolution GmbH que esté vinculado a la representación del usuario de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SAML SSO for Jira by resolution GmbH.

**Para configurar el inicio de sesión único de Azure AD con SAML SSO for Jira by resolution GmbH, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **SAML SSO for Jira by resolution GmbH**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. En la sección **SAML SSO for Jira by resolution GmbH Domain and URLs** (Dominio y direcciones URL de SAML SSO for Jira by resolution GmbH), si quiere configurar la aplicación en modo iniciado por **IdP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.

4. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de clientes de SAML SSO for Jira by resolution GmbH](https://www.resolution.de/go/support) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. En otra ventana del explorador web, inicie sesión en el **Portal de administración de SAML SSO for Jira by resolution GmbH** como administrador.

8. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. Se le redirigirá a la página de acceso de administrador. Escriba la **Contraseña** y haga clic en el botón **Confirmar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. En la sección de la pestaña Complementos, haga clic en **Find new add-ons** (Buscar nuevos complementos). Busque **SAML Single Sign On (SSO) for JIRA** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. Se iniciará la instalación del complemento. Haga clic en **Cerrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. Haga clic en **Administrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. En la página **SAML SingleSignOn Plugin Configuration** (Configuración del complemento SingleSignOn de SAML), haga clic en el botón **Add additional Identity Provider** (Agregar proveedor de identidad adicional) para configurar las opciones del proveedor de identidades.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. Siga los pasos indicados en esta página:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon5.png)
 
    a. Agregue el **nombre** del proveedor de identidades (p. ej., Azure AD).
    
    b. Agregue la **descripción** del proveedor de identidades (p. ej., Azure AD).

    c. Haga clic en **XML** y seleccione el archivo **Metadata** que ha descargado en Azure Portal.

    d. Haga clic en el botón **Cargar**.

    e. Lee los metadatos de IdP y rellena los campos tal y como se resalta en la captura de pantalla. 

16. Haga clic en el botón **Guardar configuración** para guardar la configuración.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/addon6.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Creación del usuario de prueba de SAML SSO for Jira by resolution GmbH

Para permitir que los usuarios de Azure AD inicien sesión en SAML SSO for Jira by resolution GmbH, deben estar aprovisionados en SAML SSO for Jira by resolution GmbH.  
En SAML SSO for Jira by resolution GmbH, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de SAML SSO for Jira by resolution GmbH como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Agregar empleado](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. Se le redirigirá a la página de acceso de administrador para especificar la **contraseña** y haga clic en el botón **Confirmar**.

    ![Agregar empleado](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. En la sección de la pestaña **Administración de usuarios**, haga clic en **Crear usuario**.

    ![Agregar empleado](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. En la página del cuadro de diálogo **"Create New User"** (Crear nuevo usuario), realice los pasos siguientes:

    ![Agregar empleado](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.

    e. Haga clic en **Crear usuario**.   

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAML SSO for Jira by resolution GmbH.

![Asignar usuario][200] 

**Para asignar a Britta Simon a SAML SSO for Jira by resolution GmbH, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAML SSO for Jira by resolution GmbH**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono SAML SSO for Jira by resolution GmbH en el Panel de acceso, su sesión debería iniciarse automáticamente en la aplicación SAML SSO for Jira by resolution GmbH.
Para más información sobre el Panel de acceso, vea la [introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png

