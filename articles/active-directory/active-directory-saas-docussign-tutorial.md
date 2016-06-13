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
	ms.date="05/26/2016"
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

7. En el panel de resultados, seleccione **DocuSign** y, después, haga clic en **Completar** para agregar la aplicación.

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

	a. En el cuadro de texto **dirección URL de inicio de sesión**, escriba la dirección URL del inquilino de DocuSign con el patrón siguiente: `https://<company name>.docusign.net/Member/MemberLogin.aspx?ssoname=<SSO instance name>`.

	b. Haga clic en **Siguiente**.


    > [AZURE.TIP] Si no sabe cuál es la dirección URL de aplicación para el inquilino, póngase en contacto con DocuSign a través de SSOSetup@Docusign.com para obtener la URL de SSO iniciado por el SP para el inquilino.
 

4. En la página **Configuración de inicio de sesión único en DocuSign**, haga clic en **Descargar certificado** y después guarde el archivo de certificado localmente en el equipo.

	![Configuración del inicio de sesión único][10]


5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía **DocuSign** como administrador.


6. En el menú de la parte superior, expanda el menú del usuario, haga clic en **Preferencias** y, después, en el panel de navegación de la izquierda, expanda **Administración de cuentas** y haga clic en **Características**.

	![Configuración del inicio de sesión único][11]

7. Haga clic en **SAML Configuration** (Configuración de SAML) y después en el vínculo **SAML Configuration**.



8. En la sección **Configuración de SAML 2.0**, realice los pasos siguientes:

	![Configuración del inicio de sesión único][13]


    a. En el Portal de Azure clásico, en la página de diálogo **Configuración de inicio de sesión único en DocuSign**, copie el valor de Dirección URL de emisor** y péguelo en el cuadro de texto **Dirección URL de punto de conexión del proveedor de identidades**.

    > [AZURE.IMPORTANT] Si esta opción de configuración no está disponible, póngase en contacto con el administrador de cuenta de DocuSign o con el equipo de soporte de SSO por correo electrónico ([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com)).
 
    b. Haga clic en **Browse** (Examinar) para cargar el certificado descargado.


    c. Seleccione **Enable first name, last name and email address** (Habilitar nombre, apellidos y dirección de correo electrónico).


    d. Haga clic en **Guardar**.


9. En el Portal de Azure clásico, seleccione la **confirmación de la configuración de inicio de sesión único** y haga clic en **Siguiente**.

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

2. En la página de **integración de aplicaciones de DocuSign**, haga clic en **Asignar usuarios**.

	![Asignación de usuarios][40]
 

3. Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

	![Asignación de usuarios][41]


Ahora debería esperar 10 minutos y comprobar si la cuenta se sincronizó con DocuSign.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento haciendo clic en la opción Panel de la página de integración de la aplicación DocuSign en el Portal de Azure clásico.

![Asignación de usuarios][42]

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Asignación de usuarios][43]


Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más información sobre el Panel de acceso, consulte Introducción al Panel de acceso.





## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=AcomDC_0601_2016-->