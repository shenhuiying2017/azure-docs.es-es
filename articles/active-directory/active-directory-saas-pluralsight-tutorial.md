<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Pluralsight | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pluralsight."
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
	ms.date="05/04/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Pluralsight

El objetivo de este tutorial es mostrar cómo integrar Pluralsight con Azure Active Directory (Azure AD).

Integrar Pluralsight con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Pluralsight.
- Puede permitir que los usuarios inicien sesión automáticamente en Pluralsight (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.


Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Pluralsight, se necesitan los siguientes elementos:

- Una suscripción de Azure
- Una suscripción habilitada para el inicio de sesión único en Pluralsight


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Pluralsight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Incorporación de Pluralsight desde la galería
Para configurar la integración de Pluralsight en Azure AD, deberá agregar Pluralsight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Pluralsight desde la galería, siga estos pasos:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.
 
    ![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Pluralsight**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_01.png)

7. En el panel de resultados, seleccione **Pluralsight** y luego haga clic en **Completar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_06.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Pluralsight con una usuaria de prueba llamada "Britta Simon".

Para configurar y probar el inicio de sesión único de Azure AD con Pluralsight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Pluralsight](#creating-a-pluralsight-test-user)**: para tener un homólogo de Britta Simon en Pluralsight que esté vinculado a la representación de esta en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Pluralsight.

La aplicación Pluralsight espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_02.png)

También puede agregar el atributo **"Id. exclusivo"** con el valor correspondiente como EmployeeID o cualquier otro aspecto que se adapte a su organización. Tenga en cuenta que esto no es el atributo obligatorio, pero puede agregarlo para identificar el usuario único.

**Para configurar el inicio de sesión único de Azure AD con Pluralsight, siga estos pasos:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Pluralsight**, en el menú de la parte superior, haga clic en **Atributos**.

	![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_81.png)



1. En el cuadro de diálogo **Atributos de token de SAML**, realice los siguientes pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/2829.png)


    a. Para cada atributo de usuario en el cuadro rojo de la tabla anterior, mantenga el puntero sobre el atributo y, a continuación, haga clic en Eliminar.




1. Para agregar los **atributos de token de SAML** requeridos, para cada fila mostrada en la tabla siguiente, realice los pasos que se indican a continuación:

	| Nombre del atributo | Valor de atributo |
	| --- | --- |    
	| Nombre de usuario| user.givenname |
    | Apellidos | user.surname |
	| Email | user.mail |

	a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

	![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_82.png)


	b. En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para esa fila.

    c. En la lista **Valor de atributo**, seleccione el valor de atributo que se muestra para esa fila.

    d. Haga clic en **Completo**.
	


1. Haga clic en **Aplicar cambios**.

	![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/3232.png)



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

	![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_general_83.png)









1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Pluralsight**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Pluralsight?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_03.png)

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_04.png)


    a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de Pluralsight con el siguiente patrón: `https://<instance name>.pluralsight.com/sso/<comapny name>`

    b. Haga clic en **Siguiente**.


4. En la página **Configurar inicio de sesión único en Pluralsight**, siga estos pasos: ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_05.png)

    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Siguiente**.


5. Para configurar el inicio de sesión único para la aplicación, póngase en contacto con el equipo de [servicios profesionales](mailTo:professionalservices@pluralsight.com) de Pluralsight y proporcione el archivo de metadatos descargado.


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

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_03.png)

4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-pluralsight-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de Pluralsight

El objetivo de esta sección es crear un usuario llamado Britta Simon en Pluralsight. Trabaje con el equipo de soporte técnico de Pluralsight para agregar los usuarios en la cuenta de Pluralsight.


> [AZURE.NOTE] Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el equipo de soporte técnico de Pluralsight.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure, para lo que se le concederá acceso a Pluralsight.

![Asignar usuario][200]

**Para asignar a Britta Simon a Pluralsight, siga estos pasos:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Pluralsight**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-pluralsight-tutorial/tutorial_pluralsight_50.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

    ![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

    ![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Pluralsight en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Pluralsight.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-pluralsight-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0504_2016-->