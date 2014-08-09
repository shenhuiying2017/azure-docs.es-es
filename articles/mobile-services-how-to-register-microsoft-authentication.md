<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="" />

Registro de sus aplicaciones para usar un inicio de sesión de la cuenta Microsoft
=================================================================================

Este tema indica cómo registrar sus aplicaciones para que puedan usar Live Connect como proveedor de autenticación para los Servicios móviles de Azure.

> [WACOM.NOTE]Si desea proporcionar autenticación centrada en el cliente para inicio de sesión único (SSO) o insertar notificaciones de una aplicación de la Tienda Windows, considere la opción de registrar su aplicación con la Tienda Windows. Para obtener más información, consulte [Registro de sus aplicaciones de la Tienda Windows para la autenticación de Live Connect de Windows](/es-es/develop/mobile/how-to-guides/register-for-single-sign-on).

1.  Vaya a la página [Mis aplicaciones](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro para desarrolladores de Live Connect e inicie sesión con cuenta Microsoft, si procede.

2.  Haga clic en **Create application** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **I accept**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png) 

   	Así se registra la aplicación con Live Connect.

3.  Haga clic en la **página de configuración de la aplicación**, después en **API Settings** y, a continuación, anote los valores de **Client ID** y **secreto de cliente**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota de seguridad</b>
	<p>El secreto de cliente es una credencial de seguridad importante. No comparta su secreto de cliente con nadie ni lo distribuya con su aplicación.</p>
    </div>

1.  En **Redirect domain**, escriba la URL de su servicio móvil y, a continuación, haga clic en **Save**.

De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación proporcionando los valores de Id. de cliente y secreto de cliente a Servicios móviles.

