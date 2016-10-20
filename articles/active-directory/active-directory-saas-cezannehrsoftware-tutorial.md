<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con el software Cezanne HR | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software Cezanne HR."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con el software Cezanne HR

El objetivo de este tutorial es mostrar cómo integrar el software Cezanne HR con Azure Active Directory (Azure AD).

La integración del software Cezanne HR con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso al software Cezanne HR.
- Puede permitir que los usuarios inicien sesión automáticamente en el software Cezanne HR (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con el software Cezanne HR, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para el inicio de sesión único en el software Cezanne HR


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición del software Cezanne HR desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición del software Cezanne HR desde la galería
Para configurar la integración del software Cezanne HR en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar el software Cezanne HR desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
	
	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
	
	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Software Cezanne HR**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. En el panel de resultados, seleccione **Software Cezanne HR** y haga clic en **Completar** para agregar la aplicación.

	![Selección de la aplicación en la galería](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con el software Cezanne HR con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo en el software Cezanne HR de un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado del software Cezanne HR.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en el software Cezanne HR.

Para configurar y probar el inicio de sesión único de Azure AD con el software Cezanne HR, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba del software Cezanne HR](#creating-a-cezanne-hr-software-test-user)**: para tener en el software Cezanne HR un homólogo de Britta Simon que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación de software Cezanne HR.

**Para configurar el inicio de sesión único de Azure AD con el software Cezanne HR, realice los pasos siguientes:**

1. En el portal clásico, en la página de integración de aplicaciones del **software Cezanne HR**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
	 
	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en el software Cezanne HR?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
    
	![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

	a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. En el cuadro de texto **Identificador**, escriba: `https://w3.cezanneondemand.com/CezanneOnDemand/`.

	c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

	d. Haga clic en **Siguiente**.

	> [AZURE.NOTE] Tenga en cuenta que será preciso que actualice estos valores con los valores reales de URL de inicio de sesión, Identificador y URL de respuesta. Para obtener estos valores, póngase en contacto con el equipo de soporte técnico del software Cezanne HR a través de <mailto:info@cezannehr.com>.

4. En la página **Configurar inicio de sesión único en el software Cezanne HR**, lleve a cabo estos pasos y haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Next**.

5. En otra ventana del explorador web, inicie sesión en el inquilino del software Cezanne HR como administrador.

6. En el panel de navegación de la izquierda, haga clic en **System Setup** (Configuración del sistema). Vaya a **Security Settings** (Configuración de seguridad). Luego, navegue hasta **Single Sign-On Configuration** (Configuración de inicio de sesión único).

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. En el panel **Allow users to log in using the following Single Sign-On (SSO) Service** (Permitir a los usuarios iniciar sesión mediante el siguiente servicio de inicio de sesión único), marque la casilla **SAML 2.0** cuadro y seleccione la opción **Advanced Configuration** (Configuración avanzada) que hay al lado.

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Haga clic en el botón **Add New** (Agregar nuevo).

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Realice los pasos siguientes en la sección **SAML 2.0 IDENTITY PROVIDERS** (PROVEEDORES DE IDENTIDADES SAML 2.0).

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

	a. Escriba el nombre de su proveedor de identidades como **nombre para mostrar**.

	b. En el cuadro de texto **Entity Identifier** (Identificador de entidad), coloque el valor de **Id. de entidad** del Asistente para configuración de aplicaciones de Azure AD.

	c. Cambie el valor de **SAML Binding** (Enlace de SMAL) a 'POST'.

	d. En el cuadro de texto **Security Token Service Endpoint** (Punto de conexión de servicio de token de seguridad), coloque el valor de **Dirección URL del servicio de inicio de sesión único** en el Asistente para configuración de aplicaciones de Azure AD.

	e. Escriba 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' en **User ID Attribute Name** (Nombre de atributo de identificador de usuario).

	f. Para cargar el certificado descargado de Azure AD, haga clic en **Upload** (Cargar).

	g. Haga clic en el botón **Ok** (Aceptar).

10. Haga clic en el botón **Guardar**.

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
    
	![Inicio de sesión único de Azure AD][10]

12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
    
	![Inicio de sesión único de Azure AD][11]



### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
    
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Next**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:
    
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Last Name** (Apellidos), escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.
    
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
    
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba del software Cezanne HR

Para permitir que los usuarios de Azure AD inicien sesión en el software Cezanne HR, tienen que aprovisionarse en él. En el caso del software Cezanne HR, el aprovisionamiento es una tarea manual.

####Para aprovisionar una cuenta de usuario, realice estos pasos:

1.  Inicie sesión en su sitio de la compañía del software Cezanne HR como administrador.

2.  En el panel de navegación de la izquierda, haga clic en **System Setup** (Configuración del sistema). Vaya a **Administrar usuarios**. A continuación, vaya a **Add New User** (Agregar nuevo usuario).

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nuevo usuario")

3.  En la sección **Person Details** (Datos de la persona), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nuevo usuario")

	a. En **Internal User** (Usuario interno), seleccione OFF (Desactivado).

	b. En el cuadro de texto **First Name** (Nombre), escriba **Britta**.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba **Simon**.

	d. En el cuadro de texto **E-mail** (Correo electrónico), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

4.  En la sección **Account Information** (Información de la cuenta), siga estos pasos:

    ![Nuevo usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nuevo usuario")

	a. En el cuadro de texto **Username** (Nombre de usuario), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

	b. En el cuadro de texto **Password** (Contraseña), escriba la contraseña de la cuenta de Britta Simon.

	c. Seleccione **HR Professional** (Profesional de RR.HH.) **Security Role** (Rol de seguridad).

	d. Haga clic en **OK** (Aceptar).

5. Navegue a la pestaña **Single Sign-On** (Inicio de sesión único) y seleccione **Add New** (Agregar nuevo) en el área **SAML 2.0 Identifiers** (Identificadores SAML 2.0).

	![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Usuario")

6. Elija el proveedor de identidades en **Identity Provider** y en el cuadro de texto **User Identifier** (Identificador de usuario), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

	![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Usuario")
	
7. Haga clic en el botón **Guardar**.

	![Usuario](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Usuario")


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon utilice el inicio de sesión único de Azure, para lo que se le concede acceso al software Cezanne HR.
	
![Asignar usuario][200]

**Para asignar un usuario llamado Britta Simon al software Cezanne HR, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
    
	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Software Cezanne HR**.
    
	![Configurar inicio de sesión único](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. En el menú de la parte superior, haga clic en **Usuarios**.
    
	![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.
    
	![Asignar usuario][205]

### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.
 
Al hacer clic en el icono Software Cezanne HR en el panel de acceso, debería iniciar sesión automáticamente en la aplicación de software Cezanne HR.

## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->