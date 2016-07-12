<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Printix | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Printix."
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
	ms.date="06/13/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Printix

En este tutorial, obtendrá información sobre cómo integrar Printix con Azure Active Directory (Azure AD).

La integración de Printix con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Printix.
- Puede permitir que los usuarios inicien sesión automáticamente en Printix (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Printix, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Printix


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Printix desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Printix desde la galería
Para configurar la integración de Printix en Azure AD, deberá agregar Printix desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Printix desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]
2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Printix**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_01.png)
7. En el panel de resultados, seleccione **Printix** y luego haga clic en **Completar** para agregar la aplicación.



##  Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Printix con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Printix para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Printix.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Printix.

Para configurar y probar el inicio de sesión único de Azure AD con Printix, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Printix](#creating-a-printix-test-user)**: para tener un homólogo de Britta Simon en Printix que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Printix.


**Para configurar el inicio de sesión único de Azure AD con Printix, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Printix**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
	 
	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Printix?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_03.png)

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_04.png)

    a. En el cuadro de texto **URL de respuesta**, escriba `https://auth.printix.net/saml/SSO`.
	
	b. Haga clic en **Siguiente**.
 
4. En la página **Configurar inicio de sesión único en Printix**, siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_05.png)

    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Inicie la sesión en el inquilino de Printix como administrador.


6. En el menú de la parte superior, haga clic en el icono situado en la esquina superior derecha y seleccione "**Authentication**" (Autenticación).

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_06.png)

7. En la pestaña **Setup** (Configuración), seleccione **Enable Azure/Office 365 authentication** (Habilitar la autenticación de Azure y Office 365).

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_07.png)

8. En la pestaña **Azure**, escriba la dirección URL de metadatos de federación en el cuadro de texto de "**Federation metadata document**" (Documento de metadatos de federación).
	
	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_08.png)

	a. Adjunte el archivo xml de metadatos que descargó en el paso 4 al equipo de soporte técnico de Printix mediante "**support@printix.net**". Ellos cargarán el archivo y le proporcionarán una dirección URL de metadatos de federación.


9. Haga clic en el botón "**Test**" (Probar) y, si la prueba se ha realizado correctamente, en el botón "**OK**" (Aceptar).

	a. Después de hacer clic en el botón **Test** (Probar) se mostrará la página de Azure Active Directory. El mensaje "The test was successful" (La prueba se realizó correctamente) aquí significa que después de escribir las credenciales de su cuenta de prueba de Azure se mostrará el mensaje "Settings tested OK" (Configuración probada correctamente). A continuación, haga clic en el botón **OK** (Aceptar).

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_09.png)


10. Haga clic en el botón **Save** (Guardar) en la página "**Authentication**" (Autenticación).


11. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
	
	![Inicio de sesión único de Azure AD][10]

12. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
 
	![Inicio de sesión único de Azure AD][11]


### Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.


![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_04.png)

5. En la página del cuadro de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
	
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de Printix

El objetivo de esta sección es crear un usuario llamado Britta Simon en Printix. Printix admite el aprovisionamiento just-in-time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Printix se creará un nuevo usuario, en caso de que no exista. [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de Printix.


### Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Printix.

![Asignar usuario][200]

**Para asignar a Britta Simon a Printix, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Printix**.

	![Configurar inicio de sesión único](./media/active-directory-saas-printix-tutorial/tutorial_printix_50.png)

3. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

4. En la lista Usuarios, seleccione **Britta Simon**.

5. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]


### Prueba del inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Printix en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Printix.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-printix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-printix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-printix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-printix-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-printix-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-printix-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-printix-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-printix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-printix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-printix-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-printix-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-printix-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-printix-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0629_2016-->