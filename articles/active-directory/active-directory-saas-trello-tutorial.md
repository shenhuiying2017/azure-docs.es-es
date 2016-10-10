<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Trello | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Trello."
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
	ms.date="09/26/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con Trello

En este tutorial, obtendrá información sobre cómo integrar Trello con Azure Active Directory (Azure AD).

Integrar Trello con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Trello.
- Puede permitir que los usuarios inicien sesión automáticamente en Trello (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Trello, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en **Trello**


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Trello desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Trello desde la galería
Para configurar la integración de Trello en Azure AD, deberá agregar Trello desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Trello desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Trello**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_01.png)

7. En el panel de resultados, seleccione **Trello** y luego haga clic en **Completar** para agregar la aplicación.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Trello con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Trello para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de Trello. Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Fuse. Para configurar y probar el inicio de sesión único de Azure AD con Trello, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Trello](#creating-a-the-funding-portal-test-user)**: para tener un homólogo de Britta Simon en Trello que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Trello.

La aplicación de Trello espera que las aserciones SAML contengan un atributo denominado "email". Configure los siguientes atributos para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_03.png)

**Para configurar el inicio de sesión único de Azure AD con Trello, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Trello**, en el menú de la parte superior, haga clic en **Atributos**.
     
    ![Configurar inicio de sesión único][5]


2. En el cuadro de diálogo **Atributos de token de SAML**, para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:
    

	| Nombre del atributo | Valor de atributo |
	| --- | --- |    
    | User.Email | user.mail |
	| User.FirstName | user.givenname |
	| User.LastName | user.surname |

	a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_05.png)
	
	b. En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para la fila.
	
	c. En la lista **Valor de atributo**, seleccione el valor de atributo que se muestra para esa fila.
	
	d. Haga clic en **Complete**. Haga clic en **Aplicar cambios** en la parte inferior de la página.

3. En el menú de la parte superior, haga clic en **Inicio rápido**.

	![Configurar inicio de sesión único][6]

4. En el portal clásico, en la página de integración de aplicaciones de **Trello**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][7]

5. En la página **¿Cómo desea que los usuarios inicien sesión en Trello?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y después haga clic en **Siguiente**.
 	
	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_06.png)

6. En la página de diálogo **Configurar las opciones de la aplicación**, si quiere configurar la aplicación en el **modo iniciado por el proveedor de identidades**, siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_07.png)


    a. En el cuadro de texto URL de respuesta, escriba una dirección URL con el siguiente patrón: `https://trello.com/auth/saml/consume/<enterprise>`.

	b. Haga clic en **Siguiente**.

> [AZURE.NOTE] Debe obtener el campo de datos dinámico **<enterprise>** desde Trello. Si no tiene el valor del campo de datos dinámico, póngase en contacto con el equipo de soporte Trello <support@trello.com> para obtenerlo.

6. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **Mostrar la configuración avanzada (opcional)** y después escriba la **URL de inicio de sesión**:

	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_08.png)

	a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://trello.com/auth/saml/consume/<enterprise>`

	b. Haga clic en **Siguiente**.

7. En la página **Configuración de inicio de sesión único en Trello**, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.

	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_09.png)

6. Con el fin de que se configure el inicio de sesión único para la aplicación, vaya a la página de [configuración de SSO empresarial de Trello](https://trello.com/sso-configuration) para enviar la URL de inicio de sesión al equipo Trello y adjuntar el certificado descargado.

7. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
	
	![Inicio de sesión único de Azure AD][10]

8. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
  	
	![Inicio de sesión único de Azure AD][11]

### Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
	
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
	
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png)

4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Next**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Complete**.



### Creación de usuario de prueba de Trello

En esta sección, creará un usuario llamado Britta Simon en Trello. En esta sección, creará un usuario llamado Britta Simon en Trello. Trello admite el aprovisionamiento Just-In-Time y se creará una nueva cuenta la primera vez que inicie una sesión en Azure AD.

### Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Trello.

![Asignar usuario][200]

**Para asignar Britta Simon a Trello, realice los pasos siguientes:**

1. En el portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Trello**.

	![Configurar inicio de sesión único](./media/active-directory-saas-trello-tutorial/tutorial_trello_10.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Todos los usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]


### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Trello en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Trello.

## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-trello-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-trello-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-trello-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-trello-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-trello-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-trello-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-trello-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0928_2016-->