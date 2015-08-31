<properties 
	pageTitle="Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory (Xamarin.iOS) | Microsoft Azure" 
	description="Obtenga información acerca de cómo autenticar usuarios para inicio de sesión único con ADAL en su aplicación de Xamarin.iOS." 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="mahender"/>

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Información general

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar la aplicación Xamarin.iOS y concederle acceso a esos permisos.


>[AZURE.NOTE]Este tutorial está pensado para ayudarle a comprender mejor la forma en que Servicios móviles permite la autenticación de Azure Active Directory con inicio de sesión único para aplicaciones Xamarin.iOS. Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles].

##Requisitos previos

Este tutorial requiere lo siguiente:

* XCode 4.5 y iOS 6.0 (o versiones posteriores). 
* Visual Studio con la [extensión Xamarin] o [Xamarin Studio] en OS X.
* Finalización del tutorial [Introducción a los Servicios móviles] o [Introducción a los datos].
* SDK de Servicios móviles de Microsoft Azure
* [Enlace de Xamarin para la biblioteca de autenticación de Active Directory para iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Configuración del servicio móvil para exigir autenticación

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Incorporación de código de autenticación a la aplicación cliente

1. Agregue el enlace de Xamarin para la biblioteca de autenticación de Active Directory al proyecto de Xamarin.iOS. En Visual Studio 2013, haga clic con el botón derecho en **Referencias** y seleccione **Agregar referencia**. Después, busque la biblioteca de enlace y haga clic en **Agregar**. Asegúrese de agregar también los guiones gráficos del origen ADAL.

2. Agregue lo siguiente a la clase QSTodoService:

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

6. En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó su aplicación; el formato debe ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure].

7. En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de su servicio móvil. Si ha seguido el tema [Registro en Azure Active Directory], el URI de id. de aplicación debe ser parecido a https://todolist.azure-mobile.net/login/aad.

8. En el código del método anterior `AuthenticateAsync`, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que ha copiado de la aplicación cliente nativa.

9. En el código del método anterior `AuthenticateAsync`, reemplace **INSERT-REDIRECT-URI-HERE** por el extremo /login/done para su servicio móvil. Debe ser similar a https://todolist.azure-mobile.net/login/done.


3. En QSTodoListViewController, modifique **ViewDidLoad** agregando el siguiente código justo antes de la llamada a RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##Prueba del cliente con autenticación

1. En el menú Ejecutar, haga clic en Ejecutar para iniciar la aplicación. 
2. Recibirá un mensaje para que inicie sesión en Azure Active Directory.  
3. La aplicación se autentica y devuelve los elementos todo.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Introducción a los datos]: mobile-services-ios-get-started-data.md
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Registro en Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Enlace de Xamarin para la biblioteca de autenticación de Active Directory para iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[extensión Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download

<!---HONumber=August15_HO8-->