---
title: "Introducción a iOS de Azure AD | Microsoft Docs"
description: "Cómo compilar una aplicación iOS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
services: active-directory
documentationcenter: ios
author: brandwe
manager: mtillman
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/30/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 36c6f6d2449d1e137f85e0f657f0399f9df8ee55
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-ad-ios-getting-started"></a>Introducción a iOS de Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Azure Active Directory (Azure AD) proporciona la biblioteca de autenticación de Active Directory (ADAL) para los clientes de iOS que necesitan tener acceso a recursos protegidos. ADAL simplifica el proceso que la aplicación usa para obtener tokens de acceso. Para demostrar lo fácil que es, en este artículo compilaremos una aplicación Objective-C de lista de tareas pendientes que permita realizar lo siguiente:

* Obtiene tokens de acceso para llamar a la API Graph de Azure AD mediante el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Buscar un directorio para los usuarios con un alias determinado

Para compilar la aplicación de trabajo completa, debe realizar estas acciones:

1. Registrar la aplicación con Azure AD
2. Instalar y configurar ADAL.
3. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). También necesita a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).


> [!TIP]
> Pruebe la versión preliminar de nuestro nuevo [portal para desarrolladores](https://identity.microsoft.com/Docs/iOS), que le ayudará a empezar a trabajar con Azure AD en tan solo unos minutos. El portal para desarrolladores lo guiará a través del proceso de registro de una aplicación y de integración de Azure AD en el código. Cuando haya terminado, tendrá una aplicación sencilla que podrá autenticar a los usuarios del inquilino, así como un back-end con capacidad para aceptar tokens y realizar validaciones. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1. Determinación de cuál es el URI de redireccionamiento para iOS
Para iniciar de forma segura sus aplicaciones en ciertos escenarios SSO, es necesaria la creación de un *URI de redireccionamiento* en un formato determinado. Se usa un URI de redireccionamiento para garantizar que los tokens se devuelven a la aplicación correcta que los solicitó.


El formato de iOS para un URI de redireccionamiento es:

```
<app-scheme>://<bundle-id>
```

* **aap-scheme**: está registrado en el proyecto de XCode. Es el modo en que otras aplicaciones pueden llamarlo. Puede encontrarlo en Info.plist -> Tipos de URL -> Identificador de URL. Debe crear uno si aún no tiene ninguno configurado.
* **bundle-id**: el identificador de paquete que se encuentra en "identity" de la configuración del proyecto XCode.

Ejemplo de este código de inicio rápido: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***.

## <a name="2-register-the-directorysearcher-application"></a>2. Registro de la aplicación DirectorySearcher
Para configurar la aplicación para que obtenga tokens, primero debe registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de Azure AD Graph:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En la barra superior, haga clic en su cuenta. En la lista **Directorio** lista, elija el inquilino de Active Directory donde desea registrar la aplicación.
3. Haga clic en **Más servicios** en el panel de navegación izquierdo y seleccione **Azure Active Directory**.
4. Haga clic en **Registros de aplicaciones** y luego seleccione **Agregar**.
5. Siga las indicaciones para crear una nueva **aplicación cliente nativa**.
  * El **nombre** de la aplicación describe la aplicación a los usuarios finales.
  * El **URI de redireccionamiento** es una combinación de esquema y cadena que Azure AD usa para devolver respuestas de tokens.  Escriba un valor que sea específico de la aplicación y que se base en la información del anterior URI de redireccionamiento.
6. Después de haber completado el registro, Azure AD le asigna a la aplicación un identificador de aplicación único.  Necesitará este valor en las secciones siguientes, así que cópielo de la pestaña de la aplicación.
7. En la página **Configuración**, seleccione **Permisos necesarios** y **Agregar**. Seleccione **Microsoft Graph** como API y agregue el permiso **Leer datos de directorio** en **Permisos delegados**.  Esto configura la aplicación de modo que consulte la API Graph de Azure AD para los usuarios.

## <a name="3-install-and-configure-adal"></a>3. Instalación y configuración de ADAL
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad.  Para que ADAL se comunique con Azure AD, hay que proporcionarle información sobre el registro de la aplicación.

1. Comience agregando ADAL al proyecto de DirectorySearcher mediante CocoaPods.

    ```
    $ vi Podfile
    ```
2. Agregue lo siguiente a este podfile:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. Cargue el archivo podfile mediante CocoaPods. Mediante este paso se crea un área de trabajo de XCode que cargará.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. En el proyecto QuickStart, abra el archivo plist `settings.plist`.  Reemplace los valores de los elementos de la sección de modo que reflejen los especificados en Azure Portal. El código hace referencia a estos valores cada vez que usa ADAL.
  * `tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
  * `clientId` es el identificador de cliente de la aplicación que copió del portal.
  * `redirectUri` es la URL de redireccionamiento que ha registrado en el portal.

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.    Uso de ADAL para obtener tokens de Azure AD
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a un completionBlock `+(void) getToken : ` y ADAL se encarga del resto.  

1. En el proyecto `QuickStart`, abra `GraphAPICaller.m` y busque el comentario `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` en la parte superior.  Este es el lugar en el que se pasan a ADAL mediante CompletionBlock las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

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

2. Ahora tenemos que utilizar este token para buscar usuarios en el gráfico. Busque el comentario `// TODO: implement SearchUsersList`. Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado.  Para realizar una consulta a la API Graph de Azure AD, tiene que incluir un access_token en el encabezado `Authorization` de la solicitud. Aquí es donde entra en juego AAL.

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

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
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


3. Cuando la aplicación llama a `getToken(...)` para solicitar un token, ADAL intenta devolver uno sin pedir credenciales al usuario.  Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta.  Si ADAL no puede devolver un token por alguna razón, mostrará una `AdalException`.

> [!Note] 
> El objeto `AuthenticationResult` contiene un objeto `tokenCacheStoreItem` que puede usarse para recopilar información que puede necesitar la aplicación. En QuickStart, `tokenCacheStoreItem` se usa para determinar si ya se ha producido la autenticación.
>
>

## <a name="5-build-and-run-the-application"></a>5. Compilación y ejecución de la aplicación
Felicidades. Ahora tiene una aplicación de iOS operativa que puede autenticar usuarios, realizar llamadas seguras a las API web mediante OAuth 2.0 y obtener información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios.  Inicie la aplicación QuickStart e inicie sesión con uno de esos usuarios.  Busque otros usuarios según su UPN.  Cierre la aplicación y vuelva a abrirla.  Observe que la sesión del usuario permanece intacta.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación.  Hace el trabajo sucio por usted: administración en caché, compatibilidad con el protocolo OAuth, presentación al usuario de una interfaz de usuario de inicio de sesión y actualización de tokens expirados.  Todo lo que necesita saber es una única llamada de API, `getToken`.

Como referencia, en [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) puede ver el ejemplo finalizado (sin sus valores de configuración).  

## <a name="next-steps"></a>pasos siguientes
Ahora puede pasar a otros escenarios.  Es posible que desee probar:

* [Proteger una API web de Node.js con Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* Obtener información sobre [cómo habilitar el inicio de sesión único entre aplicaciones en iOS mediante ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

