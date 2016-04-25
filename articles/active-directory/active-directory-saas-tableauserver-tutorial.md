<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Tableau Server | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tableau Server."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Tableau Server

El objetivo de este tutorial es mostrar cómo integrar Tableau Server con Azure Active Directory (Azure AD).

Integrar Tableau Server con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Tableau Server.
- Puede permitir que los usuarios inicien sesión automáticamente en Tableau Server (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Tableau Server, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Tableau Server


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tableau Server desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Tableau Server desde la galería
Para configurar la integración de Tableau Server en Azure AD, será preciso que agregue Tableau Server desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tableau Server desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
 
	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Tableau Server**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)

7. En el panel de resultados, seleccione **Tableau Server** y luego haga clic en **Completar** para agregar la aplicación.

	![Selección de la aplicación en la galería](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Tableau Server con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tableau Server para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tableau Server.

Para establecer esta relación de vínculo, se toma el valor del **nombre de usuario** en Azure AD y se asigna como valor del **Nombre de usuario** en Tableau Server.

Para configurar y probar el inicio de sesión único de Azure AD con Tableau Server, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Tableau Server](#creating-a-tableauserver-test-user)**: para tener un homólogo de Britta Simon en Tableau Server que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Tableau Server.

La aplicación Tableau Server espera las aserciones de SAML en un formato concreto. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png)

**Para configurar el inicio de sesión único de Azure AD con Tableau Server, realice los pasos siguientes:**


1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Tableau Server**, en el menú de la parte superior, haga clic en **Atributos**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png)


1. En el cuadro de diálogo **Atributos de token de SAML**, realice los siguientes pasos:

	

	a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png)


	b. En el cuadro de texto **Nombre de atributo**, escriba **username**.

    c. En la lista **Valor de atributo**, seleccione **user.displayname**.

    d. Haga clic en **Completo**.
	



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)








1. Haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]



2. En la página **¿Cómo desea que los usuarios inicien sesión en Tableau Server?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png)


3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png)



    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL de su servidor Tableau.

	b. En el cuadro Identificador, copie

	c. Haga clic en **Siguiente**.


4. En la página **Configurar inicio de sesión único en Tableau Server**, lleve a cabo estos pasos y haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png)


    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


6. Para configurar SSO para la aplicación, debe iniciar sesión en su inquilino de Tableau Server como administrador.

	a. En la configuración de Tableau Server, haga clic en la pestaña **SAML**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png)


	b. Active la casilla de **Use SAML for single sign-on** (Usar SAML para inicio de sesión único).

	c. Busque el archivo de metadatos de federación descargado desde el Portal de administración de Azure y después cárguelo en el **SAML Idp metadata file** (Archivo de metadatos de Idp SAML).

	d. Tableau Server return URL (URL de retorno de Tableau Server): es la URL a la que accederán los usuarios de Tableau Server, como http://tableau_server. No se recomienda usar http://localhost. No se admiten direcciones URL que tengan una barra diagonal final (por ejemplo, http://tableau_server/). Copie el valor de **Tableau Server return URL** y péguelo en el cuadro de texto **URL de inicio de sesión**, tal como se muestra en el paso 3.

	e. SAML entity ID (Id. de entidad SAML): el identificador de entidad identifica de forma exclusiva la instalación de Tableau Server en el IdP. Aquí, si quiere, puede especificar de nuevo la dirección URL de Tableau Server, pero no tiene que ser esa misma URL. Copie el valor de **SAML entity ID** y péguelo en el cuadro de texto **Identificador** de Azure AD, tal como se muestra en el paso 3.

	f. Haga clic en **Export Metadata File** (Exportar archivo de metadatos) y ábralo en la aplicación de editor de texto. Busque la URL del Servicio de consumidor de aserciones con Http Post e índice 0 y copie la URL. Ahora péguela en el cuadro de texto **URL de respuesta** tal como se muestra en el paso 3.

	g. Haga clic en el botón **Aceptar** de la página de configuración de Tableau Server.

	> [AZURE.NOTE] Si necesita ayuda para configurar SAML en Tableau Server, consulte el artículo [Configuración de SAML](http://onlinehelp.tableau.com/current/server/es-ES/config_saml.htm)

6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
 
	![Inicio de sesión único de Azure AD][11]


### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

En la lista Usuarios, seleccione **Britta Simon**.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png)


4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png)

    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png)


8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Crear un usuario de prueba de Tableau Server

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Tableau Server. Debe aprovisionar todos los usuarios en el servidor de Tableau. Tenga en cuenta también que el nombre de usuario debe coincidir con el valor que ha configurado en el atributo personalizado de **nombre de usuario** de Azure AD. Con la asignación correcta, la integración debería funcionar. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear un usuario manualmente, póngase en contacto con el administrador de Tableau Server de su organización.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Tableau Server.

![Asignar usuario][200]

**Para asignar Britta Simon a Tableau Server, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
 
	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Tableau Server**.

	![Configurar inicio de sesión único](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png)


1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Tableau Server en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tableau Server.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0413_2016-->