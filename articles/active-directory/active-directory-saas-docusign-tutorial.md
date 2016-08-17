<properties
	pageTitle="Tutorial: Integración de Azure Active Directory con DocuSign | Microsoft Azure"
	description="Aprenda a configurar el inicio de sesión único entre Azure Active Directory y DocuSign."
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
	ms.date="08/01/2016"
	ms.author="jeedes"/>


# Tutorial: Integración de Azure Active Directory con DocuSign

El objetivo de este tutorial es mostrar la integración de Azure y DocuSign. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

- Una suscripción de Azure válida
- Un inquilino en DocuSign



La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. [Habilitación de la integración de aplicaciones para DocuSign](#enabling-the-application-integration-for-docusign)


2. [Configuración del inicio de sesión único](#configuring-single-sign-on)


3. [Configuración del aprovisionamiento de cuentas](#configuring-account-provisioning)


4. [Asignación de usuarios](#assigning-users)

    ![Configuración del inicio de sesión único][0]
 

## Habilitación de la integración de aplicaciones para DocuSign

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para DocuSign.

### Siga estos pasos para habilitar la integración de aplicaciones para DocuSign:

1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

	![Configuración del inicio de sesión único][1]

2. En la lista Directory, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

	![Configuración del inicio de sesión único][2]

4. Haga clic en **Agregar** en la parte inferior de la página.

	![Aplicaciones][3]

5. En el cuadro de diálogo ¿Qué desea hacer?, haga clic en **Agregar una aplicación de la galería**.

	![Configuración del inicio de sesión único][4]


6. En el cuadro de búsqueda, escriba **DocuSign**.

	![Configuración del inicio de sesión único][5]

7. En el panel de resultados, seleccione **DocuSign** y haga clic en **Completar** para agregar la aplicación.

	![Configuración del inicio de sesión único][6]


## Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir que los usuarios se autentiquen en DocuSign con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.


### Siga estos pasos para configurar el inicio de sesión único:

1. En el Portal de Azure clásico, en la página de **integración de la aplicación DocuSign**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo Configurar inicio de sesión único.

	![Configuración del inicio de sesión único][7]

2. En la página **¿Cómo desea que los usuarios inicien sesión en DocuSign?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en Siguiente.

	![Configuración del inicio de sesión único][8]

3. En la página **Configurar las opciones de la aplicación**, realice los pasos siguientes:

	![Configuración del inicio de sesión único][9]

	a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL del inquilino de Docusign con el siguiente patrón: para el entorno de producción, el patrón de dirección URL será **"https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>"**; para el entorno de demostración, el patrón de la dirección URL será **"https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2/login/sp/<IDPID>"**

	b. En el cuadro de texto **Identificador**, escriba la dirección URL del emisor de Docusign con el siguiente patrón: para el entorno de producción, el patrón de dirección URL será **"https://account.docusign.com/organizations/<ORGANIZATIONID>/saml2"**; para el entorno de demostración, el patrón de la dirección URL será **"https://account-d.docusign.com/organizations/<ORGANIZATIONID>/saml2"**

	c. Haga clic en **Siguiente**.


    > [AZURE.TIP] Si no sabe cuál es la dirección URL de aplicación para el inquilino, póngase en contacto con DocuSign a través de [SSOSetup@Docusign.com](emailTo:SSOSetup@Docusign.com) para obtener la URL de SSO iniciado por el SP para el inquilino.
 

4. En la página **Configurar inicio de sesión único en DocuSign**, haga clic en **Descargar certificado** y después guarde el archivo de certificado localmente en el equipo.

	![Configuración del inicio de sesión único][10]


5. En otra ventana del explorador web, inicie sesión en el **Portal de administración de DocuSign** como administrador.


6. En el menú de navegación izquierdo, haga clic en **Domains** (Dominios)

	![Configuración del inicio de sesión único][51]

7. En el panel derecho, haga clic en el botón **CLAIM DOMAIN** (DOMINIO DE NOTIFICACIÓN).

	![Configuración del inicio de sesión único][52]

8. En la ventana emergente, escriba el nombre de dominio de su compañía y haga clic en la notificación. Asegúrese de que comprueba el dominio y de que muestra el estado como activo.

	![Configuración del inicio de sesión único][53]

9. En el menú de navegación izquierdo, haga clic en **Identity Providers** (Proveedores de identidades)

	![Configuración del inicio de sesión único][54]

10. En el panel derecho, haga clic en el botón "ADD IDENTITY PROVIDER" (AGREGAR PROVEEDOR DE IDENTIDADES). Se abrirá la página de configuración de SSO.
	
	![Configuración del inicio de sesión único][55]

11. En la página de configuración del proveedor de identidades, realice las siguientes acciones.

	a. Dé un nombre único a la configuración. No use espacios entre las palabras.

	b. En el cuadro de texto **Identity Provider Issuer** (Emisor de proveedor de identidades), coloque el valor de **URL del emisor** del Asistente para configuración de aplicaciones de Azure AD.

	c. En el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión de proveedor de identidades), coloque el valor de **Dirección URL de inicio de sesión remoto** del Asistente para configuración de aplicaciones de Azure AD.

	d. En el cuadro de texto **Identity Provider Logout URL** (Dirección URL de cierre de sesión de proveedor de identidades), coloque el valor de **Dirección URL de cierre de sesión remoto** del Asistente para configuración de aplicaciones de Azure AD.

	e. Active la casilla **Sign AuthN Request** (Firmar solicitud de autenticación).

	f. Asegúrese de que la opción **Send AuthN request by:** (Enviar solicitud de autenticación por:) está establecida en **POST**

	g. Asegúrese de que la opción **Send logout request by:** (Enviar solicitud de cierre de sesión por:) está establecida en **POST**

	![Configuración del inicio de sesión único][56]

12. En la sección **Custom Attribute Mapping** (Asignación de atributos personalizados), elija el campo que desea asignar con la notificación de Azure AD. Por ejemplo, hemos utilizado la notificación **emailaddress** asignada con el valor como **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este es el nombre de notificación predeterminado de Azure AD para la notificación de correo electrónico.

	> [AZURE.NOTE] Utilice el identificador de usuario adecuado para asignar el usuario de Azure AD a la asignación de usuarios de Docusign. Seleccione el campo apropiado y escriba el valor adecuado según la configuración de la organización.

	![Configuración del inicio de sesión único][57]

13. En la sección **Identity Provider Certificate** (Certificado de proveedor de identidades), haga clic en el botón **ADD CERTIFICATE** (AGREGAR CERTIFICADO) y cargue el certificado que ha descargado del Asistente para configuración de aplicaciones de Azure AD.

	![Configuración del inicio de sesión único][58]

14. Haga clic en el botón **Save** (Guardar) para guardar la configuración.

15. En la sección **Identity Providers** (Proveedores de identidades), haga clic en el botón **Actions** (Acciones) botón y haga clic en **Endpoints** (Puntos de conexión)

	![Configuración del inicio de sesión único][59]

16. En la sección **View SAML 2.0 Endpoints** (Ver puntos de conexión de SAML 2.0) lleve a cabo estos pasos:

	a. Copie el valor de **Service Provider Issuer URL** (Dirección URL de emisor de proveedor de servicio) y colóquelo en el cuadro de texto **Identificador** del Asistente para configuración de Azure AD.

	b. Copie el valor de **Service Provider Login URL** (Dirección URL de inicio de sesión de proveedor de servicio) y colóquelo en el cuadro de texto **URL de inicio de sesión** del Asistente para configuración de Azure AD.

	![Configuración del inicio de sesión único][60]

	c. Haga clic en **Close** (Cerrar).

15. En el Portal de Azure clásico, seleccione la **confirmación de la configuración de inicio de sesión único** y haga clic en **Siguiente**.

	![Aplicaciones][14]

10. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.

	![Aplicaciones][15]
 

## Configuración del aprovisionamiento de cuentas

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de usuarios de Active Directory para DocuSign.

### Siga estos pasos para configurar el aprovisionamiento de usuario:

1. En el **Portal de Azure clásico**, en la página de **integración de la aplicación DocuSign**, haga clic en **Configurar aprovisionamiento de cuentas** para abrir el cuadro de diálogo Configurar aprovisionamiento de usuarios.

	![Configuración del aprovisionamiento de cuentas][30]

2. En la página **Configuración y credenciales de administrador**, para habilitar el aprovisionamiento automático de usuarios, proporcione las credenciales de una cuenta de DocuSign con derechos suficientes y después haga clic en **Siguiente**.

	![Configuración del aprovisionamiento de cuentas][31]

3. En el cuadro de diálogo **Probar conexión**, haga clic en **Iniciar prueba** y, cuando se haya realizado la prueba correctamente, haga clic en **Siguiente**.

	![Configuración del aprovisionamiento de cuentas][32]

3. En la página **Confirmación**, haga clic en **Completar**.

	![Configuración del aprovisionamiento de cuentas][33]
 

## Asignación de usuarios

Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### Para asignar usuarios a DocuSign, lleve a cabo los siguientes pasos:

1. En el **Portal de Azure clásico**, cree una cuenta de prueba.

2. En la página de **integración de la aplicación DocuSign**, haga clic en **Asignar usuarios**.

	![Asignación de usuarios][40]
 

3. Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

	![Asignación de usuarios][41]


Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado con DocuSign.

Como primer paso de la comprobación, puede comprobar el estado del aprovisionamiento, para lo que debe hacer clic en Panel en la D de la página de integración de la aplicación DocuSign en el Portal de Azure clásico.

![Asignación de usuarios][42]

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Asignación de usuarios][43]


Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más información sobre el Panel de acceso, consulte Introducción al Panel de acceso.


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png

<!---HONumber=AcomDC_0803_2016-->