<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Ariba | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Ariba."
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
	ms.date="04/26/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Ariba

El objetivo de este tutorial es mostrar cómo integrar Ariba con Azure Active Directory (Azure AD).

Integrar Ariba con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Ariba.
- Puede permitir que los usuarios inicien sesión automáticamente en Ariba (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Ariba, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Ariba


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Ariba desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Ariba desde la galería
Para configurar la integración de Ariba en Azure AD, es preciso agregar Ariba desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Ariba desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
	
	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Ariba**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_01.png)

7. En el panel de resultados, seleccione **Ariba** y haga clic en **Completar** para agregar la aplicación.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Ariba con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Ariba para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Ariba.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **Nombre de usuario** en Ariba.

Para configurar y probar el inicio de sesión único de Azure AD con Ariba, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Ariba](#creating-a-ariba-test-user)**: para tener un homólogo de Britta Simon en Ariba que esté vinculado a su representación en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Ariba.



**Para configurar el inicio de sesión único de Azure AD con Ariba, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Ariba**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Ariba?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_03.png)

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Configurar inicio de sesión único](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_04.png)


    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que utilizan los usuarios para iniciar sesión en la aplicación Ariba con el siguiente patrón: **“https://<companyname>.sourcing.ariba.com"** o **"https://<CompanyName>.supplier.ariba.com”**.

    b. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en Ariba**, siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_05.png)

    a. Haga clic en **Descargar certificado** y después guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para configurar el SSO para su aplicación, póngase en contacto con el equipo de soporte de Ariba mediante el teléfono **1-866-218-2155**.


> [AZURE.NOTE] Asegúrese de que ese nombre de usuario en el sistema de Ariba coincide con el de Azure AD ya que, en caso contrario, no funcionará la integración.


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.

	![Inicio de sesión único de Azure AD][11]



### Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ariba-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de Ariba

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Ariba. Trabaje con el equipo de soporte técnico de Ariba para agregar los usuarios en el sistema.


> [AZURE.NOTE] Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el equipo de soporte técnico de Ariba.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a Ariba.

![Asignar usuario][200]

**Para asignar Britta Simon a Ariba, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Ariba**.

	![Configurar inicio de sesión único](./media/active-directory-saas-ariba-tutorial/tutorial_ariba_50.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Ariba en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Ariba.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-ariba-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0511_2016-->