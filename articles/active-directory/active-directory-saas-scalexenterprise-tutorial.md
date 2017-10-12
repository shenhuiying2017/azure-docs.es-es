---
title: "Tutorial: Integración de Azure Active Directory con ScaleX Enterprise | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ScaleX Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 0ebed0c2605862426384c0e219e52c9d626b6246
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutorial: Integración de Azure Active Directory con ScaleX Enterprise

En este tutorial, aprenderá a integrar ScaleX Enterprise con Azure Active Directory (Azure AD).

La integración de ScaleX Enterprise con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ScaleX Enterprise.
- Puede permitir que los usuarios inicien sesión automáticamente en ScaleX Enterprise (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si quiere conocer más detalles sobre la integración de aplicaciones SaaS con Azure AD, vea: ¿Qué es el acceso a aplicaciones y el inicio de sesión único con [Azure Active Directory](active-directory-appssoaccess-whatis.md)?

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ScaleX Enterprise, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ScaleX Enterprise

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use su entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de ScaleX Enterprise desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adición de ScaleX Enterprise desde la galería
Para configurar la integración de ScaleX Enterprise en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ScaleX Enterprise desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **ScaleX Enterprise**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. En el panel de resultados, seleccione **ScaleX Enterprise** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ScaleX Enterprise utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ScaleX Enterprise para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ScaleX Enterprise.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ScaleX Enterprise.

Para configurar y probar el inicio de sesión único de Azure AD con ScaleX Enterprise, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**: para tener un homólogo de Britta Simon en ScaleX Enterprise que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación ScaleX Enterprise.

**Para configurar el inicio de sesión único de Azure AD con ScaleX Enterprise, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **ScaleX Enterprise**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. En la sección **Dominio y direcciones URL de ScaleX Enterprise**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. En el cuadro de texto **Identificador**, escriba el valor con el siguiente patrón: `https://platform.rescale.com/saml2/<company id>/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://platform.rescale.com/saml2/<company id>/acs/`.

4. Active **Mostrar configuración avanzada de URL**, si desea volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba el valor con el siguiente patrón: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta o URL de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de ScaleX Enterprise](http://info.rescale.com/contact_sales) para obtener estos valores. 

5. La aplicación ScaleX Enterprise espera las aserciones de SAML en un formato específico, que requiere que se modifiquen las asignaciones de atributos personalizados en la configuración de los atributos del token de SAML. Haga clic en **Ver y editar todos los demás atributos de usuario** para abrir la configuración de atributos personalizados.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Haga clic con el botón derecho en el **nombre** del atributo y luego haga clic en Eliminar.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/delete_attribute_name.png)

    b. Haga clic en el atributo **emailaddress** para abrir la ventana Editar atributo. Cambie el valor de **user.mail** a **user.userprincipalname** y haga clic en Aceptar.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/edit_email_attribute.png)   
    
5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de ScaleX Enterprise**, haga clic en **Configurar ScaleX Enterprise** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID** y **SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Para configurar el inicio de sesión único en **ScaleX Enterprise**, regístrese en el sitio web de la compañía de ScaleX Enterprise como administrador.

9. Haga clic en el menú en la esquina superior derecha y seleccione **Contoso Administration** (Administración de Contoso).

    > [!NOTE] 
    > Contoso solo es un ejemplo. Debería tratarse del nombre real de la empresa. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/Test_Admin.png) 

10. Seleccione **Integrations** (Integraciones) en el menú superior y seleccione **Single Sign-On** (Inicio de sesión único).

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/admin_sso.png) 

11. Complete el formulario de la siguiente manera:

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Seleccione **"Create any user who can authenticate with SSO"** (Crear cualquier usuario que se pueda autenticar con SSO).

    b. **Service Provider saml** (SAML del proveedor de servicios): pegue el valor ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***.

    c. **Name of Identity Provider email field in ACS response** (Nombre de campo de correo electrónico del proveedor de identidades en respuestas de ACS): pegue el valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    d. **Identity Provider EntityDescriptor Entity ID** (Id. de entidad EntityDescriptor del proveedor de identidades): pegue el valor del **identificador de entidad en SAML** que ha copiado de Azure Portal.

    e. **Identity Provider SingleSignOnService URL** (Dirección URL del servicio de inicio de sesión único del proveedor de identidades): pegue la **dirección URL del servicio de inicio de sesión único de SAML** de Azure Portal.

    f. **Identity Provider public X509 certificate** (Certificado X509 público del proveedor de identidades): abra el certificado X509 descargado de Azure Portal en el Bloc de notas y pegue el contenido en este cuadro. Asegúrese de que no haya saltos de línea en el medio del contenido del certificado.
    
    g. Active las casillas siguientes: **Enabled, Encrypt NameID y Sign AuthnRequests.** (Habilitado, Cifrar Id. de nombre y Firmar solicitudes de autenticación).

    h. Haga clic en **Update SSO Settings** (Actualizar configuración de SSO) para guardar la configuración.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-scalexenterprise-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Creación de un usuario de prueba de ScaleX Enterprise

Para permitir que los usuarios de Azure AD se registren en ScaleX Enterprise, deben aprovisionarse en ScaleX Enterprise. En el caso de ScaleX Enterprise, el aprovisionamiento es una tarea automática y no se requiere ningún paso manual. Todos los usuarios que se pueden autenticar correctamente con las credenciales de SSO se aprovisionarán automáticamente en ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a ScaleX Enterprise.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a ScaleX Enterprise, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ScaleX Enterprise**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ScaleX Enterprise del panel de acceso, iniciará sesión automáticamente en la aplicación ScaleX Enterprise. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scalexenterprise-tutorial/tutorial_general_203.png

