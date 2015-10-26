<properties pageTitle="Tutorial: integración de Azure Active Directory con ServiceNow | Microsoft Azure" description="Aprenda a usar ServiceNow con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integración de Azure Active Directory con ServiceNow
>[AZURE.TIP]Para enviar comentarios, haga clic [aquí](http://go.microsoft.com/fwlink/?LinkId=521880).
  
El objetivo de este tutorial es mostrar la integración de Azure y ServiceNow. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en ServiceNow
  
Después de completar este tutorial, los usuarios de Azure AD asignados a ServiceNow podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ServiceNow (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ServiceNow
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-servicenow-tutorial/IC769496.png "Escenario")
##Habilitación de la integración de aplicaciones para ServiceNow
  
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para ServiceNow.

###Siga estos pasos para habilitar la integración de aplicaciones para ServiceNow:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-servicenow-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-servicenow-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-servicenow-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **ServiceNow**.

    ![Galería de aplicaciones](./media/active-directory-saas-servicenow-tutorial/IC701016.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **ServiceNow** y haga clic en **Completar** para agregar la aplicación.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en ServiceNow con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Dropbox para Empresas. Si no está familiarizado con este procedimiento, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **ServiceNow**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ServiceNow?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de ServiceNow**, escriba su dirección URL con el siguiente patrón "*https://<InstanceName>.servicenow.com*" y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en ServiceNow**, haga clic en **Descargar certificado**, guarde el archivo de certificado localmente en el equipo y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configurar inicio de sesión único")

5.  En su inquilino de ServiceNow, en la barra de navegación de la izquierda, haga clic en **Properties** para abrir la página **SAML 2.0 Single Sign on properties**.


6. En la página **SAML 2.0 Single Sign-on properties**, realice los pasos siguientes:

     6\.1. Como **Enable external authentication**, seleccione **Yes**.


     6\.2. En el cuadro de texto **The Identity Provider URL which will issue the SAML2 security token with user info**, escriba **https://sts.windows.net/<su GUID de inquilino>/**.


     6\.3. En el cuadro de texto **The base URL to the Identity Provider’s AuthnRequest service**, escriba **https://login.windows.net/<su GUID de inquilino>/saml2**.


     6\.4. En el cuadro de texto **The base URL to the Identity Provider’s SingleLogoutRequest service**, escriba **https://login.windows.net/<su GUID de inquilino>/saml2**.


     6\.5. En el cuadro de texto **The protocol binding for the Identity Provider’s SingleLogoutRequest service**, escriba **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**.

     6\.6. Como **Sign LogoutRequest**, seleccione **Yes**.

     6\.7. En el cuadro de texto **Cuando se produce un error en el inicio de sesión único de SAML 2.0 porque la sesión no está autenticada, o es el primer inicio de sesión, redirigir a esta dirección URL**, escriba **https://login.windows.net/<su GUID de inquilino>/saml2**.

  

7. En la sección **Service Provider (Service-Now) properties **, realice los pasos siguientes:

     7\.1. En el cuadro de texto **The URL to Service-now instance homepage** cuadro de texto, escriba la dirección URL a la página de inicio de instancia de ServiceNow. La dirección URL de la página principal de la instancia de ServiceNow es una concatenación de su **URL de inquilino de ServiceNow** y **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** <br><br> ![Página principal de la instancia de Service-now](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Página principal de la instancia de Service-now")


     7\.2. En el cuadro de texto **The entity identification, or the issuer**, escriba la diección URL de su inquilino.

     7\.3. En el cuadro de texto **The audience uri that accepts SAML2 token**, escriba la dirección URL de su inquilino.

     7\.4. En el cuadro de texto **The User table field to match with the Subject’s NameID element in the SAMLResponse**, escriba **email**.

     7\.5. En el cuadro de texto **The NameID policy to use for returning the Subject’s NameID in the SAMLResponse**, escriba **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**.

     7\.6 Deje la casilla **Create an AuthnContextClass request in the AuthnRequest statement** sin activar.

     7\.7 En el cuadro de texto **The AuthnContextClassRef method that will be included in our SAML 2.0 AuthnRequest to the Identity Provider**, escriba ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.



8. En la sección **Advanced settings**, lleve a cabo estos pasos:

     8\.1. En el cuadro de texto **The number in seconds before “notBefore” constraint, or after “notOnOrAfter” constraint to consider still valid**, escriba **60**.


9. Para guardar la configuración, haga clic en **Save**.

10. En la barra de navegación de la izquierda, haga clic en **Certificate** para abrir la página **Certificate**.



11. Para cargar el certificado, en la página Certificate, realice los pasos siguientes:

     11\.1. Haga clic en **Nuevo**.

     11\.2. En el cuadro de texto **Name**, escriba **SAML 2.0**.

     11\.3. Seleccione **Active**.

     11\.4. Como **Format**, seleccione **PEM**.

     11\.5. Cree un archivo codificado en Base 64 a partir del certificado descargado. >[AZURE.NOTE]Para obtener más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

     11\.6. En **el Bloc de notas**, abra el archivo codificado en Base 64 y copie el contenido de este archivo en el Portapapeles.

     11\.7. Pegue el contenido del Portapapeles en el cuadro de texto **PEM Certificate**.

     11\.8. Haga clic en **Enviar**.



12. En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y haga clic en Completar para cerrar el cuadro de diálogo Configurar inicio de sesión único. <br><br> ![Configurar inicio de sesión único](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Configurar inicio de sesión único")




##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para ServiceNow.


### Siga estos pasos para configurar el aprovisionamiento de usuario:

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **ServiceNow**, haga clic en **Configurar aprovisionamiento de usuarios**. <br><br> ![Aprovisionamiento de usuarios](./media/active-directory-saas-servicenow-tutorial/IC769498.png "Aprovisionamiento de usuarios")


2. En la página **Especifique sus credenciales de ServiceNow para habilitar el aprovisionamiento automático de usuarios**, proporcione los valores de configuración siguientes:

     2\.1. En el cuadro de texto **ServiceNow Instance Name**, escriba el nombre de la instancia de ServiceNow.

     2\.2. En el cuadro de texto **ServiceNow Admin User Name**, escriba el nombre de la cuenta de administrador de ServiceNow.

     2\.3. En el cuadro de texto **ServiceNow Admin Password**, escriba la contraseña para esta cuenta.

     2\.4. Haga clic en **validar** para comprobar la configuración.

     2\.5. Haga clic en el botón **Siguiente** para abrir la página **Pasos siguientes**.

     2\.6. Si quiere aprovisionar todos los usuarios para esta aplicación, seleccione "**Automatically provision all user accounts in the directory to this application**". <br><br> ![Pasos siguientes](./media/active-directory-saas-servicenow-tutorial/IC698804.png "Pasos siguientes")

     2\.7. En la página **Pasos siguientes**, haga clic en **Completar** para guardar la configuración.











##Asignación de usuarios
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Siga estos pasos para asignar usuarios a ServiceNow:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ServiceNow **, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-servicenow-tutorial/IC769499.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y, a continuación, en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-servicenow-tutorial/IC767830.png "Sí")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=Oct15_HO3-->