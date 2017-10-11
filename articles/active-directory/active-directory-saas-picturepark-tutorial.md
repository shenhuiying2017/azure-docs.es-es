---
title: "Tutorial: Integración de Azure Active Directory con Picturepark | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Picturepark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 1c009aa1fdd3140a4466cf762b6c9687e74ce4c7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integración de Azure Active Directory con Picturepark

En este tutorial, obtendrá información sobre cómo integrar Picturepark con Azure Active Directory (Azure AD).

La integración de Picturepark con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Picturepark.
- Puede permitir que los usuarios inicien sesión automáticamente en Picturepark (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Picturepark, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Picturepark

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Picturepark desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-picturepark-from-the-gallery"></a>Incorporación de Picturepark desde la galería
Para configurar la integración de Picturepark en Azure AD, será preciso que agregue Picturepark desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Picturepark desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Picturepark**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. En el panel de resultados, seleccione **Picturepark** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Picturepark con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Picturepark para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Picturepark.

Para establecer la relación de vínculo, en Picturepark, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Picturepark, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Picturepark](#creating-a-picturepark-test-user)**: el objetivo es tener un homólogo de Britta Simon en Picturepark que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Picturepark.

**Para configurar el inicio de sesión único de Azure AD con Picturepark, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Picturepark**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. En la sección **Dominio y direcciones URL de Picturepark**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.picturepark.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de Picturepark](https://picturepark.com/about/contact/) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Picturepark**, haga clic en **Configurar Picturepark** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía Picturepark como administrador.

8. En la barra de herramientas de la parte superior, haga clic en **Herramientas administrativas** y en **Consola de administración**.
   
    ![Consola de administración](./media/active-directory-saas-picturepark-tutorial/ic795062.png "Consola de administración")

9. Haga clic en **Autenticación** y en **Proveedores de identidades**.
   
    ![Autenticación](./media/active-directory-saas-picturepark-tutorial/ic795063.png "Autenticación")

10. En la sección de **Configuración del proveedor de identidades** , realice los pasos siguientes:
   
    ![Configuración del proveedor de identidades](./media/active-directory-saas-picturepark-tutorial/ic795064.png "Configuración del proveedor de identidades")
   
    a. Haga clic en **Agregar**.
  
    b. Escriba un nombre para su configuración.
   
    c. Seleccione **Establecer como predeterminado**.
   
    d. En el cuadro de texto **Issuer URI** (URI de emisor), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.
   
    e. En el cuadro de texto **Trusted Issuer Thumb Print** (Huella digital del emisor de confianza), pegue el valor de **huella digital** que copió de la sección **Certificado de firma SAML**. 

11. Haga clic en **JoinDefaultUsersGroup**.

12. Para establecer el atributo **Emailaddress** en el cuadro de texto **Notificación**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` y haga clic en **Guardar**.

      ![Configuración](./media/active-directory-saas-picturepark-tutorial/ic795065.png "Configuración")

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-picturepark-test-user"></a>Creación de un usuario de prueba de Picturepark

Para permitir que los usuarios de Azure AD inicien sesión en Picturepark, deben aprovisionarse en Picturepark. En el caso de Picturepark, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Picturepark** .

2. En la barra de herramientas de la parte superior, haga clic en **Herramientas administrativas** y en **Usuarios**.
   
    ![Usuarios](./media/active-directory-saas-picturepark-tutorial/ic795067.png "Usuarios")

3. En la pestaña de **información general de los usuarios**, haga clic **Nuevo**.
   
    ![Administración de usuarios](./media/active-directory-saas-picturepark-tutorial/ic795068.png "Administración de usuarios")

4. En el cuadro de diálogo **Crear usuario**, siga estos pasos para un usuario válido de Azure Active Directory que desea aprovisionar:
   
    ![Creación de usuarios](./media/active-directory-saas-picturepark-tutorial/ic795069.png "Creación de usuarios")
   
    a. En el cuadro de texto **Dirección de correo electrónico**, escriba la **dirección de correo electrónico** del usuario **BrittaSimon@contoso.com**.  
   
    b. En los cuadros de texto **Contraseña** y **Confirmar contraseña**, escriba la **contraseña** de BrittaSimon. 
   
    c. En el cuadro de texto **Nombre**, escriba el **nombre** de la usuaria **Britta**. 
   
    d. En el cuadro de texto **Apellido**, escriba el **apellido** de la usuaria **Simon**.
   
    e. En el cuadro de texto **Empresa**, escriba el **nombre de la empresa** del usuario. 
   
    f. En el cuadro de texto **País**, seleccione el **país** del usuario.
  
    g. En el cuadro de texto **ZIP** (Código postal), escriba el **código postal** de la ciudad.
   
    h. En el cuadro de texto **Ciudad**, escriba el **nombre de la ciudad** del usuario.

    i. Seleccione un valor en **Language**(Idioma).
   
    j. Haga clic en **Crear**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Picturepark ofrecida por Picturepark para aprovisionar cuentas de usuario de Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Picturepark.

![Asignar usuario][200] 

**Para asignar Britta Simon a Picturepark, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Picturepark**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Picturepark en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Picturepark. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png

