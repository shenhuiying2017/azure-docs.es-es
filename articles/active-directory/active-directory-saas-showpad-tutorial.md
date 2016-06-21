<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Showpad | Microsoft Azure"
	description="Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Showpad."
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
	ms.date="06/06/2016"
	ms.author="jeedes"/>


# Tutorial: integración de Azure Active Directory con Showpad

El objetivo de este tutorial es mostrar cómo integrar Showpad con Azure Active Directory (Azure AD).

Integrar Showpad con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Showpad.
- Puede permitir que los usuarios inicien sesión automáticamente en Showpad (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Showpad, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de Showpad


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Showpad desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Showpad desde la galería
Para configurar la integración de Showpad en Azure AD, deberá agregar Showpad desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Showpad desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

	![Aplicaciones][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
 
	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Showpad**.

	![Aplicaciones](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_01.png)

7. En el panel de resultados, seleccione **Showpad** y, luego, haga clic en **Completar** para agregar la aplicación.

	![Aplicaciones](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Showpad con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Showpad para un usuario de Azure AD. Es decir, hay que establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Showpad.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Showpad.

Para configurar y probar el inicio de sesión único de Azure AD con Showpad, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Showpad](#creating-a-showpad-test-user)**: para tener un homólogo de Britta Simon en Showpad que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Showpad.



**Para configurar el inicio de sesión único de Azure AD con Showpad, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Showpad**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Showpad?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_03.png)

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_04.png)


    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de Showpad con el siguiente patrón: `https://<company name>.showpad.biz/login`.

	b. En el cuadro de texto **Identificador**, escriba la dirección URL con el siguiente patrón: `https://<company name>.showpad.biz`.

	c. Haga clic en **Siguiente**.


4. En la página **Configurar inicio de sesión único en Showpad**, lleve a cabo estos pasos y luego haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_05.png)

    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Inicie la sesión en el inquilino de Showpad como administrador.

6. En el menú de la parte superior, haga clic en **Configuración**.

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_001.png)

7. Vaya a **Inicio de sesión único** y haga clic en **Habilitar**.
 
	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_002.png)

8. En el cuadro de diálogo **Agregar un servicio de SAML 2.0**, realice los pasos siguientes:

	![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_003.png)

	a. En el cuadro de texto **Nombre**, escriba el nombre del proveedor de identidades (por ejemplo, el nombre de la compañía).

	b. Como **origen de metadatos**, seleccione **XML**.

	c. Copie el contenido del archivo XML de metadatos descargado y, a continuación, péguelo en el cuadro de texto **XML de metadatos**.

	d. Seleccione **Auto-provision accounts for new users when they log in** (Aprovisionar cuentas automáticamente para nuevos usuarios cuando inicien sesión).

	e. Haga clic en **Enviar**.


10. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][10]


11. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
  
	![Inicio de sesión único de Azure AD][11]






### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba de Showpad en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_03.png)

4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_05.png)

    a. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    b. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. Como **Role** (Rol), seleccione **User** (Usuario).

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.


### Creación de usuarios de prueba de Showpad

El objetivo de esta sección es crear un usuario llamado Britta Simon en Showpad.

Showpad admite el aprovisionamiento Just-In-Time. Ya lo ha habilitado en **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**.

No hay ningún elemento de acción para usted en esta sección.




### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Showpad.

![Asignar usuario][200]

**Para asignar Britta Simon a Showpad, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en el menú de la parte superior, haga clic en **Aplicaciones**.

	![Asignar usuario][201]

2. En la lista de aplicaciones, haga clic en **Showpad**.

	![Configurar inicio de sesión único](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_50.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]




### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de **Showpad** del panel de acceso, debería iniciar sesión automáticamente en su aplicación Showpad.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0608_2016-->