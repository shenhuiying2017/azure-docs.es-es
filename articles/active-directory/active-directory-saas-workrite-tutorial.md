<properties
	pageTitle="Tutorial: integración de Azure Active Directory con Workrite | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Workrite."
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
	ms.date="08/12/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Workrite

El objetivo de este tutorial es mostrar cómo integrar Workrite con Azure Active Directory (Azure AD). La integración de Workrite con Azure AD proporciona las siguientes ventajas:


- Puede controlar en Azure AD quién tiene acceso a Workrite.
- Puede permitir que los usuarios inicien sesión automáticamente en Workrite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con Workrite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Workrite


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de tres bloques de creación principales:

1. Agregar Workrite desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Agregar Workrite desde la galería
Para configurar la integración de Workrite en Azure AD, deberá agregar Workrite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Workrite desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
 
	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
 
	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Workrite**.

	![Aplicaciones][5]

7. En el panel de resultados, seleccione **Workrite** y haga clic en **Completar** para agregar la aplicación.

	![Aplicaciones][500]


##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Workrite con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Workrite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Workrite. Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Workrite.
 
Para configurar y probar el inicio de sesión único de Azure AD con Workrite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Workrite](#creating-a-halogen-software-test-user)**: para tener un homólogo de Britta Simon en Workrite que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Workrite.

**Para configurar el inicio de sesión único de Azure AD con Workrite, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Workrite**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Workrite?**, seleccione **Inicio de sesión único de Azure AD** y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][7]

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:
	
	![Inicio de sesión único de Azure AD][8]
 
     a. En el cuadro de texto **Dirección URL de inicio de sesión**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de Workrite (p. ej. *https://app.workrite.co.uk/securelogin/samlgateway.aspx?id=1a82b5aa-4dd6-4472-9721-7d0193f59e22*).

     > [AZURE.NOTE] Póngase en contacto con el equipo de soporte de Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk) si no conoce el valor de la URL de inicio de sesión.

     b. Haga clic en **Next**.
 
4. En la página **Configurar inicio de sesión único en Workrite**, siga estos pasos:

	![Inicio de sesión único de Azure AD][9]

    a. Haga clic en Descargar certificado y después guarde el archivo en el equipo.

    b. Póngase en contacto con el equipo de soporte de Workrite [support@workrite.co.uk](mailto:support@workrite.co.uk), proporciónele el certificado descargado, la **dirección URL de emisor** (identificador de entidad), la **dirección URL de servicio de inicio de sesión único**, la **dirección URL de cierre de sesión único** y, finalmente, solicite la instalación de inicio de sesión único para la aplicación Workrite.

    c. Haga clic en **Siguiente**.


6. En el Portal de Azure clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
 
	![Inicio de sesión único de Azure AD][11]




### Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de Azure clásico llamado Britta Simon.

![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_03.png)
 
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Next**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_06.png)
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_07.png)
 
8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-workrite-tutorial/create_aaduser_08.png)
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de Workrite

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en Workrite.

**Para crear una usuaria llamada Britta Simon en Workrite, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de Workrite como administrador.

2. En el panel de navegación, haga clic en **Administrador**.

	![Asignar usuario][400]

3. Vaya a Vínculos rápidos y, a continuación, haga clic en **Crear usuario**.

	![Asignar usuario][401]

4. En el cuadro de diálogo **Crear usuario**, realice los pasos siguientes:

	![Asignar usuario][402]

    a. Escriba los valores de **Correo electrónico**, **Nombre** y **Apellido** de un usuario válido de Azure AD que quiera aprovisionar.

    b. Seleccione **Administrador de cliente** como **Elegir rol**.

    c. Haga clic en **Guardar**.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Workrite.

	![Assign User][200] 

**Para asignar a Britta Simon a Workrite, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Workrite**.

	![Asignar usuario][202]

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]
1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso. Al hacer clic en el icono de Workrite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Workrite.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_01.png
[500]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_05.png

[6]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_02.png
[8]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_03.png
[9]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_04.png
[10]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_07.png
[203]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-workrite-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_400.png
[401]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_401.png
[402]: ./media/active-directory-saas-workrite-tutorial/tutorial_workrite_402.png

<!---HONumber=AcomDC_0817_2016-->