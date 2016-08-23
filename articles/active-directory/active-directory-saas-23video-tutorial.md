<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con 23 Video | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y 23 Video."
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
	ms.date="08/05/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con 23 Video

El objetivo de este tutorial es mostrar cómo integrar 23 Video con Azure Active Directory (Azure AD). Integrar 23 Video con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso 23 Video.
- Puede permitir que los usuarios inicien sesión automáticamente en 23 Video (inicio de sesión único) con sus cuentas de Azure AD.

Si quiere obtener más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos 

Para configurar la integración de Azure AD con 23 Video, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en 23 Video


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

 
## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de 23 Video desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de 23 Video desde la galería
Para configurar la integración de 23 Video en Azure AD, deberá agregar 23 Video desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar 23 Video desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**.

	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **23 Video**.

	![Aplicaciones][5]

7. En el panel de resultados, seleccione **23 Video** y luego haga clic en **Completar** para agregar la aplicación.

	![Aplicaciones][25]

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con 23 Video con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de 23 Video para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de 23 Video. Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en 23 Video.
 
Para configurar y probar el inicio de sesión único de Azure AD con 23 Video, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de 23 Video](#creating-a-23-video-test-user)**: para tener un homólogo de Britta Simon en 23 Video que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación 23 Video.

**Para configurar el inicio de sesión único de Azure AD con 23 Video, realice los pasos siguientes:**

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **23 Video**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único][6]

2. En la página **¿Cómo desea que los usuarios inicien sesión en 23 Video?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD][7]

3. En la página de diálogo **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Inicio de sesión único de Azure AD][8]
 
     a. En el cuadro de texto **URL de respuesta**, escriba la dirección URL que los usuarios usan para iniciar sesión en el sitio de 23 Video (p. ej.: *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] La integración de Active Directory mediante SAML 2.0 está disponible para todos los usuarios de 23 Video. Si necesita los metadatos relacionados, póngase en contacto con el soporte técnico en [support@23company.com](mailto:support@23company.com).

     b. Haga clic en **Siguiente**.
 
4. En la página **Configurar inicio de sesión único en 23 Video**, siga estos pasos:

	![Inicio de sesión único de Azure AD][9]

    a. Haga clic en Descargar certificado y después guarde el archivo en el equipo.

    b. Póngase en contacto con el equipo de soporte técnico de 23 Video a través de [support@23company.com](mailto:support@23company.com), proporcióneles el certificado descargado, la **URL del emisor**, la **Dirección URL del servicio de inicio de sesión único** y la **Dirección URL de inicio de sesión único**, y pídales que configuren el inicio de sesión único para su aplicación 23 Video.

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

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png)
 
4. Para abrir el diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)

    a. En Tipo de usuario, seleccione Nuevo usuario de la organización.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png)
 
    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png)
 
8. En la página de diálogo **Obtener contraseña temporal**, siga estos pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png)
  
    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.

  
 
### Creación de un usuario de prueba de 23 Video

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en 23 Video.

**Para crear un usuario llamado Britta Simon en 23 Video, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la compañía de 23 Video como administrador.

1. Vaya a **Configuración**.


2. En la sección **Usuarios**, haga clic en **Configurar**.

	![Asignar usuario][400]

3. Haga clic en **Agregar un nuevo usuario**.

	![Asignar usuario][401]

4. En la sección **Invite someone to join this site** (Invitar a alguien a unirse a este sitio), realice los siguientes pasos:

	![Asignar usuario][402]

    a. En el cuadro de texto **Direcciones de correo electrónico**, escriba la dirección de correo electrónico de Britta Simon en Azure AD.

    b. Haga clic en **Agregar un usuario**.


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a 23 Video.

![Asignar usuario][200]

**Para asignar a Britta Simon a 23 Video, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.

	![Asignar usuario][201]

2. En la lista de aplicaciones. seleccione **23 Video**.

	![Asignar usuario][202]

1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

	![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso. Al hacer clic en el icono de 23 Video en el panel de acceso, debería iniciar sesión automáticamente en su aplicación 23 Video.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png

<!---HONumber=AcomDC_0810_2016-->