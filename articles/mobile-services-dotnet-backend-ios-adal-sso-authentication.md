<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="C# para Tienda Windows" >C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar la aplicación de iOS y conceder acceso a esos permisos.

> [WACOM.NOTE] Este tutorial está pensado para ayudarle a comprender mejor cómo Servicios móviles permite autenticación de Azure Active Directory con inicio de sesión único para aplicaciones de iOS. Si esta es la primera vez que usa los Servicios móviles, complete el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Registro del servicio móvil en Azure Active Directory][Registro del servicio móvil en Azure Active Directory]
2.  [Registro de la aplicación en Azure Active Directory][Registro de la aplicación en Azure Active Directory]
3.  [Configuración del servicio móvil para exigir autenticación][Configuración del servicio móvil para exigir autenticación]
4.  [Incorporación de código de autenticación a la aplicación cliente][Incorporación de código de autenticación a la aplicación cliente]
5.  [Prueba del cliente mediante autenticación][Prueba del cliente mediante autenticación]

Este tutorial requiere lo siguiente:

-   XCode 4.5 y iOS 6.0 (o versiones posteriores)
-   Finalización del tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles] o [Introducción a los datos][Introducción a los datos].
-   SDK de Servicios móviles de Microsoft Azure
-   [Biblioteca de autenticación de Active Directory para iOS][Biblioteca de autenticación de Active Directory para iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

1.  Descargue la [biblioteca de autenticación de Active Directory para iOS][Biblioteca de autenticación de Active Directory para iOS] e inclúyala en su proyecto. Asegúrese de agregar también los guiones gráficos del origen ADAL.

2.  En el elemento QSTodoListViewController, incluya ADAL con el siguiente código:

        #import "ADALiOS/ADAuthenticationContext.h"

3.  Después agregue el método siguiente:

        - (void) loginAndGetData
        {    
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];   
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }

4.  En el código del método `loginAndGetData` anterior, reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado su aplicación; el formato debe ser <https://login.windows.net/tenant-name.onmicrosoft.com>. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure][Portal de administración de Azure].

5.  En el código del método anterior `loginAndGetData`, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de su dispositivo móvil. Si ha seguido el tema [Registro en Azure Active Directory][Registro en Azure Active Directory], el URI de id. de aplicación debe ser parecido a <https://todolist.azure-mobile.net/login/aad>.

6.  En el código del método anterior `loginAndGetData`, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que ha copiado de la aplicación cliente nativa.

7.  En el código del método `loginAndGetData` anterior, reemplace **INSERT-REDIRECT-URI-HERE** por el extremo /login/done para su servicio móvil. Debe ser similar a <https://todolist.azure-mobile.net/login/done>.

8.  En el elemento QSTodoListViewController, modifique `ViewDidLoad` reemplazando `[self refresh]` por el siguiente código:

        [self loginAndGetData];

## <a name="test-client"></a>Prueba del cliente mediante autenticación

1.  En el menú Producto, haga clic en Ejecutar para iniciar la aplicación.
2.  Recibirá un mensaje para que inicie sesión en Azure Active Directory.
3.  La aplicación se autentica y devuelve los elementos todo.

   ![][]

<!-- Anchors. --> 
<!-- URLs. -->

  [C\# para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "C# para Tienda Windows"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [Registro del servicio móvil en Azure Active Directory]: #register-mobile-service-aad
  [Registro de la aplicación en Azure Active Directory]: #register-app-aad
  [Configuración del servicio móvil para exigir autenticación]: #require-authentication
  [Incorporación de código de autenticación a la aplicación cliente]: #add-authentication-code
  [Prueba del cliente mediante autenticación]: #test-client
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
  [Biblioteca de autenticación de Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
