---
title: "Tutorial: Integración de Azure Active Directory con Work.com | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Work.com."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7aa04faab5da9ee7dae977be3a8c040c3aed0b9c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integración de Azure Active Directory con Work.com

En este tutorial, obtendrá información sobre cómo integrar Work.com con Azure Active Directory (Azure AD).

La integración de Work.com con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Work.com.
- Puede permitir que los usuarios inicien sesión automáticamente en Work.com (Inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Work.com, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Work.com

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Work.com desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-workcom-from-the-gallery"></a>Agregar Work.com desde la galería
Para configurar la integración de Work.com en Azure AD, será preciso que agregue Work.com desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Work.com desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Work.com**, seleccione **Work.com** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Incorporación de la aplicación desde la galería](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Work.com con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Work.com para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Work.com.

Para establecer la relación de vínculo, en Work.com, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Work.com, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Work.com](#create-a-workcom-test-user)**: para tener un homólogo de Britta Simon en Work.com que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Work.com.

>[!NOTE]
>Para configurar el inicio de sesión único, deberá configurar todavía un nombre de dominio personalizado de Work.com. Deberá definir al menos un nombre de dominio, probar su nombre de dominio e implementarlo en toda la organización.

**Para configurar el inicio de sesión único de Azure AD con Work.com, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Work.com**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. En la sección **Dominio y direcciones URL de Work.com**, lleve a cabo los pasos siguientes:

    ![Sección Dominio y direcciones URL de Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `http://<companyname>.my.salesforce.com`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte al cliente de Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Sección Certificado de firma SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Work.com**, haga clic en **Configurar Work.com** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Sección de configuración de Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Inicie sesión en su inquilino de Work.com como administrador.

8. Acceda a **Setup**(Configuración).
   
    ![Instalación](./media/active-directory-saas-work-com-tutorial/ic794108.png "Instalación")

9. En el panel de navegación izquierdo, en la sección **Administer** (Administrar), haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y, luego, haga clic en **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
   
    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/ic767825.png "Mi dominio")

10. Para comprobar que el dominio se configuró correctamente, asegúrese de que está en "**Step 4 Deployed to Users**" (Paso 4 Dominio implementado para usuarios) y revise la sección "**My Domain Settings**" (Mi configuración de dominio).
   
    ![Dominio implementado al usuario](./media/active-directory-saas-work-com-tutorial/ic784377.png "Dominio implementado al usuario")

11. Inicie sesión en su inquilino de Work.com.

12. Acceda a **Setup**(Configuración).
    
    ![Instalación](./media/active-directory-saas-work-com-tutorial/ic794108.png "Instalación")

13. Expanda el menú **Security Controls** (Controles de seguridad) y luego haga clic en **Single Sign-On Settings** (Configuración de inicio de sesión único).
    
    ![Configuración de inicio de sesión único](./media/active-directory-saas-work-com-tutorial/ic794113.png "Configuración de inicio de sesión único")

14. En la página del cuadro de diálogo **Single Sign-On Settings** (Configuración de inicio de sesión único), siga estos pasos:
    
    ![SAML habilitado](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML habilitado")
    
    a. Seleccione **SAML habilitado**.
    
    b. Haga clic en **Nuevo**.

15. En la sección **SAML Single Sign-On Settings** (Configuración del inicio de sesión único de SAML), siga estos pasos:
    
    ![Inicio de sesión único SAML](./media/active-directory-saas-work-com-tutorial/ic794114.png "Inicio de sesión único SAML")
    
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.  
       
    > [!NOTE]
    > Si se proporciona un valor para **Name** (Nombre), el cuadro de texto **API Name** (Nombre de API) se completa automáticamente.
    
    b. En el cuadro de texto **Emisor**, pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal.
    
    c. Para cargar el certificado descargado de Azure Portal, haga clic en **Browse** (Examinar).
    
    d. En el cuadro de texto **Id. de identidad**, escriba `https://salesforce-work.com`.
    
    e. Como **SAML Identity Type** (Tipo de identidad SAML), seleccione **Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto Usuario).
    
    f. Para **///SAML Identity Location** (Ubicación de identidad SAML), seleccione **///Identity is in the NameIdentfier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción Subject).
    
    g. En el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión del proveedor de identidades), pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML**, que ha copiado de Azure Portal.

    h. En el cuadro de texto **Identity Provider Logout URL** (Dirección URL de cierre de sesión del proveedor de identidades), pegue el valor de **dirección URL de cierre de sesión**, que ha copiado de Azure Portal.
    
    i. Para **Service Provider Initiated Request Binding** (Enlace de solicitud iniciada por el proveedor de servicio), seleccione **HTTP Post** (Método HTTP Post).
    
    j. Haga clic en **Save**.

