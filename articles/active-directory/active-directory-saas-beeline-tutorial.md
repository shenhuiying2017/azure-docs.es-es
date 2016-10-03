<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Beeline | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Beeline."
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
	ms.date="09/19/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Beeline

En este tutorial, obtendrá información sobre cómo integrar Beeline con Azure Active Directory (Azure AD).

Integrar Beeline con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Beeline
- Puede permitir que los usuarios inicien sesión automáticamente en Beeline (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el Portal de Azure clásico.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Beeline, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Beeline


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Beeline desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Beeline desde la galería
Para configurar la integración de Beeline en Azure AD, deberá agregar Beeline desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Beeline desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Beeline**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_01.png)

7. En el panel de resultados, seleccione **Beeline** y después haga clic en **Completar** para agregar la aplicación.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_06.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Beeline con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Beeline para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Beeline. Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Beeline.

Para configurar y probar el inicio de sesión único de Azure AD con Beeline, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Beeline](#creating-an-beeline-test-user)**: para tener un homólogo de Britta Simon en Beeline que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el portal clásico y configurará el inicio de sesión único en la aplicación Beeline.

La aplicación Beeline espera las aserciones de SAML en un formato concreto. Trabaje primero con el equipo de Beeline para identificar el identificador de usuario correcto que se asignará a la aplicación. También siga las instrucciones del equipo de Beeline sobre el atributo que desean usar para esta asignación. Microsoft recomienda utilizar el atributo **"NameIdentifier"** como identificador de usuario. Puede administrar el valor de este atributo desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto. Aquí hemos asignado la notificación nameidentifier con el atributo **userprincipalname**, que proporciona el identificador de usuario único, que se enviará a la aplicación Beeline en cada respuesta de SAML correcta.

![Configurar inicio de sesión único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_07.png)


**Para configurar el inicio de sesión único de Azure AD con Beeline, realice los pasos siguientes:**

1. En el Portal clásico, en la página de integración de aplicaciones de **Beeline**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	 ![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en Beeline?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.
 	
	![Configurar inicio de sesión único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_03.png)

3. En la página del cuadro de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Configurar inicio de sesión único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_04.png)


    a. En el cuadro de texto **Identificador**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Beeline con el siguiente patrón: `https://projects.beeline.net/<instance name>`

	b. En URL de respuesta, escriba la dirección URL con el siguiente patrón: `https://projects.beeline.net/<instance name>/SSO_External.ashx` o `https://projects.beeline.net/<company name>/SSO_External.ashx`


4. En la página **Configurar inicio de sesión único en Beeline**, realice los pasos siguientes:

	![Configurar inicio de sesión único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_05.png)

    a. Haga clic en **Descargar metadatos** y luego guarde el archivo en el equipo.

    b. Haga clic en **Next**.


5.  Para tener SSO configurado para la aplicación, póngase en contacto con el equipo de soporte técnico Beeline, que le ayudará a configurar SSO. Tenga en cuenta que tendrá que enviar un correo electrónico y adjuntar el archivo de metadatos descargado y también proporcionar el id. de entidad y una dirección URL de servicio de único inicio de sesión a dirección URL del servicio de cierre de sesión único.
  
6. En el portal clásico, seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Siguiente**.
	
	![Inicio de sesión único de Azure AD][10]

7. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
  	
	![Inicio de sesión único de Azure AD][11]



### Creación de un usuario de prueba de Azure AD
En esta sección, creará un usuario de prueba llamado Britta Simon en el portal clásico.


![Creación de un usuario de Azure AD][20]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.
	
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
	
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png)

4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Next**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_07.png)

8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-beeline-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Creación de un usuario de prueba de Beeline

En esta sección, creará un usuario llamado Britta Simon en Beeline. La aplicación Beeline necesita que todos los usuarios estén aprovisionados en la aplicación antes de realizar el inicio de sesión único. Así pues, colabore con el servicio de asistencia al cliente de Beeline para aprovisionar todos estos usuarios en la aplicación.


> [AZURE.NOTE] Si necesita crear un usuario de forma manual o por lotes de los usuarios, póngase en contacto con el equipo de soporte técnico de Beeline.


### Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Beeline.

![Asignar usuario][200]

**Para asignar a Britta Simon a Beeline, realice los pasos siguientes:**

1. En el Portal clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Beeline**.

	![Configurar inicio de sesión único](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_50.png)

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]


### Prueba del inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso. Al hacer clic en el icono de Beeline en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Beeline.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0921_2016-->