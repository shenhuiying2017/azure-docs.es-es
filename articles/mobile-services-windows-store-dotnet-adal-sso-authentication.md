<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar su aplicación de la Tienda Windows y otorgar acceso a esos permisos.

> [WACOM.NOTE] Este tutorial está destinado a ayudarle a comprender mejor cómo los Servicios móviles le permiten la autenticación de Azure Active Directory en un solo inicio de sesión para aplicaciones de la Tienda Windows. Si esta es la primera vez que usa los Servicios móviles, complete el tutorial [Introducción a los Servicios móviles][].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Registro del servicio móvil en Azure Active Directory][]
2.  [Registro de la aplicación en Azure Active Directory][]
3.  [Configuración del servicio móvil para exigir autenticación][]
4.  [Incorporación de código de autenticación a la aplicación cliente][]
5.  [Prueba del cliente mediante autenticación][]

Este tutorial requiere lo siguiente:

-   Visual Studio 2013 en Windows 8.1.
-   Finalización del tutorial [Introducción a los Servicios móviles][] o [Introducción a los datos][].
-   Paquete NuGet del SDK de Servicios móviles de Microsoft Azure
-   Paquete NuGet de la biblioteca de autenticación de Active Directory

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][]]

## <a name="register-app-aad"></a>Registro de la aplicación en Azure Active Directory

Para registrar la aplicación en Azure Active Directory, debe asociarla a la Tienda Windows y tener un identificador de seguridad de paquete (SID) para la aplicación. El SID del paquete se registra con la configuración de la aplicación nativa en Azure Active Directory.

### Asociación de la aplicación con un nuevo nombre de aplicación de la tienda

1.  En Visual Studio, haga clic con el botón secundario en el proyecto de aplicación y haga clic en **Tienda** y en **Asociar aplicación con la Tienda**

    ![][]

2.  Inicie sesión en su cuenta del Centro de desarrollo.

3.  Escriba el nombre de la aplicación que desea reservar para esta y haga clic en **Reservar**.

    ![][1]

4.  Seleccione el nuevo nombre de la aplicación y haga clic en **Siguiente**.

5.  Haga clic en **Asociar** para asociar la aplicación con el nombre de la tienda.

### Recuperación del SID del paquete para la aplicación

Ahora, debe recuperar el SID del paquete que se configurará con la configuración de la aplicación nativa.

1.  Inicie sesión en su [panel del Centro de desarrollo de Windows][] y haga clic en **Editar** en la aplicación.

    ![][2]

2.  Luego haga clic en **Servicios**.

    ![][3]

3.  Después haga clic en **sitio de Live Services**.

    ![][4]

4.  Copie el SID del paquete de la parte superior de la página.

    ![][5]

### Creación del registro de la aplicación nativa

1.  Diríjase a **Active Directory** en el [Portal de administración de Azure][] y haga clic en el directorio.

    ![][6]

2.  Haga clic en la pestaña **Aplicaciones** que aparece en la parte superior y, a continuación, haga clic en **ADD** para agregar una aplicación.

    ![][7]

3.  Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

4.  En el asistente para agregar aplicaciones, escriba el valor de **Nombre** para la aplicación y haga clic en el tipo **Aplicación de cliente nativo**. A continuación, haga clic para continuar.

    ![][8]

5.  En el cuadro **URI de redirección**, pegue el SID del paquete de la aplicación que copió anteriormente y luego haga clic para completar el registro de la aplicación nativa.

    ![][9]

6.  Haga clic en la pestaña **Configurar** de la aplicación nativa y copie el **Id. de cliente**. Lo necesitará más adelante.

    ![][10]

7.  Desplácese hacia abajo, hasta la sección **permisos a otras aplicaciones**, y conceda acceso total a la aplicación de servicio móvil que registró anteriormente. A continuación, haga clic en **Guardar**.

    ![][11]

El servicio móvil está ahora configurado en AAD para recibir inicios de sesión únicos desde su aplicación.

## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][]]

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

1.  Abra su proyecto de aplicación cliente de la Tienda Windows en Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget][]]

1.  En la ventana del Explorador de soluciones de Visual Studio, abra el archivo MainPage.xaml.cs y agregue las siguientes instrucciones using:

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;

2.  Agregue el siguiente código a la clase MainPage que declara el método `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

3.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado su aplicación; el formato debe ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure][].

4.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de su dispositivo móvil. Si ha seguido el tema [Registro en Azure Active Directory][], el URI de id. de aplicación debe ser parecido a <https://todolist.azure-mobile.net/login/aad>.

5.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que ha copiado de la aplicación cliente nativa.

6.  En la ventana del Explorador de soluciones de Visual Studio, abra el archivo Package.appxmanifest en el proyecto cliente. Haga clic en la pestaña **Funcionalidades** y habilite **Aplicación de empresa** y **Redes privadas (cliente y servidor)**. Guarde el archivo.

    ![][12]

7.  En el archivo MainPage.cs, actualice el controlador de eventos `OnNavigatedTo` para llamar al método `AuthenticateAsync` de la manera siguiente.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }

## <a name="test-client"></a>Prueba del cliente mediante autenticación

1.  En Visual Studio, ejecute la aplicación cliente.
2.  Recibirá un mensaje para que inicie sesión en Azure Active Directory.
3.  La aplicación se autentica y devuelve los elementos todo.

    ![][13]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "C# para Tienda Windows"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Registro del servicio móvil en Azure Active Directory]: #register-mobile-service-aad
  [Registro de la aplicación en Azure Active Directory]: #register-app-aad
  [Configuración del servicio móvil para exigir autenticación]: #require-authentication
  [Incorporación de código de autenticación a la aplicación cliente]: #add-authentication-code
  [Prueba del cliente mediante autenticación]: #test-client
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  []: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
  [1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
  [panel del Centro de desarrollo de Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734
  [2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
  [3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
  [4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
  [5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
  [7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
  [8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
  [9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
  [10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
  [11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-dotnet-adal-install-nuget]: ../includes/mobile-services-dotnet-adal-install-nuget.md
  [Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
  [13]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png
