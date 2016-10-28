<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Novatus | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y LearnUpon."
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
	ms.date="08/15/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con LearnUpon

El objetivo de este tutorial es mostrar cómo integrar LearnUpon con Azure Active Directory (Azure AD). La integración de LearnUpon con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a LearnUpon.
- Puede permitir que los usuarios inicien sesión automáticamente en LearnUpon (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory clásico.


Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con LearnUpon, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en LearnUpon


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de LearnUpon desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de LearnUpon desde la galería
Para configurar la integración de LearnUpon en Azure AD, es preciso agregar LearnUpon desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar LearnUpon desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **LearnUpon**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_01.png)

7. En el panel de resultados, seleccione **LearnUpon** y haga clic en **Completar** para agregar la aplicación.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con LearnUpon con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de LearnUpon para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de LearnUpon. Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en LearnUpon.

Para configurar y probar el inicio de sesión único de Azure AD con LearnUpon, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de LearnUpon](#creating-a-learnupon-test-user)**: para tener un homólogo de Britta Simon en LearnUpon que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación LearnUpon.



**Para configurar el inicio de sesión único de Azure AD con LearnUpon, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de la aplicación **LearnUpon**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en LearnUpon?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_03.png)

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_04.png)


    a. En el cuadro de texto **URL de respuesta**, escriba la URL del Servicio de consumidor de aserciones con el siguiente patrón: `https://<companyname>.learnupon.com/saml/consumer`

    b. Haga clic en **Next**.


4. En la página **Configurar inicio de sesión único en LearnUpon**, siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_05.png)

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo. Necesitaremos este certificado y las direcciones URL de metadatos (Id. de entidad, Dirección URL de inicio de sesión SSO y Dirección URL de cierre de sesión) para configurar el SSO en LearnUpon.

    b. Haga clic en **Siguiente**.




1. Abra otra instancia del explorador e inicie sesión en LearnUpon con una cuenta de administrador.

1. Haga clic en la pestaña **settings** (Configuración).

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)


1. Haga clic en **Single Sign On - SAML** (Inicio de sesión único - SAML) y, después, en **General Settings** (Configuración general) para configurar SAML.

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png)


5. En la sección **General Settings** (Configuración general), siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)

	a. Seleccione **Habilitado**.

	b. En **Version** (Versión), seleccione **2.0**.

	c. En **Skip conditions** (Omitir condiciones), seleccione **No**.

	d. En el cuadro de texto **SAML Token Post param name** (Nombre de parámetro POST de token SAML), escriba el nombre del parámetro POST de la solicitud en la dirección URL del consumidor de SAML indicada anteriormente que contenga la aserción SAML que se va a comprobar y autenticar (por ejemplo, **SAMLResponse**).

	e. En el cuadro de texto **Name Identifier Format** (Formato de identificador de nombre), escriba el valor que indica en qué lugar de la aserción SAML reside el identificador del usuario (dirección de correo electrónico) (por ejemplo, **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**).

	f. En el cuadro de texto **Identify Provider Location** (Identificar ubicación de proveedor), escriba el valor que indica el lugar al que se envían los usuarios si hacen clic en el icono cargado desde la pantalla de inicio de sesión del Portal de Azure clásico.

	g. En el Portal de Azure clásico, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Sign out URL** (Dirección URL de cierre de sesión).

	h. Haga clic en **Manage finger prints** (Administrar huellas dactilares) y, a continuación, cargue la huella digital del certificado descargado.


1. Haga clic en **User Settings** (Configuración del usuario) y siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)

	a. En el cuadro de texto **First Name Identifier Format** (Formato de identificador de nombre), escriba el valor que nos indica el lugar de la aserción SAML en que reside el nombre de los usuarios (por ejemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**).

	b. En el cuadro de texto **Last Name Identifier Format** (Formato de identificador de apellidos), escriba el valor que nos indica el lugar de la aserción SAML en que residen los apellidos de los usuarios (por ejemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**).


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.

	![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png)

4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Next**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de LearnUpon

El objetivo de esta sección es crear un usuario llamado Britta Simon en LearnUpon. LearnUpon admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a LearnUpon se creará un nuevo usuario, si aún no existe. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear un usuario manualmente, es preciso que se ponga contacto con el equipo de soporte técnico de LearnUpon.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a LearnUpon.

![Asignar usuario][200]

**Para asignar Britta Simon a LearnUpon, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **LearnUpon**.

	![Configurar inicio de sesión único](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_50.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso. Al hacer clic en el icono de LearnUpon en el panel de acceso, debería iniciar sesión automáticamente en su aplicación LearnUpon.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->