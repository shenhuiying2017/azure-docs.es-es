<properties umbracoNaviHide="0" pageTitle="Azure AD Integration with Google Apps" metaKeywords="Azure Google Apps Integration" description="Learn about integrating Azure AD with Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Azure AD Integration with Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Azure AD Integration with Google Apps" authors="" />

Integración de Azure AD con Google Apps
=======================================

El objetivo de este tutorial es mostrar la integración de Azure y Google Apps. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de prueba en Google Apps

Si aún no tiene un inquilino válido en Google Apps, puede iniciar sesión con una cuenta de prueba del sitio web de Google Apps for Business.

La situación descrita en este tutorial consta de los siguientes bloques de creación:

Habilitación de la integración de aplicaciones para Google Apps 
Configuración del inicio de sesión único 
Habilitación del acceso a la API de Google Apps 
Incorporación de dominios personalizados 
Configuración del aprovisionamiento de usuario

Habilitación de la integración de aplicaciones para Google Apps
===============================================================

El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Google Apps.

Siga estos pasos para habilitar la integración de aplicaciones para Google Apps:
--------------------------------------------------------------------------------

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.
2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.
3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
4.  En la parte inferior, haga clic en **Add** para abrir el diálogo **Add Application**.
5.  En el diálogo **Integrate an app with Azure AD**, haga clic en **Manage access to an application**.
6.  En la página **Select an application to manage**, seleccione **Google Apps** en la lista de aplicaciones.
7.  Haga clic en el botón **Complete** para agregar la aplicación y cerrar el cuadro de diálogo.

Configuración del inicio de sesión único
========================================

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Google Apps con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

Siga estos pasos para configurar el inicio de sesión único federado:
--------------------------------------------------------------------

1.  En el panel de navegación izquierdo del Portal de administración de Azure, seleccione **Active Directory** para abrir la página de diálogo de Active Directory.
2.  En la lista de directorios, seleccione el suyo para abrir la página de configuración del mismo.
3.  Seleccione **APPLICATIONS** en el menú de nivel superior.
4.  En la lista de aplicaciones, seleccione **Google Apps** para abrir el diálogo de configuración de Google Apps.
5.  Para abrir el diálogo **CONFIGURE SINGLE SIGN-ON**, haga clic en **Configure single sign-on**.

    ![Google\_Tutorial\_01](./media/integration-azure-google-apps/Google_Tutorial_01.png)

6.  En la página de diálogo Select the single sign-on mode for this app, seleccione Users authenticate with their account en Azure AD como MODE y, a continuación, haga clic en el botón Next.

    ![Google\_Tutorial\_02](./media/integration-azure-google-apps/Google_Tutorial_02.png)

7.  En la página de diálogo **Configure App URL**, en el cuadro de texto **GOOGLE APPS TENANT URL**, escriba la URL del inquilino de Google Apps y, a continuación, haga clic en el botón **Next**.

    ![Google\_Tutorial\_03](./media/integration-azure-google-apps/Google_Tutorial_03.png)

