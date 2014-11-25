<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# para Tienda Windows" >C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar la aplicación Xamarin.iOS y concederle acceso a esos permisos.

> [WACOM.NOTE] Este tutorial está pensado para ayudarle a comprender mejor la forma en que Servicios móviles permite la autenticación de Azure Active Directory con inicio de sesión único para aplicaciones Xamarin.iOS. Si esta es la primera vez que usa los Servicios móviles, complete el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Registro del servicio móvil en Azure Active Directory][Registro del servicio móvil en Azure Active Directory]
2.  [Registro de la aplicación en Azure Active Directory][Registro de la aplicación en Azure Active Directory]
3.  [Configuración del servicio móvil para exigir autenticación][Configuración del servicio móvil para exigir autenticación]
4.  [Incorporación de código de autenticación a la aplicación cliente][Incorporación de código de autenticación a la aplicación cliente]
5.  [Prueba del cliente mediante autenticación][Prueba del cliente mediante autenticación]

Este tutorial requiere lo siguiente:

-   XCode 4.5 y iOS 6.0 (o versiones posteriores).
-   Visual Studio con la [extensión Xamarin][extensión Xamarin] o [Xamarin Studio][Xamarin Studio] en OS X.
-   Finalización del tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].
-   SDK de Servicios móviles de Microsoft Azure
-   [Enlace de Xamarin para la biblioteca de autenticación de Active Directory para iOS][Enlace de Xamarin para la biblioteca de autenticación de Active Directory para iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

1.  Agregue el enlace de Xamarin para la biblioteca de autenticación de Active Directory al proyecto de Xamarin.iOS En Visual Studio 2013, haga clic con el botón secundario en **Referencias** y seleccione **Agregar referencia**. Después, busque la biblioteca de enlace y haga clic en **Agregar**. Asegúrese de agregar también los guiones gráficos del origen ADAL.

2.  Agregue lo siguiente a la clase QSTodoService:

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

3.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado su aplicación; el formato debe ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure][Portal de administración de Azure].

4.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de su dispositivo móvil. Si ha seguido el tema [Registro en Azure Active Directory][Registro en Azure Active Directory], el URI de id. de aplicación debe ser parecido a <https://todolist.azure-mobile.net/login/aad>.

5.  En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que ha copiado de la aplicación cliente nativa.

6.  En el código del método `AuthenticateAsync` anterior, reemplace **INSERT-REDIRECT-URI-HERE** por el extremo /login/done para su servicio móvil. Debe ser similar a <https://todolist.azure-mobile.net/login/done>.

7.  En QSTodoListViewController, modifique **ViewDidLoad** agregando el siguiente código justo antes de la llamada a RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Prueba del cliente mediante autenticación

1.  En el menú Ejecutar, haga clic en Ejecutar para iniciar la aplicación.
2.  Recibirá un mensaje para que inicie sesión en Azure Active Directory.
3.  La aplicación se autentica y devuelve los elementos todo.

   ![][0]

 


  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Registro del servicio móvil en Azure Active Directory]: #register-mobile-service-aad
  [Registro de la aplicación en Azure Active Directory]: #register-app-aad
  [Configuración del servicio móvil para exigir autenticación]: #require-authentication
  [Incorporación de código de autenticación a la aplicación cliente]: #add-authentication-code
  [Prueba del cliente mediante autenticación]: #test-client
  [extensión Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Introducción a los datos]: /es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Enlace de Xamarin para la biblioteca de autenticación de Active Directory para iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