16. En el portal de Work.com, en el panel de navegación izquierdo, haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y luego haga clic en la página **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
    
    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/ic794115.png "Mi dominio")

17. En la página **My domain** (Mi dominio), en la sección **Login Page Branding** (Personalización de marca de la página de inicio de sesión), haga clic en **Edit** (Editar).
    
    ![Personalización de marca de página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/ic767826.png "Personalización de marca de página de inicio de sesión")

14. En la página **Login Page Branding** (Personalización de marca de la página de inicio de sesión), en la sección **Authentication Service** (Servicio de autenticación), se muestra el nombre de su **SAML SSO Settings** (Configuración de SSO de SAML). Selecciónelo y luego haga clic en **Save**(Guardar).
    
    ![Personalización de marca de página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/ic784366.png "Personalización de marca de página de inicio de sesión")

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Usuarios y grupos -> Todos los usuarios](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Sumar](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Página del cuadro de diálogo Usuario](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-workcom-test-user"></a>Creación de un usuario de prueba de Work.com
Para que los usuarios de Azure Active Directory puedan iniciar sesión, deben aprovisionarse a Work.com. En el caso de Work.com, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de Work.com como administrador.

2. Acceda a **Setup**(Configuración).
   
    ![Instalación](./media/active-directory-saas-work-com-tutorial/IC794108.png "Instalación")
3. Vaya a **Manage Users \> (Administrar usuarios) Users** (Usuarios).
   
    ![Administración de usuarios](./media/active-directory-saas-work-com-tutorial/IC784369.png "Administración de usuarios")

4. Haga clic en **Nuevo usuario**.
   
    ![Todos los usuarios](./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos los usuarios")

5. En la sección Edición de usuarios, realice los pasos siguientes, en los atributos de una cuenta de Azure AD válida que desee aprovisionar en los cuadros de texto relacionados:
   
    ![Edición de usuarios](./media/active-directory-saas-work-com-tutorial/ic794118.png "Edición de usuarios")
   
    a. En el cuadro de texto **Nombre**, escriba el **nombre** del usuario **Britta**.
    
    b. En el cuadro de texto **Apellido**, escriba el **apellido** del usuario **Simon**.
    
    c. En el cuadro de texto **Alias**, escriba el **nombre** del usuario **Britta**.
    
    d. En el cuadro de texto **Correo electrónico**, escriba la **dirección de correo electrónico** del usuario, **Brittasimon@contoso.com**.
    
    e. En el cuadro de texto **Nombre de usuario**, escriba un nombre de usuario como **Brittasimon@contoso.com**.
    
    f. En el cuadro de texto **Sobrenombre**, escriba un **sobrenombre** del usuario, como **Simon**.
    
    g. Seleccione **Role** (Rol), **User License** (Licencia de usuario) y **Profile** (Perfil).
    
    h. Haga clic en **Guardar**.  
      
    > [!NOTE]
    > El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Work.com.

![Asignar usuario][200] 

**Para asignar el usuario Britta Simon a Work.com, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Work.com**.

    ![Work.com en la lista de la aplicación](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Work.com en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Work.com.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png

