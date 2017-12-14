---
title: "Incorporación del inicio de sesión a una aplicación iOS mediante el punto de conexión de Azure AD v2.0 | Microsoft Docs"
description: "Procedimiento para compilar una aplicación iOS con la que los usuarios pueden iniciar sesión utilizando su cuenta personal de Microsoft como sus cuentas profesionales o educativas mediante bibliotecas de terceros"
services: active-directory
documentationcenter: 
author: brandwe
manager: mtillman
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 398ddbd004b4a12f4aa79ed64cc85f0e5bc5407a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Adición de inicio de sesión a una aplicación iOS mediante una biblioteca de terceros con la API Graph mediante la versión 2.0 del punto de conexión
La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Los desarrolladores pueden usar la biblioteca que quieran integrar con nuestros servicios. Para ayudar a los desarrolladores a utilizar nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales como este para demostrar cómo configurar bibliotecas de terceros con el objetivo de conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) pueden conectarse a la plataforma Microsoft Identity.

Con la aplicación que se crea en este tutorial, los usuarios podrán iniciar sesión en su organización y, después, buscar a otros de la misma organización mediante la API Graph.

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no le sea relevante. Si este es el caso, le recomendamos que lea [Protocolos de la versión 2.0: Flujo de código de autorización de OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

> [!NOTE]
> Algunas características de nuestra plataforma mencionadas en los estándares OAuth2 u OpenID Connect, como el acceso condicional y la administración de directivas de Intune, deben usar nuestras bibliotecas de código abierto de Microsoft Azure Identity.
> 
> 

No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión.

> [!NOTE]
> Para determinar si debe utilizar la versión 2.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Descarga del código desde GitHub
El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

También puede descargar el ejemplo y comenzar a trabajar inmediatamente:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrar una aplicación
Cree una aplicación en el [Portal de registro de aplicaciones](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o siga los pasos que se detallan en [Cómo registrar una aplicación con el punto de conexión v2.0](active-directory-v2-app-registration.md).  Asegúrese de que:

* Copie el **id. de aplicación** asignado a su aplicación, ya que lo necesitará pronto.
* Agregar la plataforma **Móvil** a la aplicación.
* Copie el **URI de redireccionamiento** del portal. Debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Descarga de la biblioteca de terceros NXOAuth2 y creación de un área de trabajo
Para este tutorial, usaremos OAuth2Client desde GitHub, que es una biblioteca de OAuth2 para Mac OS X e iOS (Cocoa y Cocoa touch). Esta biblioteca se basa en el borrador 10 de la especificación OAuth2. Implementa el perfil de la aplicación nativa y admite el punto de conexión de autorización del usuario. Esto es todo lo que vamos a necesitar para integrarlo con la plataforma de Microsoft Identity.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Adición de la biblioteca al proyecto mediante CocoaPods
CocoaPods es un administrador de dependencias para proyectos de Xcode. Administra automáticamente los pasos de instalación anteriores.

```
$ vi Podfile
```
1. Agregue lo siguiente a este podfile:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Cargue el archivo podfile mediante CocoaPods. Así se creará una nueva área de trabajo de XCode que cargará.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Exploración de la estructura del proyecto
Hemos configurado la siguiente estructura para nuestro proyecto en el esquema:

* Una vista principal con una búsqueda UPN
* Una vista de detalle para los datos sobre el usuario seleccionado
* Una vista de inicio de sesión que permite al usuario iniciar sesión en la aplicación para consultar el gráfico

Pasaremos por varios archivos del esquema para agregar la autenticación. Aunque otras partes del código, como el código visual, no son relevantes para la identidad, se incluyen también.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Configuración del archivo settings.plst en la biblioteca
* En el proyecto QuickStart, abra el archivo `settings.plist` . Reemplace los valores de los elementos de la sección para que reflejen los usados en el Portal de Azure. El código hará referencia a estos valores cada vez que use la biblioteca de autenticación de Active Directory.
  * `clientId` es el identificador de cliente de la aplicación que copió del portal.
  * `redirectUri` es la URL de redirección que ha proporcionado el portal.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Configuración de la biblioteca de NXOAuth2Client en LoginViewController
La biblioteca NXOAuth2Client requiere que se configuren algunos valores. Después de completar esa tarea, puede usar el token obtenido para llamar a la API Graph. Como se llamará a `LoginView` cada vez que tengamos que autenticarnos, procede insertar los valores de configuración en ese archivo.

* Vamos a agregar algunos valores al archivo `LoginViewController.m` para establecer el contexto de la autenticación y autorización. Después del código, encontrará los detalles de los valores.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Analicemos los detalles del código.

La primera cadena es para `scopes`.  El valor `User.Read` permite leer el perfil básico del usuario que ha iniciado sesión.

Puede obtener más información sobre todos los ámbitos disponibles en [Microsoft Graph permission scopes](https://graph.microsoft.io/docs/authorization/permission_scopes)(Ámbitos de los permisos de Microsoft Graph).

Para `authURL`, `loginURL`, `bhh` y `tokenURL`, debe utilizar los valores indicados anteriormente. Si usa las bibliotecas de código abierto de Microsoft Azure Identity, podemos obtener estos datos para que se utilice nuestro punto de conexión de metadatos. Hemos hecho lo más difícil al extraer estos valores.

`keychain` es el contenedor que la biblioteca NXOAuth2Client utilizará para crear una cadena de claves con el fin de almacenar los tokens. Si quiere obtener el inicio de sesión único (SSO) en varias aplicaciones, puede especificar la misma cadena de claves en cada una de las aplicaciones, así como solicitar el uso de esa cadena de claves en los derechos de XCode. Esto se explica en la documentación de Apple.

El resto de estos valores se necesitan para usar la biblioteca y crear sitios para agregar los valores al contexto.

### <a name="create-a-url-cache"></a>Creación de una caché de URL
Dentro de `(void)viewDidLoad()`, que siempre se invoca después de cargar la vista, el código siguiente prepara una memoria caché para que podamos utilizarlo.

Agregue el siguiente código:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Creación de una vista web para iniciar sesión
Gracias a las vistas web, podemos solicitar al usuario factores adicionales, como mensajes de texto SMS (si están configurados) o devolver mensajes de error al usuario. Aquí también configuraremos la vista web y, después, escribiremos el código para controlar las devoluciones de llamada que se realizarán en la vista web de los servicios de identidad.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Invalidación de los métodos de la vista web para controlar la autenticación
Tal y como hemos explicado anteriormente, para indicar a la vista web lo que sucederá cuando un usuario tenga que iniciar sesión, puede pegar el código siguiente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Escriba el código para controlar el resultado de la solicitud de OAuth2.
El siguiente código controlará la URL de redireccionamiento que se devuelve desde la vista web. Si la autenticación no se realizó correctamente, el código volverá a tratar de realizar la operación. Mientras tanto, la biblioteca proporcionará el error que se puede ver en la consola, o bien lo controlará de forma asincrónica.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Configuración del contexto de OAuth (denominado "almacén de cuentas")
Aquí se puede llamar a `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` en el almacén de cuentas compartidas para cada servicio al que quiere que acceda la aplicación. El tipo de cuenta es una cadena que se utiliza como identificador para un servicio determinado. Como va a acceder a la API Graph, el código hará referencia a ella como `"myGraphService"`. Después, configuramos un observador que nos indicará cuándo cambia algo con el token. Una vez obtenido el token, devolvemos al usuario a la `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Configuración de la vista principal para buscar y mostrar los usuarios de la API Graph
Las aplicaciones de control de vista principal (MVC) que muestran los datos devueltos en la cuadrícula no se tratan en este tutorial. Hay varios en línea que explican cómo crear una. Todo este código se encuentra en el archivo de esquema. Sin embargo, debemos tratar algunos aspectos en esta aplicación MVC:

* Interceptar cuando el usuario escribe algo en el campo de búsqueda
* Proporcionar un objeto de datos a la vista principal para que pueda mostrar los resultados en la cuadrícula

Lo haremos todo a continuación.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Adición de una comprobación para ver si hemos iniciado sesión
La aplicación no sirve de mucho si el usuario no ha iniciado sesión, por lo que se recomienda comprobar si ya hay un token en la memoria caché. De lo contrario, volveremos a la vista de inicio de sesión para que el usuario inicie sesión. Como recordará, la mejor manera de realizar acciones cuando se carga una vista es usar el método `viewDidLoad()` que proporciona Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Actualizar la vista de tabla cuando se reciben datos
Cuando la API Graph devuelve los datos, debe mostrarlos. Para simplificar, este es todo el código que necesita si quiere actualizar la tabla. Solo tiene que pegar los valores correctos en el código reutilizable de MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Proporcionar una manera de llamar a la API Graph cuando alguien escribe en el campo de búsqueda
Cuando un usuario escribe una búsqueda en el cuadro, hay que llevarla a la API Graph. La clase `GraphAPICaller` , que creará en el código siguiente, separa la funcionalidad de búsqueda de la de presentación. Por ahora, vamos a escribir el código que transmite los caracteres de búsqueda a la API Graph. Para ello, se proporciona el método denominado " `lookupInGraph`", que toma la cadena que queremos buscar.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Escritura de una clase auxiliar para acceder a la API Graph
Se trata del núcleo de nuestra aplicación. Mientras que el resto consistía en insertar código en el patrón MVC predeterminado de Apple, aquí escribimos código para consultar el gráfico cuando el usuario escribe y devolver los datos. Aquí se muestra el código y, abajo de este, proporcionamos una explicación detallada.

### <a name="create-a-new-objective-c-header-file"></a>Creación de un nuevo archivo de encabezado Objective C
Denomine al archivo `GraphAPICaller.h`y agregue el código siguiente.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Se puede ver que estamos especificando un método que toma una cadena y devuelve un elemento completionBlock. Este elemento completionBlock, como se habrá imaginado, actualizará la tabla proporcionando un objeto con datos rellenados en tiempo real cuando el usuario realiza las búsquedas.

### <a name="create-a-new-objective-c-file"></a>Creación de un nuevo archivo Objective C
Denomine al archivo `GraphAPICaller.m`y agregue el método siguiente.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


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

```

Analicemos pormenorizadamente este método.

El núcleo de este código se encuentra en el método `NXOAuth2Request`, que toma los parámetros que ya hemos definido dentro del archivo settings.plist.

El primer paso consiste en crear la llamada correcta a la API Graph. Como vamos a llamar a `/users`, lo especificamos anexándolo a nuestro recurso de API Graph junto con la versión. Se recomienda colocarlos en un archivo de configuración externo, ya que pueden cambiar cuando evolucione la API.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Después, tenemos que especificar los parámetros, que también proporcionaremos a la llamada de API Graph. Es *muy importante* que no coloque los parámetros en el punto de conexión de los recursos, ya que se eliminan todos los caracteres del identificador URI no compatibles en el entorno de tiempo de ejecución. Todo el código de consulta debe indicarse en los parámetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Es posible que observe que se llama a un método `convertParamsToDictionary` que todavía no se ha escrito. Lo haremos ahora al final del archivo:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
A continuación, vamos a usar el método `NXOAuth2Request` para obtener los datos de la API en formato JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Finalmente, veamos cómo se devuelven los datos al método MasterViewController. Los datos los obtenemos serializados; tenemos que deserializarlos y cargarlos en un objeto que pueda utilizar MainViewController. Por este motivo, el esquema tiene un archivo `User.m/h` que crea un objeto de usuario. Rellenamos ese objeto de usuario con la información del gráfico.

```objc
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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Ejecución del ejemplo
Si ha utilizado el esquema o ha seguido todo el tutorial, la aplicación debería ejecutarse ahora. Inicie el simulador y haga clic en **Iniciar sesión** para usar la aplicación.

## <a name="get-security-updates-for-our-product"></a>Obtención de actualizaciones de seguridad para nuestro producto
Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite la página [TechCenter de seguridad](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de documentos informativos de seguridad.

