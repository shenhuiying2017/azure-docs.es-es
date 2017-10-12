---
title: "Tutorial: Integración de Azure Active Directory con Salesforce | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Salesforce."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Tutorial: Integración de Azure Active Directory con Salesforce

En este tutorial, aprenderá a integrar Salesforce con Azure Active Directory (Azure AD).

La integración de Salesforce con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Salesforce.
- Puede permitir que los usuarios inicien sesión automáticamente en Salesforce (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Salesforce, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Salesforce

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Salesforce desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Adición de Salesforce desde la galería
Para configurar la integración de Salesforce en Azure AD, deberá agregar Salesforce desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Salesforce desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Salesforce**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. En el panel de resultados, seleccione **Salesforce** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Salesforce con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Salesforce para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Salesforce.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Salesforce.

Para configurar y probar el inicio de sesión único de Azure AD con Salesforce, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Ssalesforce](#creating-a-salesforce-test-user)**: el objetivo es tener un homólogo de Britta Simon en Salesforce que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Salesforce.

**Para configurar el inicio de sesión único de Azure AD con Salesforce, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Salesforce** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. En la sección **Dominio y direcciones URL de Salesforce**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: 
   * Cuenta de empresa: `https://<subdomain>.my.salesforce.com`
   * Cuenta de desarrollador: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Salesforce](https://help.salesforce.com/support) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Salesforce**, haga clic en **Configurar Salesforce** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**. 

    ![Configuración del inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Abra una nueva pestaña en el explorador e inicie sesión en su cuenta de administrador de Salesforce.

8.  En el panel de navegación **Administrador**, haga clic en **Controles de seguridad** para expandir la sección relacionada. A continuación, haga clic en **Configuración de inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  En la página **Configuración de inicio de sesión único**, haga clic en el botón **Editar**.
    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Si no puede habilitar la configuración de inicio de sesión único para su cuenta de Salesforce, puede que necesite ponerse en contacto con el [equipo de soporte técnico de Salesforce](https://help.salesforce.com/support). 

10. Seleccione **SAML habilitado** y haga clic en **Guardar**.

      ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Para establecer la configuración de inicio de sesión único de SAML, haga clic en **Nuevo**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. En la página **Edición de la configuración de inicio de sesión único de SAML** , realice las siguientes configuraciones:

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. En el campo **Nombre** , escriba un nombre descriptivo para esta configuración. Si se proporciona un valor para **Name** (Nombre), se completa automáticamente el cuadro de texto **API Name** (Nombre de API).

    b. Pegue el valor **SMAL Entity ID** (Id. de entidad de SAML) en el campo **Issuer** (Emisor) de Salesforce.

    c. En el **Cuadro de texto de identificador de entidades**, escriba el nombre de dominio de Salesforce con el siguiente patrón:
      
      * Cuenta de empresa: `https://<subdomain>.my.salesforce.com`
      * Cuenta de desarrollador: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Haga clic en **Browse** (Examinar) o **Choose File** (Elegir archivo) para abrir el cuadro de diálogo **Choose File to Upload** (Elegir archivos para cargar), seleccione el certificado de Salesforce y haga clic en **Open** (Abrir) para cargar el certificado.

    e. En **SAML Identity Type** (Tipo de identidad de SAML), seleccione **Assertion contains User's salesforce.com username** (La aserción contiene el nombre de usuario de salesforce.com del usuario).

    f. En **SAML Identity Location** (Ubicación de identidad de SAML), seleccione **Identity is in the NameIdentifier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción de asunto).

    g. Pegue la **URL de servicio de inicio de sesión** en el campo **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades) de Salesforce.
    
    h. En **Service Provider Initiated Request Binding** (Vinculación de solicitud iniciada del proveedor de servicios), seleccione **HTTP Redirect** (Redirección HTTP).
    
    i. Por último, haga clic en **Guardar** para aplicar la configuración de inicio de sesión único de SAML.

13. En el panel de navegación izquierdo de Salesforce, haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y haga clic en **My Domain** (Mi dominio).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Desplácese hacia abajo hasta la sección **Authentication Configuration** (Configuración de autenticación) y haga clic en el botón **Edit** (Editar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. En la sección **Authentication Service** (Servicio de autenticación), seleccione el nombre descriptivo de la configuración de SSO de SAML y haga clic en **Save** (Guardar).

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Si se selecciona más de un servicio de autenticación, cuando los usuarios intentan realizar un inicio de sesión único para el entorno Salesforce, se les pedirá que seleccionen el servicio de autenticación con el que les gustaría iniciar sesión. Si no desea que esto ocurra, **deje sin activar todos los demás servicios de autenticación**.
<CE>    
> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada mediante la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Documentación insertada sobre Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-salesforce-test-user"></a>Creación de un usuario de prueba de Salesforce

En esta sección, creará un usuario llamado a Britta Simon en Salesforce. Salesforce admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.
No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe aún en Salesforce, se crea uno nuevo cuando se intenta acceder a esta aplicación.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Salesforce.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Salesforce, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Salesforce**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

Para probar la configuración de inicio de sesión único, abra el Panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/) y, a continuación, inicie sesión en la cuenta de prueba y haga clic en **Salesforce**.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-salesforce-provisioning-tutorial.md)

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

