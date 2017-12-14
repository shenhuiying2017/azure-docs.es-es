---
title: "Tutorial: Integración de Azure Active Directory con SAML SSO for Confluence by resolution GmbH | Documentos de Microsoft"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y SAML SSO for Confluence by resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 70c01e2ee5d97ed5d09e9281c69f1110b5c220da
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integración de Azure Active Directory con SAML SSO for Confluence by resolution GmbH

En este tutorial, aprenderá a integrar SAML SSO for Confluence by resolution GmbH con Azure Active Directory (Azure AD).

La integración de SAML SSO for Confluence by resolution GmbH con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a SAML SSO for Confluence by resolution GmbH.
- Puede permitir que los usuarios inicien sesión automáticamente en SAML SSO for Confluence by resolution GmbH (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con SAML SSO for Confluence by resolution GmbH, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de SAML SSO for Confluence by resolution GmbH

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de SAML SSO for Confluence by resolution GmbH desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adición de SAML SSO for Confluence by resolution GmbH desde la galería

Para configurar la integración de SAML SSO for Confluence by resolution GmbH en Azure AD, debe agregar SAML SSO for Confluence by resolution GmbH desde la galería a la lista de aplicaciones administradas de SaaS.

**Para agregar SAML SSO for Confluence by resolution GmbH desde la galería, siga los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **SAML SSO for Confluence by resolution GmbH**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

5. En el panel de resultados, seleccione **SAML SSO for Confluence by resolution GmbH** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo de SAML SSO for Confluence by resolution GmbH para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de SAML SSO for Confluence by resolution GmbH.

Para establecer la relación de vínculo, en SAML SSO for Confluence by resolution GmbH, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH, se deben completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de SAML SSO for Confluence by resolution GmbH](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)**: para tener un homólogo de Britta Simon en SAML SSO for Confluence by resolution GmbH que esté vinculado a la representación del usuario de Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación SAML SSO for Confluence by resolution GmbH.

**Para configurar el inicio de sesión único de Azure AD con SAML SSO for Confluence by resolution GmbH, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **SAML SSO for Confluence by resolution GmbH**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

3. En la sección **SAML SSO for Confluence by resolution GmbH Domain and URLs** (Dominio y direcciones URL de SAML SSO for Confluence by resolution GmbH), si quiere configurar la aplicación en modo iniciado por **IdP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.

4. Active **Mostrar configuración avanzada de URL**. Si quiere volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<server-base-url>/plugins/servlet/samlsso`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de clientes de SAML SSO for Confluence by resolution GmbH](https://www.resolution.de/go/support) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_400.png)  
    
7. En otra ventana del explorador web, inicie sesión en el **Portal de administración de SAML SSO for Confluence by resolution GmbH** como administrador.

8. Mantenga el mouse encima del icono de engranaje y haga clic en **Complementos**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon1.png)

9. Se le redirigirá a la página Acceso de administrador. Escriba la contraseña y haga clic en el botón **Confirmar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon2.png)

10. En la pestaña **ATLASSIAN MARKETPLACE** (MARKETPLACE DE ATLASSIAN), haga clic en **Find new add-ons** (Buscar nuevos complementos). 

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon.png)

11. Busque **SAML Single Sign On (SSO) for Confluence** y haga clic en el botón **Instalar** para instalar el nuevo complemento SAML.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon7.png)

12. Se iniciará la instalación del complemento. Haga clic en **Cerrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon8.png)

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon9.png)

13. Haga clic en **Administrar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon10.png)
    
14. Haga clic en **Configurar** para configurar el nuevo complemento.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon11.png)

15. Este nuevo complemento también puede encontrarse en la pestaña **USERS & SECURITY** (USUARIOS Y SEGURIDAD).

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon3.png)
    
16. En la página **SAML SingleSignOn Plugin Configuration** (Configuración del complemento SingleSignOn de SAML), haga clic en el botón **Add new IdP** (Agregar IdP nuevo) para configurar los valores del proveedor de identidades.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon4.png)

17. En la página **Choose your SAML Identity Provider** (Elegir el proveedor de identidades SAML), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon5a.png)
 
    a. Establezca **Azure AD** como el tipo de IdP.
    
    b. Agregue el **nombre** del proveedor de identidades (p. ej., Azure AD).
    
    c. Agregue la **descripción** del proveedor de identidades (p. ej., Azure AD).
    
    d. Haga clic en **Siguiente**.
    
18. En la página **Identity provider configuration** (Configuración del proveedor de identidades), haga clic en el botón **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon5b.png)

19. En la página **Import SAML IdP Metadata** (Importar los metadatos del IdP de SAML), siga los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon5c.png)

    a. Haga clic en el botón **Cargar archivo** y seleccione el archivo XML de metadatos que descargó en el paso 5.

    b. Haga clic en el botón **Importar**.
    
    c. Espere un momento hasta que la importación se realice correctamente.
    
    d. A continuación, haga clic en el botón **Siguiente**.
    
20. En la página **User ID attribute and transformation** (Transformación y atributo del id. de usuario), haga clic en el botón **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon5d.png)
    
21. En la página **User creation and update** (Creación y actualización de usuarios), haga clic en **Guardar y siguiente** para guardar la configuración.   
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon6a.png)
    
22. En la página **Test your settings** (Probar su configuración), haga clic en **Skip test & configure manually** (Omitir la prueba y configurar manualmente) para omitir la prueba de usuario por ahora. Esto se realizará en la sección siguiente y requiere la configuración de algunos valores en Azure Portal. 
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon6b.png)
    
23. En el cuadro que aparece con el texto **Skipping the test means...** (Omitir la prueba significa...), haga clic en **Aceptar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/addon6c.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Creación del usuario de prueba de SAML SSO for Confluence by resolution GmbH

Para permitir que los usuarios de Azure AD inicien sesión en SAML SSO for Confluence by resolution GmbH, deben estar aprovisionados en SAML SSO for Confluence by resolution GmbH.  
En SAML SSO for Confluence by resolution GmbH, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la empresa de SAML SSO for Confluence by resolution GmbH como administrador.

2. Mantenga el mouse encima del icono de engranaje y haga clic en **Administración de usuarios**.

    ![Agregar empleado](./media/active-directory-saas-samlssoconfluence-tutorial/user1.png) 

3. En la sección Usuarios, haga clic en la pestaña **Agregar usuarios**. En la página del cuadro de diálogo **Agregar un usuario**, realice los siguientes pasos:

    ![Agregar empleado](./media/active-directory-saas-samlssoconfluence-tutorial/user2.png) 

    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Britta Simon.

    b. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario, por ejemplo, Britta Simon.

    c. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

    d. En el cuadro de texto **Contraseña**, escriba la contraseña de Britta Simon.

    e. Haga clic en **Confirmar contraseña** y vuelva a escribir la contraseña.
    
    f. Haga clic en el botón **Agregar**.    

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a SAML SSO for Confluence by resolution GmbH.

![Asignar usuario][200] 

**Para asignar a Britta Simon a SAML SSO for Confluence by resolution GmbH, siga los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **SAML SSO for Confluence by resolution GmbH**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono SAML SSO for Confluence by resolution GmbH en el Panel de acceso, su sesión debería iniciarse automáticamente en la aplicación SAML SSO for Confluence by resolution GmbH.
Para más información sobre el Panel de acceso, vea la [introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_203.png

