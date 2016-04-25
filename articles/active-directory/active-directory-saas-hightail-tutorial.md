<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con Hightail | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hightail."
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
	ms.date="04/11/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con Hightail

El objetivo de este tutorial es mostrar cómo integrar Hightail con Azure Active Directory (Azure AD).

Integrar Hightail con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Hightail.
- Puede permitir que los usuarios inicien sesión automáticamente en Hightail (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central, el Portal de Azure Active Directory.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Requisitos previos

Para configurar la integración de Azure AD con Hightail, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Hightail


> [AZURE.NOTE] Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## Descripción del escenario
El objetivo de este tutorial es permitirle probar el inicio de sesión único de Azure AD en un entorno de prueba.

La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Hightail desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD


## Adición de Hightail desde la galería
Para configurar la integración de Hightail en Azure AD, será preciso que agregue Hightail desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Hightail desde la galería, realice los pasos siguientes:**

1. En el **Portal de Azure clásico**, en el panel de navegación izquierdo, haga clic en **Active Directory**. 
 
	![Active Directory][1]

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Aplicaciones][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

	![Aplicaciones][4]

6. En el cuadro de búsqueda, escriba **Hightail**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)

7. En el panel de resultados, seleccione **Hightail** y después haga clic en **Completar** para agregar la aplicación.

	![Selección de la aplicación en la galería](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

##  Configuración y comprobación del inicio de sesión único de Azure AD
El objetivo de esta sección es mostrar cómo configurar y probar el inicio de sesión único de Azure AD con Hightail con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Hightail para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Hightail.

Esta relación de vínculo se establece asignando el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Hightail.

Para configurar y probar el inicio de sesión único de Azure AD con Hightail, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**: para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Creación de un usuario de prueba de Hightail](#creating-a-hightail-test-user)**: para tener un homólogo de Britta Simon en Hightail que esté vinculado a la representación de ella en Azure AD.
5. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#testing-single-sign-on)**: para comprobar si funciona la configuración.

### Configuración del inicio de sesión único de Azure AD

El objetivo de esta sección es habilitar el inicio de sesión único de Azure AD en el Portal de Azure clásico y configurar el inicio de sesión único en la aplicación Hightail.

La aplicación Hightail espera las aserciones de SAML en un formato concreto. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **"Atributo"** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto.

![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png)

**Para configurar el inicio de sesión único de Azure AD con Hightail, realice los pasos siguientes:**


1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Hightail**, en el menú de la parte superior, haga clic en **Atributos**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png)


1. En el cuadro de diálogo **Atributos de token de SAML**, para cada fila de la tabla siguiente, realice los pasos que se indican a continuación:

	| Nombre del atributo | Valor de atributo |
	| --- | --- |    
	| Nombre | user.givenname |
    | Apellidos | user.surname |
	| Email | user.mail |
	| UserIdentity | user.mail |

	a. Haga clic en **agregar atributo de usuario** para abrir el cuadro de diálogo **Agregar atributo de usuario**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png)


	b. En el cuadro de texto **Nombre de atributo**, escriba el nombre de atributo que se muestra para la fila.

    c. En la lista **Valor de atributo**, seleccione el valor de atributo que se muestra para esa fila.

    d. Haga clic en **Completo**.
	



1. En el menú de la parte superior, haga clic en **Inicio rápido**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)



2. En la página **¿Cómo desea que los usuarios inicien sesión en Hightail?**, seleccione **Inicio de sesión único de Azure AD** y después haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png)


3. En la página de diálogo **Configurar las opciones de la aplicación**, si desea configurar la aplicación en el **modo iniciado por el proveedor de identidades**, realice los pasos siguientes y haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png)



    a. En el cuadro de texto **URL de respuesta**, escriba la dirección URL en el siguiente patrón: **"https://www.hightail.com/samlLogin?phi\_action=app/samlLogin&subAction=handleSamlResponse"**.

	b. Haga clic en **Siguiente**.

4. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de servicios**, en la página de diálogo **Configurar las opciones de la aplicación**, haga clic en **"Mostrar la configuración avanzada (opcional)"** y después escriba la **URL de inicio de sesión** y haga clic en **Siguiente**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png)

	a. En el cuadro de texto URL de inicio de sesión, escriba la dirección URL que usan los usuarios para iniciar sesión en su aplicación de Hightail con el siguiente patrón: ****https://www.hightail.com/loginSSO**. Esta es la página de inicio de sesión común para todos los clientes que quieren usar el inicio de sesión único.

	b. Haga clic en **Siguiente**.

