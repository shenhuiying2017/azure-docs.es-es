<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="" />

Registro de las aplicaciones para usar un inicio de sesión de la cuenta de Azure Active Directory
=================================================================================================

Este tema indica cómo registrar las aplicaciones para que puedan usar Azure Active Directory como proveedor de autenticación para los Servicios móviles de Azure.

**Nota:**

Si desea proporcionar autenticación centrada en el cliente para inicio de sesión único (SSO) o insertar notificaciones de una aplicación de la Tienda Windows, considere la opción de registrar su aplicación con la Tienda Windows. Para obtener más información, consulte [Registro de sus aplicaciones de la Tienda Windows para la autenticación de Live Connect de Windows](/en-us/develop/mobile/how-to-guides/register-for-single-sign-on).

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).

2.  Diríjase a **Active Directory** en el portal de administración y haga clic en el directorio.

    ![](./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png)

3.  Haga clic en la pestaña **Aplicaciones** y después haga clic en **Add an App**.

    ![](./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png)

4.  Siga las indicaciones del asistente para la nueva aplicación eligiendo **Web Application And/Or Web API** para XXX. Habilite el inicio de sesión único. Cuando se le solicite la **dirección URL de la aplicación**, pegue la dirección URL de la aplicación de servicios móviles.

5.  *** MORE TO COME ***

De este modo ya estará listo para usar una cuenta Azure Active Directory para autenticarse en su aplicación proporcionando los valores de identificador de cliente y secreto de cliente a Servicios móviles.

