---
title: "Introducción a iOS de Azure AD | Microsoft Docs"
description: "Cómo crear una aplicación iOS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 09/16/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 1865043ca9c9019b9813f11eb4a55f7f16d79287
ms.openlocfilehash: b0ae22b8af23c50f25740901db4a8ab9cad045be


---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrar Azure AD en una aplicación iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD proporciona la biblioteca de autenticación de Active Directory (ADAL) para los clientes de iOS que necesitan tener acceso a recursos protegidos.  El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso.  Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de Objective-C que:

* Obtenga tokens de acceso para llamar a la API de gráficos de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Buscar un directorio para los usuarios con un alias determinado

Para crear la aplicación de trabajo completa, deberá:

1. Registrar la aplicación con Azure AD
2. Instalar y configurar ADAL
3. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación.  Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

> [!TIP]
> Pruebe la versión preliminar de nuestro nuevo [portal para desarrolladores](https://identity.microsoft.com/Docs/iOS) , que lo ayudará a empezar a trabajar con Azure Active Directory en tan solo unos minutos.  El portal para desarrolladores lo guiará a través del proceso de registro de una aplicación y de integración de Azure AD en el código.  Cuando haya terminado, tendrá una aplicación sencilla que podrá autenticar a los usuarios del inquilino, así como un back-end con capacidad para aceptar tokens y realizar validaciones.
>
>

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. Determinación de cuál será el URI de redireccionamiento para iOS*
Para iniciar de forma segura sus aplicaciones en ciertos escenarios SSO, es necesaria la creación de un **URI de redireccionamiento** en un formato determinado. Un URI de redirección se utiliza para garantizar que los tokens se devuelven a la aplicación correcta que los solicitó.

El formato de iOS para un URI de redireccionamiento es:

```
<app-scheme>://<bundle-id>
```

* **aap esquema** : se registra en el proyecto de XCode. Es el modo en que otras aplicaciones pueden llamarlo. Puede encontrarlo en Info.plist -> Tipos de URL -> Identificador de URL. Debe crear uno si aún no tiene ninguno configurado.
* **bundle-id** : es el identificador de paquete que se encuentra en la "identity" de la configuración del proyecto en XCode.

Un ejemplo de este código de inicio rápido sería: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***.

## <a name="2-register-the-directorysearcher-application"></a>*2. Registro de la aplicación DirectorySearcher*
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API Graph de Azure AD:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la parte superior derecha, haga clic en su cuenta y en la lista **Directorio**, elija un inquilino de Active Directory que tenga permisos de administrador.
3. Escriba **registros de aplicaciones** en el filtro de búsqueda.
4. Haga clic en **Registros de aplicaciones** y elija **Agregar**.
5. Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**. El **nombre** de la aplicación describirá su aplicación a los usuarios finales. El **identificador URI de redirección** es el identificador URI que se usa para devolver tokens a su aplicación. Escriba un valor específico para la aplicación. Haga clic en **Crear** para crear la aplicación.
6. Mientras sigue en Azure Portal, elija la aplicación, haga clic en **Configuración** y elija **Propiedades**.
7. Busque el valor de identificador de la aplicación y cópielo en el portapapeles.
8. Configuración de los permisos de la aplicación: en el menú de configuración, elija la sección "Permisos necesarios", haga clic en **Agregar**, luego, en **Seleccionar una API** y, finalmente, seleccione "Windows Azure Active Directory" (esta es la API de AADGraph). A continuación, haga clic en **Seleccionar permisos** y seleccione "Leer datos de directorio".

## <a name="3-install--configure-adal"></a>*3. Instalación y configuración de ADAL*
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad.  Para que ADAL pueda comunicarse con Azure AD, hay que proporcionarle información sobre el registro de la aplicación.

* Comience agregando ADAL al proyecto de DirectorySearcher utilizando Cocapods.

```
$ vi Podfile
```
Agregue lo siguiente a este podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Ahora cargue el podfile mediante cocoapods. Esto creará una nueva área de trabajo de XCode que cargará.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

* En el proyecto QuickStart, abra el archivo plist `settings.plist`.  Reemplace los valores de los elementos de la sección para que reflejen los valores especificados en el Portal de Azure.  El código hará referencia a estos valores cada vez que use ADAL.
  * `tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `clientId` es el identificador de cliente de la aplicación que copió del portal.
  * El `redirectUri` es la URL de redirección que ha registrado en el portal.

## <a name="4----use-adal-to-get-tokens-from-aad"></a>*4.    Uso de ADAL para obtener tokens de AAD*
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a un completionBlock `+(void) getToken : ` y ADAL se encarga del resto.  

* En el proyecto `QuickStart`, abra `GraphAPICaller.m` y busque el comentario `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` en la parte superior.  Este es el lugar en el que se pasan a ADAL mediante CompletionBlock las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

* Ahora tenemos que utilizar este token para buscar usuarios en el gráfico. Busque el comentario `// TODO: implement SearchUsersList`. Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
                 }
                 else
                 {
                     completionBlock(nil, error);
                 }

             }];
         }
     }];

}

```
* Cuando la aplicación solicita un token mediante una llamada a `getToken(...)`, ADAL intentará devolver un token sin solicitar al usuario las credenciales.  Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta.  Si ADAL no puede devolver un token por alguna razón, mostrará una `AdalException`.
* Observe que el objeto `AuthenticationResult` contiene un objeto `tokenCacheStoreItem` que puede usarse para recopilar información puede necesitar la aplicación.  En QuickStart, `tokenCacheStoreItem` se utiliza para determinar si ya se ha producido la autenticación.

## <a name="step-5-build-and-run-the-application"></a>Paso 5: compilación y ejecución de la aplicación
¡Enhorabuena! Ahora tiene una aplicación de iOS operativa que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios.  Ejecute la aplicación QuickStart e inicie sesión con uno de esos usuarios.  Busque otros usuarios según su UPN.  Cierre la aplicación y vuelva a ejecutarla.  Observe cómo la sesión del usuario permanece intacta.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación.  Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc.  Todo lo que necesita saber es una única llamada de API, `getToken`.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)puede ver el ejemplo finalizado (sin sus valores de configuración).  

## <a name="additional-scenarios"></a>Otros escenarios
Ahora puede pasar a otros escenarios.  También puede probar lo siguiente:

* [Proteger una API web de Node.js con Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Obtener información sobre [cómo habilitar en iOS la autenticación SSO entre aplicaciones mediante ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Nov16_HO5-->


