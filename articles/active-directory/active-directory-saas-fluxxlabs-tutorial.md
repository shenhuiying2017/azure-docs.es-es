---
title: 'Tutorial: integración de Azure Active Directory con Fluxx Labs | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 5288ae3deaf82e76accb9c9584c250c7dbe2c9ca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Tutorial: integración de Azure Active Directory con Fluxx Labs

En este tutorial, obtendrá información sobre cómo integrar Fluxx Labs con Azure Active Directory (Azure AD).

La integración de Fluxx Labs con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a Fluxx Labs.
- Puede permitir que los usuarios inicien sesión automáticamente en Fluxx Labs (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Fluxx Labs se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Fluxx Labs

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Fluxx Labs desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adición de Fluxx Labs desde la galería
Para configurar la integración de Fluxx Labs en Azure AD, será preciso que agregue Fluxx Labs desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Fluxx Labs desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Fluxx Labs**, seleccione **Fluxx Labs** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Fluxx Labs en la lista de resultados](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Fluxx Labs con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Fluxx Labs para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Fluxx Labs.

Para establecer la relación de vínculo, en Fluxx Labs, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Fluxx Labs, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Fluxx Labs](#create-a-fluxx-labs-test-user)**: para tener un homólogo de Britta Simon en Fluxx Labs que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Fluxx Labs.

**Para configurar el inicio de sesión único de Azure AD con Fluxx Labs, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Fluxx Labs**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. En la sección **Dominio y direcciones URL de Fluxx Labs**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

    | Environment | Patrón de dirección URL|
    |-------------|------------|
    | Producción | `https://<subdomain>.fluxx.io` |
    | Preproducción | `https://<subdomain>.preprod.fluxxlabs.com`|
    | Ensayo    | `https://<subdomain>.stage.fluxxlabs.com`|
    
    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:

    | Environment | Patrón de dirección URL|
    |-------------|------------|
    | Producción | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Preproducción | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
    | Ensayo    | `https://<subdomain>.stage.fluxxlabs.com/auth/saml/callback`|
    
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Fluxx Labs](mailto:travis@fluxxlabs.com) para obtener estos valores.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Fluxx Labs**, haga clic en **Configurar Fluxx Labs** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Fluxx Labs como administrador.

8. En la esquina superior derecha de la página, haga clic en **Settings icon** (Icono de configuración) y, después, en **Admin** (Administración).

    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. En el panel de administración, seleccione **Plug-ins** (Complementos) > **Integrations** (Integraciones) y, a continuación, seleccione **SAML SSO-(Enabled)** [SAML SSO-(Habilitado)]

    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. En la sección de atributos, realice los siguientes pasos:
    
    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Seleccione la casilla de verificación **SAML SSO** (SSO de SAML).

    b. En el cuadro de texto **Request Path** (Ruta de acceso de la solicitud), escriba **/auth/saml**.

    c. En el cuadro de texto **Callback Path** (Ruta de acceso de devolución de llamada), escriba **/auth/saml/callback**.

    d. En el cuadro de texto **Assertion Consumer Service Url** (Dirección URL del servicio de consumidor de aserciones), pegue el valor de **URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    e. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **SAML Entity ID** (Id. de entidad de SAML) que ha copiado de Azure Portal.

    f. Abra el certificado con codificación Base64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **IDP Cert** (Certificado del IDP).

    g.  En el cuadro de texto **Name identifier Format** (Formato de identificador de nombre), escriba el valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > Una vez guardado el contenido, el campo aparecerá en blanco por seguridad, pero se ha guardado el valor en la configuración.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-fluxx-labs-test-user"></a>Creación de un usuario de prueba de Fluxx Labs

Para permitir que los usuarios de Azure AD inicien sesión en Fluxx Labs, deben aprovisionarse en Fluxx Labs. En el caso de Fluxx Labs, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de Fluxx Labs.

2. En el panel, haga clic en el icono mostrado a continuación para abrir la tarjeta **New PERSON** (Nueva persona).

    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

3. En la sección **NEW PERSON** (Nueva persona), lleve a cabo estos pasos:
    
    ![Configuración de Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. En el cuadro de texto **Login** (Inicio de sesión), escriba el correo electrónico del usuario, por ejemplo, Azure_Admin.

    b. En el cuadro de texto **Password** (Contraseña), escriba la contraseña de la cuenta Azure_Admin.

    c. En el cuadro de texto **Confirm Password** (Confirmar contraseña), escriba la contraseña de la cuenta Azure_Admin.

    d. Fluxx Labs usa la dirección de correo electrónico como identificador único para los inicios de sesión SSO. Rellene el campo **SSO UID** con la dirección de correo electrónico del usuario, que coincide con la dirección de correo electrónico que usan como inicio de sesión con SSO.

    e. Haga clic en **Create Person** (Crear persona).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Fluxx Labs.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Fluxx Labs, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Fluxx Labs**.

    ![Vínculo a Fluxx Labs en la lista de aplicaciones](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Fluxx Labs en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Fluxx Labs.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png

