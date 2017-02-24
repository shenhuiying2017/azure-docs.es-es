---
title: "Tutorial: integración de Azure Active Directory con el software Cezanne HR | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software Cezanne HR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9585494ebff68891d374a29e8e3e4b7756914bcc
ms.openlocfilehash: d8a654340df56002e503f2f61e910facb51696c5


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Integración de Azure Active Directory con el software Cezanne HR

En este tutorial, obtendrá información sobre cómo integrar Cezanne HR Software con Azure Active Directory (Azure AD).

La integración del software Cezanne HR con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al software Cezanne HR.
- Puede permitir que los usuarios inicien sesión automáticamente en el software Cezanne HR (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con el software Cezanne HR, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en el software Cezanne HR


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición del software Cezanne HR desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Adición del software Cezanne HR desde la galería
Para configurar la integración del software Cezanne HR en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar el software Cezanne HR desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Software Cezanne HR**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. En el panel de resultados, seleccione **Cezanne HR Software** y haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Cezanne HR Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo en Cezanne HR Software de un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado del software Cezanne HR.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Cezanne HR Software.

Para configurar y probar el inicio de sesión único de Azure AD con el software Cezanne HR, debe completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba del software Cezanne HR](#creating-a-cezanne-hr-software-test-user)** : para tener en el software Cezanne HR un homólogo de Britta Simon que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Cezanne HR Software.

**Para configurar el inicio de sesión único de Azure AD con el software Cezanne HR, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **Cezanne HR Software**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En la página de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. En la sección **Dominio y direcciones URL de Cezanne HR Software**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.
    
    b. En el cuadro de texto **Identificador**, escriba: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Aquí le recomendamos que utilice el valor único de URL en el identificador. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de Cezanne HR Software](mailto:info@cezannehr.com).

4. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una valor en **Fecha de expiración**. Luego haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. En la sección **Certificado de firma de SAML**, seleccione **Make new certificate active** (Activar el nuevo certificado) y haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. En la ventana emergente **Rollover certificate** (Certificado de sustitución), haga clic en **Aceptar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. En la sección **Configuración de Cezanne HR Software**, haga clic en **Configurar Cezanne HR Software** para abrir la ventana **Configurar inicio de sesión**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. En otra ventana del explorador web, inicie sesión en el inquilino del software Cezanne HR como administrador.

11. En el panel de navegación de la izquierda, haga clic en **System Setup**(Configuración del sistema). Vaya a **Security Settings**(Configuración de seguridad). Luego, navegue hasta **Single Sign-On Configuration**(Configuración de inicio de sesión único).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. En el panel **Allow users to log in using the following Single Sign-On (SSO) Service** (Permitir a los usuarios iniciar sesión mediante el siguiente servicio de inicio de sesión único), marque la casilla **SAML 2.0** y seleccione la opción **Advanced Configuration** (Configuración avanzada).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. Haga clic en el botón **Add New** (Agregar nuevo).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. Realice los pasos siguientes en la sección **SAML 2.0 IDENTITY PROVIDERS** (PROVEEDORES DE IDENTIDADES SAML 2.0).

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Escriba el nombre de su proveedor de identidades como **nombre para mostrar**.

    b. En el cuadro de texto **Entity Identifier** (Identificador de entidad), coloque el valor de **ID de identidad de SAML** de la ventana de configuración de aplicaciones de Azure AD.

    c. Cambie el valor de **SAML Binding** (Enlace de SMAL) a 'POST'.

    d. En el cuadro de texto **Security Token Service Endpoint** (Punto de conexión de servicio de token de seguridad), coloque el valor de **Dirección URL del servicio de inicio de sesión único de SAML** de la ventana de configuración de aplicaciones de Azure AD.

    e. Escriba "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" en **User ID Attribute Name**(Nombre de atributo de identificador de usuario).

    f. Para cargar el certificado descargado de Azure AD, haga clic en **Upload** (Cargar).

    g. Haga clic en el botón **Ok** (Aceptar). 

15. Haga clic en el botón **Guardar** .

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)



### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 



### <a name="creating-a-cezanne-hr-software-test-user"></a>Creación de un usuario de prueba del software Cezanne HR

Para permitir que los usuarios de Azure AD inicien sesión en el software Cezanne HR, tienen que aprovisionarse en él. En el caso del software Cezanne HR, el aprovisionamiento es una tarea manual.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1.  Inicie sesión en su sitio de la compañía del software Cezanne HR como administrador.

2.  En el panel de navegación de la izquierda, haga clic en **System Setup**(Configuración del sistema). Vaya a **Administrar usuarios**. A continuación, vaya a **Add New User**(Agregar nuevo usuario).

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuevo usuario")

3.  En la sección **Person Details** (Datos de la persona), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuevo usuario")

    a. En **Internal User** (Usuario interno), seleccione OFF (Desactivado).

    b. En el cuadro de texto **Nombre**, escriba **Britta**.  

    c. En el cuadro de texto **Apellidos**, escriba **Simon**.

    d. En el cuadro de texto **E-mail** (Correo electrónico), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

4.  En la sección **Account Information** (Información de la cuenta), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuevo usuario")

    a. En el cuadro de texto **Username** (Nombre de usuario), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    b. En el cuadro de texto **Password** (Contraseña), escriba la contraseña de la cuenta de Britta Simon.

    c. Seleccione **HR Professional** (Profesional de RR.HH.) como **Security Role** (Rol de seguridad).

    d. Haga clic en **OK**(Aceptar).

5. Navegue a la pestaña **Single Sign-On** (Inicio de sesión único) y seleccione **Add New** (Agregar nuevo) en el área **SAML 2.0 Identifiers** (Identificadores SAML 2.0).

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuario")

6. Elija el proveedor de identidades en **Identity Provider** (Proveedor de identidades) y en el cuadro de texto **User Identifier** (Identificador de usuario), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuario")
    
7. Haga clic en el botón **Guardar** .

    ![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuario")



### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Cezanne HR Software.

![Asignar usuario][200] 

**Para asignar un usuario llamado Britta Simon al software Cezanne HR, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y seleccione **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Software Cezanne HR**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono Software Cezanne HR en el panel de acceso, debería iniciar sesión automáticamente en la aplicación de software Cezanne HR.


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


<!--HONumber=Feb17_HO1-->