8.  En la página de diálogo **Configure single sign-on at Google Apps**, siga estos pasos y, a continuación, haga clic en el botón **Complete**.

    -   Haga clic en **Download certificate** y, a continuación, guarde el certificado como **c:\\googleapps.cer**.
    -   Abra la página de inicio de sesión de Google Apps e inicie sesión.

    ![Google\_Tutorial\_04](./media/integration-azure-google-apps/Google_Tutorial_04.png)

    -   En la **Consola de administración**, haga clic en Security.

    ![Google\_Tutorial\_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

    Si el icono Security no aparece, debe hacer clic en More controls, al final de la página.

9.  En la página **Security**, haga clic en la configuración **Advanced**.

    ![Google\_Tutorial\_06](./media/integration-azure-google-apps/Google_Tutorial_06.png)

10. En la sección de configuración **Advanced** de la página, seleccione **Set up single sign-on**.

    ![Google\_Tutorial\_07](./media/integration-azure-google-apps/Google_Tutorial_07.png)

11. Siga estos pasos en la página de configuración del inicio de sesión único:

    ![Google\_Tutorial\_08](./media/integration-azure-google-apps/Google_Tutorial_08.png)

        + Seleccione <strong>Enable Single Sign-on</strong>.
        + En la página <strong>Configure single sign-on at Google Apps</strong> del Portal de Azure AD, copie la <strong>URL DE INICIO DE SESIÓN ÚNICO</strong> y, a continuación, péguela en el cuadro de texto relacionado de la página <strong>Security</strong> de la <strong>Consola de administración</strong> de Google Apps.
        + En la página <strong>Configure single sign-on at Google Apps</strong> del Portal de Azure AD, copie la <strong>URL de servicio de cierre de sesión único</strong> y, a continuación, péguela en el cuadro de texto relacionado de la página <strong>Security</strong> de la <strong>Consola de administración</strong> de Google Apps.
        + En la página <strong>Configure single sign-on at Google Apps</strong> del Portal de Azure, copie la <strong>URL de cambio de contraseña</strong> y, a continuación, péguela en el cuadro de texto relacionado de la página <strong>Security</strong> de la <strong>Consola de administración</strong> de Google Apps.
        + Haga clic en el botón <strong>Browse</strong> para localizar el <strong>Certificado de comprobación</strong> y, a continuación, haga clic en Upload.
        + Haga clic en <strong>Save changes</strong>.

12. En la página **Configure single sign-on at Google Apps** del Portal de Azure AD, haga clic en el botón Complete.

Ahora ya puede ir al [Panel de acceso](https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633) y probar el inicio de sesión único en Google Apps.

Habilitación del acceso API de Google Apps
==========================================

Cuando integre Azure Active Directory con Google Apps para el aprovisionamiento de usuario, debe habilitar el acceso a la API para su inquilino de Google Apps.

Habilitación del acceso API de Google Apps
------------------------------------------

1.  Inicie sesión en su inquilino de Google Apps.
2.  En la **Consola de administración**, haga clic en **Security**.

    ![Google\_Tutorial\_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

    Si el icono Security no aparece, haga clic en More controls, en la parte final de la Consola de administración.
3.  En la página Security, haga clic en **API reference** para abrir la página de diálogo de configuración relacionada.
4.  Seleccione **Enable API access**.

    ![Google\_Tutorial\_09](./media/integration-azure-google-apps/Google_Tutorial_09.png)

Incorporación de dominios personalizados
========================================

La configuración del aprovisionamiento de usuario con Google Apps exige que el dominio de Azure AD y el dominio de Google Apps tengan el mismo nombre de dominio completo (FQDN). Sin embargo, cuando se usan inquilinos de prueba para verificar la situación que explica este tutorial, los FQDN de su inquilino no coinciden normalmente. Para solucionar este problema, puede configurar dominios personalizados en Azure AD y Google Apps. Configurar un dominio personalizado requiere acceso a su archivo de zona DNS del dominio público.

![Google\_Tutorial\_10](./media/integration-azure-google-apps/Google_Tutorial_10.png)

Siga estos pasos para agregar un dominio personalizado en Azure AD:
-------------------------------------------------------------------

1.  En el panel de navegación izquierdo del Portal de administración de Azure, seleccione **Active Directory** para abrir la página de diálogo **Active Directory**.
2.  En la lista de directorios, seleccione el suyo para abrir la página de configuración del mismo.
3.  Seleccione **DOMAINS** en el menú de nivel superior.
4.  Para abrir el cuadro de texto **ADD DOMAIN NAME**, escriba el nombre de dominio y, a continuación, haga clic en **add**.
5.  Haga clic en **Next** para abrir la página de diálogo **Verify your domain name**.

    ![Google\_Tutorial\_11](./media/integration-azure-google-apps/Google_Tutorial_11.png)

6.  Seleccione un valor en **RECORD TYPE** y, a continuación, registre el registro seleccionado en su archivo de zona DNS.

    ![Google\_Tutorial\_12](./media/integration-azure-google-apps/Google_Tutorial_12.png)

7.  Al usar el comando **nslookup**, debe comprobar si el registro DNS se ha registrado correctamente.

    ![Google\_Tutorial\_13](./media/integration-azure-google-apps/Google_Tutorial_13.png)

Siga estos pasos para agregar un dominio personalizado en Google Apps:
----------------------------------------------------------------------

1.  Inicie sesión en su inquilino de Google Apps.
2.  En la **Consola de administración**, haga clic en **Domains**.

    ![Google\_Tutorial\_14](./media/integration-azure-google-apps/Google_Tutorial_14.png)

3.  Haga clic en **Agregar un dominio personalizado**.

    ![Google\_Tutorial\_15](./media/integration-azure-google-apps/Google_Tutorial_15.png)

4.  Haga clic en **Use a domain you already own** y, a continuación, en **Continue**.

    ![Google\_Tutorial\_16](./media/integration-azure-google-apps/Google_Tutorial_16.png)

5.  Escriba el nombre de dominio personalizado y, a continuación, haga clic en **Continue**.

    ![Google\_Tutorial\_17](./media/integration-azure-google-apps/Google_Tutorial_17.png)

6.  Complete los pasos para comprobar a quién pertenece el dominio.

    Si ya ha configurado un inicio de sesión único federado, debe actualizar la URL del inquilino de Google Apps en su configuración de inicio de sesión único federado.

Configuración del aprovisionamiento de usuario
==============================================

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Google Apps.

Siga estos pasos para configurar el aprovisionamiento de usuario:
-----------------------------------------------------------------

1.  En el panel de navegación izquierdo del Portal de administración de Azure, seleccione **Active Directory** para abrir la página de diálogo **Active Directory**.
2.  En la lista de directorios, seleccione el suyo para abrir la página de configuración del mismo.
3.  Seleccione **APPLICATIONS** en el menú de nivel superior. En esta lista de aplicaciones, seleccione **Google Apps** para abrir el diálogo de configuración de **Google Apps**.
4.  Para abrir el diálogo **CONFIGURE ACCOUNT SYNC**, haga clic en **Configure account sync**.
5.  En la página de diálogo **CONFIGURE ACCOUNT SYNC**, proporcione el nombre de dominio, el nombre de usuario y la contraseña de Google Apps y, a continuación, haga clic en el botón **Next**.

    ![Google\_Tutorial\_18](./media/integration-azure-google-apps/Google_Tutorial_18.png)

6.  En la página de diálogo **Confirmation**, haga clic en el botón **Complete** para cerrar el diálogo **CONFIGURE ACCOUNT SYNC**.

Ahora ya puede crear una cuenta de prueba, espere 10 minutos y compruebe si la cuenta se ha sincronizado en Google Apps.

Consulte también [Prácticas recomendadas para administrar las mejoras de acceso a las aplicaciones para Azure Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx) (en inglés)