5. En la página **Configurar inicio de sesión único en Hightail**, lleve a cabo estos pasos y haga clic en **Siguiente**:

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png)


    a. Haga clic en **Descargar certificado** y luego guarde el archivo de certificado codificado en base 64 en el equipo.

    b. Haga clic en **Siguiente**.

	> [AZURE.NOTE] Antes de configurar el inicio de sesión único en la aplicación Hightail, incluya en la lista blanca su dominio de correo electrónico con el equipo Hightail para que todos los usuarios que usen este dominio puedan aprovechar la funcionalidad de inicio de sesión único.

6. Para configurar SSO para la aplicación, debe iniciar sesión en su inquilino de Hightail como administrador.

	a. En el menú de la parte superior, haga clic en la pestaña **Cuenta** y seleccione **Configurar SAML**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png)


	b. Active la casilla **Enable SAML Authentication** (Habilitar autenticación SAML).

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png)

	c. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido en el Portapapeles y péguelo en el cuadro de texto **SAML Token Signing Certificate** (Certificado de firma de tokens de SAML).

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png)


	d. Copie la dirección URL de inicio de sesión remoto de Azure AD en **SAML Authority (Identity Provider)** (Autoridad de SAML [proveedor de identidades]) en Hightail.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)
	
	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

	e. Si quiere configurar la aplicación en el **modo iniciado por el proveedor de identidades**, seleccione **"Identity Provider (IdP) initiated log in"** ("Inicio de sesión iniciado por el proveedor de identidades"). Si opta por el **modo iniciado por el proveedor de servicios**, seleccione **"Service Provider (SP) initiated log in"** ("Inicio de sesión iniciado por el proveedor de servicios").
	
	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

	f. Copie la dirección URL del consumidor de SAML para la instancia y péguela en el cuadro de texto **URL de respuesta**, tal como se muestra en el paso 4.

	g. Haga clic en **Guardar**.



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

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png)

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para mostrar la lista de usuarios, en el menú de la parte superior, haga clic en **Usuarios**.
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png)


4. Para abrir el cuadro de diálogo **Agregar usuario**, en la barra de herramientas de la parte inferior, haga clic en **Agregar usuario**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)

5. En la página de diálogo **Proporcione información sobre este usuario**, realice los pasos siguientes:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png)

    a. En **Tipo de usuario**, seleccione **Nuevo usuario de la organización**.

    b. En el cuadro de texto **Nombre de usuario**, escriba **BrittaSimon**.

    c. Haga clic en **Siguiente**.

6.  En la página de diálogo **Perfil de usuario**, realice los siguientes pasos:

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png)

    a. En el cuadro de texto **Nombre**, escriba **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba **Simon**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **Britta Simon**.

    d. En la lista **Rol**, seleccione **Usuario**.

    e. Haga clic en **Siguiente**.

7. En la página de diálogo **Obtener contraseña temporal**, haga clic en **Crear**.

	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png)


8. En la página de diálogo **Obtener contraseña temporal**, realice los pasos siguientes:
 
	![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png)

    a. Anote el valor del campo **Nueva contraseña**.

    b. Haga clic en **Completo**.



### Crear un usuario de prueba de Hightail

El objetivo de esta sección es crear un usuario llamado Britta Simon en Hightail.

No hay ningún elemento de acción para usted en esta sección. Hightail admite el aprovisionamiento Just-In-Time de usuarios en función de las notificaciones personalizadas. Si ha configurado las notificaciones personalizadas como se muestra en la anterior sección **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)**, se creará automáticamente un usuario en la aplicación, si es que todavía no existe uno.

> [AZURE.NOTE] Si necesita crear manualmente un usuario, debe ponerse en contacto con el equipo de soporte técnico de Hightail enviando un correo electrónico a la dirección [support@hightail.com](mailto:support@hightail.com).


### Asignación del usuario de prueba de Azure AD

El objetivo de esta sección es permitir que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Hightail.

![Asignar usuario][200]

**Para asignar a Britta Simon a Hightail, realice los pasos siguientes:**

1. En el Portal de Azure clásico, para abrir la vista de aplicaciones, en la vista del directorio, haga clic en **Aplicaciones** en el menú superior.
 
	![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **Hightail**.

	![Configurar inicio de sesión único](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png)


1. En el menú de la parte superior, haga clic en **Usuarios**.

	![Asignar usuario][203]

1. En la lista Usuarios, seleccione **Britta Simon**.

2. En la barra de herramientas de la parte inferior, haga clic en **Asignar**.

![Asignar usuario][205]



### Prueba del inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Hightail en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Hightail.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0413_2016-->