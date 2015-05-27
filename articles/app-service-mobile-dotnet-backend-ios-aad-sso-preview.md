<properties 
        pageTitle="Autenticación de usuarios de la aplicación iOS con el inicio de sesión único de Azure Active Directory" 
        description="Obtenga información acerca de cómo registrar usuarios en la aplicación iOS con la biblioteca de autenticación de Active Directory." 
        documentationCenter="Mobile" 
        authors="mattchenderson" 
        services="app-service\mobile" 
        manager="dwrede" />

<tags ms.service="app-service"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios" 
ms.devlang="objective-c" 
ms.topic="article" 
ms.date="02/20/2015" 
ms.author="mahender" />

# Incorporación del inicio de sesión único de Azure Active Directory a la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../includes/app-service-mobile-selector-aad-sso.md)]

En este tutorial, agregará la autenticación al proyecto de inicio rápido mediante la biblioteca de autenticación de Active Directory.

Para poder autenticar usuarios, debe registrar su aplicación en el inquilino de Azure Active Directory (AAD). Para ello, debe realizar dos pasos. Primero, debe registrar su Servicio de aplicaciones y exponer los permisos sobre él. En segundo lugar, debe registrar la aplicación iOS y otorgar acceso a esos permisos.

Este tutorial requiere lo siguiente:

* XCode 4.5 y iOS 6.0 (o versiones posteriores).
* Finalización del tutorial [Introducción a las aplicaciones móviles].
* SDK de Servicios móviles de Microsoft Azure
* La [biblioteca de autenticación de Active Directory para iOS]

## <a name="register-application"></a>Registro de la aplicación con Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Configuración de la aplicación para que requiera autenticación

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

## <a name="add-adal"></a>Incorporación de una referencia a la biblioteca de autenticación de Active Directory

1. Descargue la [biblioteca de autenticación de Active Directory para iOS].

2. En Xcode Navigator, seleccione el proyecto y haga clic en **Archivo** y elija **Agregar archivos a...** Vaya al sitio donde descargó la biblioteca y seleccione **ADALiOS.xcodeproj**.

3. Vuelva a seleccionar el proyecto y abra la pestaña **Configuración de generación**. Vaya a la sección **Vinculando** y agregue `-ObjC` a **Otras marcas del enlazador**.

4. Seleccione la pestaña **Fases de generación**. En **Dependencias de destino**, agregue `ADALiOS`.

5. En **Vincular binario con bibliotecas**, agregue `libADALiOS.a`.

Ahora podrá hacer referencia a la biblioteca de autenticación de Active Directory en el proyecto.

## <a name="add-authentication-code"></a>Incorporación de código de autenticación a la aplicación cliente

2. En el elemento QSTodoListViewController, incluya ADAL con el siguiente código:

        #import "ADALiOS/ADAuthenticationContext.h"

3. Después agregue el método siguiente:

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

4. En el código del método anterior `loginAndGetData`, sustituya **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó su aplicación; el formato debe ser https://login.windows.net/tenant-name.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de administración de Azure].

5. En el código del método `loginAndGetData` anterior, sustituya **INSERT-RESOURCE-URI-HERE** por el **URI de id. de aplicación** de la Aplicación móvil. Si ha seguido el tema [Configuración de la aplicación móvil con Azure Active Directory], el URI del id. de aplicación debe ser parecido a https://contosogateway.azurewebsites.net/login/aad.

6. En el código del método `loginAndGetData` anterior, sustituya **INSERT-CLIENT-ID-HERE** por el Id. de cliente que ha copiado de la aplicación cliente nativa.

7. En el código del método `loginAndGetData` anterior, sustituya **INSERT-REDIRECT-URI-HERE** por el extremo /login/done de su puerta de enlace del Servicio de aplicaciones. Debe ser similar a https://contosogateway.azurewebsites.net/login/done.

8. En QSTodoListViewController, modifique `viewDidLoad` sustituyendo `[self refresh]` por lo siguiente:

        [self loginAndGetData];

## <a name="test-client"></a>Prueba del cliente con autenticación

1. En el menú Producto, haga clic en Ejecutar para iniciar la aplicación.
2. Recibirá un mensaje para que inicie sesión en Azure Active Directory.  
3. La aplicación se autentica y devuelve los elementos todo.

<!-- URLs. -->
[Configuración de la aplicación móvil con Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[biblioteca de autenticación de Active Directory para iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
[Introducción a las aplicaciones móviles]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

<!--HONumber=54-->