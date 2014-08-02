<properties linkid="develop-mobile-how-to-guides-register-for-single-sign-on" urlDisplayName="Register for single sign on" pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="" solutions="" manager="" editor="" />

Registro de las aplicaciones de la Tienda Windows para usar el inicio de sesión único de Live Connect de Windows
================================================================================================================

En este tema se explica cómo registrar la aplicación con la Tienda Windows para poder usar Live Connect para el inicio de sesión único como el proveedor de identidades de Servicios móviles de Azure. Este paso también es necesario para usar notificaciones de inserción.

**Nota:**

No es necesario que registre la aplicación con la Tienda Windows para poder usar la cuenta Microsoft para la autenticación antes de publicar la aplicación. Cuando la aplicación de la Tienda Windows no requiere inicio de sesión único o notificaciones de inserción, puede simplemente registrar la aplicación con Live Connect para usar un inicio de sesión de la cuenta Microsoft. Para obtener más información, consulte [Registro de sus aplicaciones de la Tienda Windows para usar un inicio de sesión de la cuenta Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication).

1.  Si aún no ha registrado la aplicación, vaya a la [página Enviar una aplicación](http://go.microsoft.com/fwlink/p/?LinkID=266582) en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	 ![][0]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

   	 ![][1]

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles](/en-us/develop/mobile/tutorials/get-started).

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

  	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png)

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

6.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

   	![][3]

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.    

7.  Desplácese hasta la página [My Applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) del Centro para desarrolladores de Live Connect y haga clic en su aplicación en la lista **My applications**.

   	![][6] 

8.  Haga clic en **Edit settings**, después en **API Settings** y anote el valor de **Secreto de cliente**.

   	![](./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota de seguridad</b>
    <p>El secreto de cliente es una credencial de seguridad importante. No comparta su secreto de cliente con nadie ni lo distribuya con su aplicación.</p>
    </div>
9.  En **Redirect domain**, escriba la dirección URL de su servicio móvil que anotó en el paso 8 y, a continuación, haga clic en **Save**.

Ahora está preparado para integrar la autenticación a su aplicación mediante el uso de Live Connect. Servicios móviles proporciona los dos siguientes métodos para autenticar usuarios mediante Live Connect:

-   Inicio de sesión único para aplicaciones de la Tienda Windows. En este método, los usuarios solo deben autorizar la autenticación en la aplicación una vez con Live Connect; a continuación, Windows administra las credenciales, según las preferencias del usuario. Para obtener más información, consulte [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

-   Autenticación básica. Este método, que es compatible con diversos proveedores de autenticación, requiere que los usuarios inicien sesión cada vez que se inicia la aplicación. Para obtener más información, consulte [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet).


<!-- Images. -->
[0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
[3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png


[6]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
[7]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png