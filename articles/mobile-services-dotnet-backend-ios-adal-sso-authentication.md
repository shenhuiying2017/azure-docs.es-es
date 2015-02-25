<properties pageTitle="Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory (iOS) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo autenticar usuarios para inicio de sesión único con ADAL en su aplicación de iOS." documentationCenter="ios" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="wesmc,mahender"/>

# Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar a los usuarios, debe registrar su aplicación en Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su servicio móvil y exponer los permisos sobre él. En segundo lugar, debe registrar la aplicación de iOS y conceder acceso a esos permisos.


>[AZURE.NOTE] Este tutorial está pensado para ayudarle a comprender mejor cómo los Servicios móviles permiten autenticación de Azure Active Directory con inicio de sesión único para aplicaciones iOS. Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1. [Registro del servicio móvil con Azure Active Directory]
2. [Registro de la aplicación con Azure Active Directory]
3. [Configuración del servicio móvil para exigir autenticación]
4. [Incorporación de código de autenticación a la aplicación cliente]
5. [Prueba del cliente mediante autenticación]

Este tutorial requiere lo siguiente:

* XCode 4.5 y iOS 6.0 (o versiones posteriores).
* Realización del tutorial [Introducción a los Servicios móviles] o del tutorial [Introducción a los datos].
* SDK de Servicios móviles de Microsoft Azure
* La [biblioteca de autenticación de Active Directory para iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configuración del servicio móvil para exigir autenticación

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

1. Descargue la [biblioteca de autenticación de Active Directory para iOS] e inclúyala en su proyecto. Asegúrese de agregar también los guiones gráficos del origen ADAL.

2. En el elemento QSTodoListViewController, incluya ADAL con el siguiente código:

        #import "ADALiOS/ADAuthenticationContext.h"

2. Después agregue el método siguiente:

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


6. En el código del método `loginAndGetData` anterior, sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que ha aprovisionado su aplicación, con el formato https://login.windows.net/tenant-name.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure].

7. En el código del método `loginAndGetData` anterior, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de su servicio móvil. Si ha seguido el tema [Registro en Azure Active Directory], su URI de id. de aplicación debe parecerse a https://todolist.azure-mobile.net/login/aad.

8. En el código del método `loginAndGetData` anterior, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que copió de la aplicación cliente nativa.

9. En el código del método `loginAndGetData` anterior, sustituya **INSERT-REDIRECT-URI-HERE** por el extremo /login/done de su servicio móvil. Debe ser similar a https://todolist.azure-mobile.net/login/done.


3. En QSTodoListViewController, modifique `ViewDidLoad` sustituyendo `[self refresh]` por lo siguiente:

        [self loginAndGetData];

## <a name="test-client"></a>Prueba del cliente mediante autenticación

1. En el menú Producto, haga clic en Ejecutar para iniciar la aplicación.
2. Recibirá un mensaje para que inicie sesión en Azure Active Directory.  
3. La aplicación se autentica y devuelve los elementos todo.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Registro del servicio móvil con Azure Active Directory]: #register-mobile-service-aad
[Registro de la aplicación con Azure Active Directory]: #register-app-aad
[Configuración del servicio móvil para exigir autenticación]: #require-authentication
[Incorporación de código de autenticación a la aplicación cliente]: #add-authentication-code
[Prueba del cliente mediante autenticación]: #test-client

<!-- URLs. -->
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data/
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Biblioteca de autenticación de Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios


<!--HONumber=42-->
