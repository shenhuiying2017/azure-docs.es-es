---
title: "Tutorial: Integración de Azure Active Directory con Replicon | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Replicon."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 130e13b0bb801c498f6de6e4bfd61bd3d2c3bf00
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Tutorial: Integración de Azure Active Directory con Replicon

En este tutorial, obtendrá información sobre cómo integrar Replicon con Azure Active Directory (Azure AD).

La integración de Replicon con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Replicon.
- Puede permitir que los usuarios inicien sesión automáticamente en Replicon (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Replicon, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Replicon

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Replicon desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-replicon-from-the-gallery"></a>Agregar Replicon desde la galería
Para configurar la integración de Replicon en Azure AD, es preciso agregar Replicon desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Replicon desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Replicon**, seleccione **Replicon** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Replicon en la lista de resultados](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Replicon con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Replicon para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Replicon.

Para establecer la relación de vínculo, en Replicon, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Replicon, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Replicon](#create-a-replicon-test-user)**: para tener un homólogo de Britta Simon en Replicon que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Replicon.

**Para configurar el inicio de sesión único de Azure AD con Replicon, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Replicon**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_samlbase.png)

3. En la sección **Dominio y direcciones URL de Replicon**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Replicon](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://global.replicon.com/<companyname>`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://global.replicon.com/!/saml2/<companyname>/sso/post`.

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte de cliente de Replicon](https://www.replicon.com/customerzone/contact-support) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-replicon-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de Replicon de la compañía.

7. Lleve a cabo los siguientes pasos para configurar SAML 2.0:
   
    ![Habilitar la autenticación SAML](./media/active-directory-saas-replicon-tutorial/ic777805.png "Habilitar la autenticación SAML")
    
    a. Para mostrar el cuadro de diálogo **EnableSAML Authentication2** (Habilitar SAML Authentication2), agregue lo siguiente a la URL después de la clave de su compañía: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
    
    * Lo siguiente muestra el esquema de la dirección URL completa:  
   `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
   
   b. Haga clic en **+** para expandir la sección **v20Configuration**.

   c. Haga clic en **+** para expandir la sección **metaDataConfiguration**.

   d. Haga clic en **Elegir archivo** para seleccionar el archivo XML de metadatos del proveedor de identidades y haga clic en **Enviar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-replicon-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-replicon-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-replicon-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-replicon-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-replicon-test-user"></a>Crear un usuario de prueba de Replicon

Para permitir que los usuarios de Azure AD inicien sesión en Replicon, deben aprovisionarse en Replicon. En el caso de Replicon, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. En una ventana del explorador web, inicie sesión en como administrador en el sitio de Replicon de la compañía.

2. Vaya a **Administración \> Usuarios**.
   
    ![Usuarios](./media/active-directory-saas-replicon-tutorial/ic777806.png "Usuarios")

3. Haga clic en **+Agregar usuario**.
   
    ![Agregar usuario](./media/active-directory-saas-replicon-tutorial/ic777807.png "Agregar usuario")

4. En la sección **Perfil de usuario** , lleve a cabo estos pasos:
   
    ![Perfil de usuario](./media/active-directory-saas-replicon-tutorial/ic777808.png "Perfil de usuario")
    
    a. En el cuadro de texto **Nombre de inicio de sesión**, escriba la dirección de correo electrónico del usuario de Azure AD que quiera aprovisionar, como **BrittaSimon@contoso.com**.
    
    b. En **Tipo de autenticación,** seleccione **SSO**.
    
    c. En el cuadro de texto **Departamento** , escriba el departamento del usuario.

    d. En **Tipo de empleado**, seleccione **Administrador**.

    e. Haga clic en **Guardar perfil de usuario**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Replicon que proporcione Replicon para aprovisionar cuentas de usuario de Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Replicon.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Replicon, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Replicon**.

    ![Vínculo a Replicon en la lista de aplicaciones](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Replicon en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Replicon.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_203.png

