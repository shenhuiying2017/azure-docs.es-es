---
title: "Tutorial: integración de Azure Active Directory con el software Cezanne HR | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software Cezanne HR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 3934f814a9060adf275a4bdcc83403da4b2a4075
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integración de Azure Active Directory con el software Cezanne HR

En este tutorial, obtendrá información sobre cómo integrar Cezanne HR Software con Azure Active Directory (Azure AD).

La integración del software Cezanne HR con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al software Cezanne HR.
- Puede permitir que los usuarios inicien sesión automáticamente en el software Cezanne HR (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con el software Cezanne HR, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en el software Cezanne HR

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición del software Cezanne HR desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adición del software Cezanne HR desde la galería
Para configurar la integración del software Cezanne HR en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar el software Cezanne HR desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Cezanne HR Software**, seleccione **Cezanne HR Software** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Cezanne HR Software en la lista de resultados](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Cezanne HR Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo en Cezanne HR Software de un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado del software Cezanne HR.

En Cezanne HR Software, asigne el valor del **nombre de usuario** en Azure AD como valor de **nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con el software Cezanne HR, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba del software Cezanne HR](#create-a-cezannehrsoftware-test-user)**: para tener en Cezanne HR Software un homólogo de Britta Simon que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación de software Cezanne HR.

**Para configurar el inicio de sesión único de Azure AD con el software Cezanne HR, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Cezanne HR Software**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. En la sección **Dominio y direcciones URL de Cezanne HR Software**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. En el cuadro de texto **URL de respuesta**, escriba la siguiente dirección URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de Cezanne HR Software Client](https://cezannehr.com/services/support/).

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Cezanne HR Software**, haga clic en **Configurar Cezanne HR Software** para abrir la ventana **Configurar inicio de sesión**.

    ![Configuración de Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Desplácese hacia abajo hasta la sección **Referencia rápida**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la **sección Referencia rápida**.

    ![Configuración de Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. En otra ventana del explorador web, inicie sesión en el inquilino del software Cezanne HR como administrador.

9. En el panel de navegación de la izquierda, haga clic en **System Setup**(Configuración del sistema). Vaya a **Security Settings**(Configuración de seguridad). Luego, navegue hasta **Single Sign-On Configuration**(Configuración de inicio de sesión único).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. En el panel **Allow users to log in using the following Single Sign-On (SSO) Service** (Permitir a los usuarios iniciar sesión mediante el siguiente servicio de inicio de sesión único), marque la casilla **SAML 2.0** y seleccione la opción **Advanced Configuration** (Configuración avanzada).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Haga clic en el botón **Add New** (Agregar nuevo).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Realice los pasos siguientes en la sección **SAML 2.0 IDENTITY PROVIDERS** (PROVEEDORES DE IDENTIDADES SAML 2.0).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Escriba el nombre de su proveedor de identidades como **nombre para mostrar**.

    b. En el cuadro de texto **Entity Identifier** (Identificador de entidad), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal. 

    c. Cambie el valor de **SAML Binding** (Enlace de SMAL) a 'POST'.

    d. En el cuadro de texto **Security Token Service Endpoint** (Punto de conexión del servicio de token de seguridad), pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    e. En el cuadro de texto User ID Attribute Name (Nombre de atributo de Id. de usuario), escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Para cargar el certificado descargado de Azure Portal, haga clic en **Upload** (Cargar).
    
    g. Haga clic en el botón **Ok** (Aceptar). 

13. Haga clic en el botón **Guardar** .

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Creación de un usuario de prueba de Cezanne HR Software

Para permitir que los usuarios de Azure AD inicien sesión en el software Cezanne HR, tienen que aprovisionarse en él. En el caso del software Cezanne HR, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1.  Inicie sesión en su sitio de la compañía del software Cezanne HR como administrador.

2.  En el panel de navegación de la izquierda, haga clic en **System Setup**(Configuración del sistema). Vaya a **Administrar usuarios**. A continuación, vaya a **Add New User**(Agregar nuevo usuario).

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuevo usuario")

3.  En la sección **Person Details** (Datos de la persona), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuevo usuario")
    
    a. En **Internal User** (Usuario interno), seleccione OFF (Desactivado).
    
    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario, en este caso, **Britta**.  
 
    c. En el cuadro de texto **Apellidos**, escriba el nombre del usuario, en este caso, **Simon**.
    
    d. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.

4.  En la sección **Account Information** (Información de la cuenta), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuevo usuario")
    
    a. En el cuadro de texto **Nombre de usuario**, escriba el correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.
    
    b. En el cuadro de texto **Contraseña**, escriba la contraseña del usuario.
    
    c. Seleccione **HR Professional** (Profesional de RR.HH.) como **Security Role** (Rol de seguridad).
    
    d. Haga clic en **Aceptar**.

5. Navegue a la pestaña **Single Sign-On** (Inicio de sesión único) y seleccione **Add New** (Agregar nuevo) en el área **SAML 2.0 Identifiers** (Identificadores SAML 2.0).

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuario")

6. Elija el proveedor de identidades en **Identity Provider** (Proveedor de identidades) y en el cuadro de texto **User Identifier** (Identificador de usuario), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuario")
    
7. Haga clic en el botón **Guardar** .

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuario")

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Cezanne HR Software.

![Asignación del rol de usuario][200] 

**Para asignar un usuario llamado Britta Simon al software Cezanne HR, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Software Cezanne HR**.

    ![Vínculo de Cezanne HR Software en la lista de aplicaciones](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono Software Cezanne HR en el panel de acceso, debería iniciar sesión automáticamente en la aplicación de software Cezanne HR.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

