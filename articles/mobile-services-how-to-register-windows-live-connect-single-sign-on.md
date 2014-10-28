<properties pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro de las aplicaciones de la Tienda Windows para usar el inicio de sesión único de Live Connect de Windows

En este tema se explica cómo registrar la aplicación con la Tienda Windows para poder usar Live Connect para el inicio de sesión único como el proveedor de identidades de Servicios móviles de Azure. Este paso también es necesario para usar notificaciones de inserción.

<div class="dev-callout"><b>Nota:</b>
<p>No es necesario que registre la aplicaci&oacute;n con la Tienda Windows para poder usar la cuenta Microsoft para la autenticaci&oacute;n antes de publicar la aplicaci&oacute;n. Cuando la aplicaci&oacute;n de la Tienda Windows no requiere inicio de sesi&oacute;n &uacute;nico o notificaciones de inserci&oacute;n, puede simplemente registrar la aplicaci&oacute;n con Live Connect para usar un inicio de sesi&oacute;n de la cuenta Microsoft.  Para obtener m&aacute;s informaci&oacute;n, consulte <a href="/es-es/develop/mobile/how-to-guides/register-for-microsoft-authentication">Registro de sus aplicaciones de la Tienda Windows para usar un inicio de sesi&oacute;n de la cuenta Microsoft</a>.</p>
</div>

1.  Si aún no ha registrado la aplicación, vaya a la [página Enviar una aplicación][] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

    ![][]

2.  Escriba el nombre de la aplicación en **App name**, haga clic en **Reserve app name** y, a continuación, haga clic en **Save**.

    ![][1]

    Se crea un nuevo registro de la Tienda Windows para su aplicación.

3.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a los Servicios móviles][].

4.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

    ![][2]

    Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

6.  Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Next** y, a continuación, en **Associate**.

    ![][3]

    Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7.  Desplácese hasta la página [My Applications][] del Centro para desarrolladores de Live Connect y haga clic en su aplicación en la lista **My applications**.

    ![][4]

8.  Haga clic en **Edit settings**, después en **API Settings** y anote el valor de **Secreto de cliente**.

    ![][5]

    <div class="dev-callout"><b>Nota de seguridad</b>
<p>El secreto de cliente es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicaci&oacute;n.</p>
</div>

9.  En **Redirect domain**, escriba la dirección URL de su servicio móvil que anotó en el paso 8 y, a continuación, haga clic en **Save**.

Ahora está preparado para integrar la autenticación a su aplicación mediante el uso de Live Connect. Servicios móviles proporciona los dos siguientes métodos para autenticar usuarios mediante Live Connect:

-   Inicio de sesión único para aplicaciones de la Tienda Windows. En este método, los usuarios solo deben autorizar la autenticación en la aplicación una vez con Live Connect; a continuación, Windows administra las credenciales, según las preferencias del usuario. Para obtener más información, consulte [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect][].

-   Autenticación básica. Este método, que es compatible con diversos proveedores de autenticación, requiere que los usuarios inicien sesión cada vez que se inicia la aplicación. Para obtener más información, consulte [Introducción a la autenticación][].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Registro de sus aplicaciones de la Tienda Windows para usar un inicio de sesión de la cuenta Microsoft]: /es-es/develop/mobile/how-to-guides/register-for-microsoft-authentication
  [página Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started
  [2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
  [3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png
  [My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [4]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
  [5]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png
  [Inicio de sesión único para aplicaciones de la Tienda Windows mediante Live Connect]: /es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet
