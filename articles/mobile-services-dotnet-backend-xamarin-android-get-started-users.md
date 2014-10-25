<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Introducción a la autenticación en Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="C# para Tienda Windows">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="iOS">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android" class="current">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1.  [Registro de la aplicación para la autenticación y configuración de Servicios móviles][Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2.  [Restricción de los permisos de tabla a usuarios autenticados][Restricción de los permisos de tabla a usuarios autenticados]
3.  [Incorporación de autenticación a la aplicación][Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

## <a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]
<ol start="6">
<li><p>En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o simulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.</p>

    <p>Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.</p></li></ol>

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## <a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1.  Agregue la siguiente propiedad a la clase **TodoActivity**:

            private MobileServiceUser user;

2.  Agregue el método siguiente a la clase **TodoActivity**:

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    De este modo se crea un método para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Aparecerá un diálogo que muestra el identificador del usuario autenticado.

    <div class="dev-callout"><b>Nota:</b>
<p>Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al m&eacute;todo <strong>LoginAsync</strong> anterior por uno de los siguientes: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> o <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  En el método **OnCreate**, agregue la línea de código siguiente después del código que crea instancias del objeto `MobileServiceClient`.

        // Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

    Esta llamada inicia el proceso de autenticación y lo espera de manera asincrónica.

4.  En el menú **Run**, haga clic en **Run** para iniciar la aplicación e inicie sesión con el proveedor de identidades que haya elegido.

    Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

<!-- ## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   --> 

<!-- Anchors. --> 
<!-- URLs. -->

  [C\# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/ "Back-end de JavaScript"
  [Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
  [Restricción de los permisos de tabla a usuarios autenticados]: #permissions
  [Incorporación de autenticación a la aplicación]: #add-authentication
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
