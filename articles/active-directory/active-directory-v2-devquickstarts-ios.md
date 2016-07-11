<properties
	pageTitle="Aplicación iOS de Azure AD v2.0 | Microsoft Azure"
	description="Cómo crear una aplicación iOS con la que los usuarios pueden iniciar sesión utilizando tanto la cuenta personal de Microsoft como sus cuentas profesionales o educativas mediante bibliotecas de terceros."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="brandwe"/>

# Adición de inicio de sesión a una aplicación iOS mediante una biblioteca de terceros con la API Graph con el punto de conexión v2.0

La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Esto permite a los desarrolladores aprovechar cualquier biblioteca que deseen integrar con nuestros servicios. Para ayudar a los desarrolladores en el uso de nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales, como este, para demostrar cómo configurar bibliotecas de terceros para conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) podrán conectarse a la plataforma Microsoft Identity.

Esta aplicación permitirá al usuario iniciar sesión en su organización y, después, buscar a otros en la organización mediante la API Graph.

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no tenga mucho sentido para usted. Se recomienda que consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
    Algunas características de nuestra plataforma que tienen una expresión en estos estándares, como el acceso condicional y la administración de directivas de Intune, deben usar nuestras bibliotecas de código abierto de Microsoft Azure Identity.

> [AZURE.NOTE] 
    No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe utilizar el punto de conexión v2.0, lea acerca de las [limitaciones de v2.0](active-directory-v2-limitations.md).

## Descargar
El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

O simplemente descárguelo y empiece a trabajar de inmediato:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## Registrar una aplicación
Crea una nueva aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com) o siga estos [pasos detallados](active-directory-v2-app-registration.md). Asegúrese de que:

- Anotar el **Id. de aplicación** asignado a su aplicación; lo necesitará pronto.
- Agregar la plataforma **Móvil** a la aplicación.
- Copiar el **URI de redirección** desde el portal. Debe usar el valor predeterminado de `urn:ietf:wg:oauth:2.0:oob`.


## Descarga de la biblioteca de terceros nxoauth2 e inicio de un área de trabajo

Para este tutorial, usaremos OAuth2Client desde GitHub, una biblioteca de OAuth2 para Mac OS X e iOS (Cocoa y Cocoa touch). Esta biblioteca se basa en el borrador 10 de la especificación OAuth2. Implementa el perfil de la aplicación nativa y admite el punto de conexión de autorización del usuario final. Esto es todo lo que vamos a necesitar para integrarlo con la plataforma de Microsoft Identity.

### Adición de la biblioteca al proyecto mediante CocoaPods

CocoaPods es un administrador de dependencias para proyectos de Xcode. Administra automáticamente los pasos de instalación anteriores.

```
$ vi Podfile
```
Agregue lo siguiente a este podfile:

```
 platform :ios, '8.0'
 
 target 'QuickStart' do
 
 pod 'NXOAuth2Client'
 
 end
```

Ahora cargue el podfile mediante cocoapods. Esto creará una nueva área de trabajo de XCode que cargará.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

## La estructura del proyecto

Hemos configurado la siguiente estructura para nuestro proyecto en el esquema:

* Una vista principal con una búsqueda UPN
* Una vista de detalle para los datos sobre el usuario seleccionado
* Una vista de inicio de sesión que permite al usuario iniciar sesión en la aplicación para consultar el gráfico.

Pasaremos por varios archivos del esquema para agregar la autenticación. Otras partes del código, como el código visual, no son relevantes para la identidad y se incluyen también.

## Configuración del archivo settings.plst en la biblioteca

-	En el proyecto QuickStart, abra el archivo plist `settings.plist`. Reemplace los valores de los elementos de la sección para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores cada vez que use ADAL.
    -	`clientId` es el identificador de cliente de la aplicación que copió del portal.
    -	El valor `redirectUri` es la URL de redirección que ha proporcionado el portal.

## Configuración de la biblioteca de NXOAuth2Client en LoginViewController

La biblioteca de NXOAuthClient requiere algunos valores para configurar la aplicación. Una vez completada, puede usar el token obtenido para llamar a la API Graph. Como sabemos que se llamará a `LoginView` cada vez que se necesite autenticación, tiene sentido que coloquemos nuestros valores de configuración en ese archivo.
* Abra el archivo `LoginViewController.m`.

* Vamos a agregar algunos valores en el código que establecerán el contexto para la autenticación y autorización. Se explicarán de forma detallada más adelante.

```objc
NSString *scopes = @"offline_access User.ReadBasic.All";
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

Vamos a verlos con detalle.

La primera cadena que se establece es para `scopes`. El ámbito que vamos a solicitar para esta aplicación es `User.ReadBasic.All`, que nos permite leer el perfil básico de todos los usuarios de nuestro directorio. Puede obtener más información sobre todos los ámbitos que están disponibles para [su uso con Microsoft Graph aquí](https://graph.microsoft.io/docs/authorization/permission_scopes).

Para `authURL`, `loginURL`, `bhh`, `tokenURL` debe utilizar los valores indicados arriba. Si usa nuestras bibliotecas de código abierto de Microsoft Azure Identity, se podrían desplegar estos datos para que se utilice nuestro punto de conexión de metadatos. Hemos hecho lo más difícil al extraer estos valores.

El valor `keychain` es el contenedor que la biblioteca NXOAuth2Client utilizará para crear una cadena de claves para almacenar los tokens. Si desea obtener el inicio de sesión único en varias aplicaciones, puede especificar la misma cadena de claves en cada una de las aplicaciones, así como solicitar el uso de esa cadena de claves en el código XCode. Esto se trata en la documentación de Apple.

El resto de estos valores se necesitan para usar la biblioteca y crear sitios para llevar los valores en el contexto.

* Creación de una caché de URL

Dentro de `(void)viewDidLoad()`, que siempre se llama cuando se carga la vista, hemos preparado una caché para nuestro uso.

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

* Cree una vista web que utilizará para el inicio de sesión.

Usaremos una vista web para el inicio de sesión de la cuenta. Esto nos permite solicitar al usuario factores adicionales, como mensaje de texto SMS (si están configurados) o devolver mensajes de error al usuario. Aquí también estableceremos la vista web y, después, escribiremos el código para controlar las devoluciones de llamada que se realizarán en la vista web desde el servicio de Microsoft Identity.

```objc
-(void)requestOAuth2Access {
    //in order to login to Mircosoft APIs using OAuth2 we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client
                                       
                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

* Invalidación de los métodos de la vista web para controlar la autenticación

Es necesario indicar a la vista web el comportamiento que queremos cuando un usuario necesita iniciar sesión, tal como se ha explicado anteriormente. Solo hay que cortar y pegar el código siguiente.

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

* Escriba el código para controlar el resultado de la solicitud de OAuth2.

Necesitaremos el código que controlará la URL de redirección que proviene de la vista web. Si no se realiza correctamente, volveremos a intentarlo. Mientras tanto, la biblioteca proporcionará el error que se puede ver en la consola o lo controlará de forma asincrónica.

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

* Configuración del contexto de OAuth (denominado almacén de cuentas)

Aquí se puede llamar a `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` en el almacén de cuentas compartidas para cada servicio al que quiere acceder desde su aplicación. El tipo de cuenta es una cadena que se utiliza como identificador para un servicio determinado. Como estamos accediendo a la API Graph, seguimos adelante y la llamamos `"myGraphService"`. Después, configuramos un observador que nos indicará cuándo cambia algo con el token. Una vez obtenido el token, devolvemos al usuario a la `masterView`.



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

## Configuración de la vista principal para buscar y mostrar los usuarios de la API Graph

Una aplicación MVC que muestra los datos devueltos en la cuadrícula está fuera del ámbito de este tutorial y hay varios tutoriales en línea que explican cómo crear una. Proporcionamos todo el código en el archivo de esquema. Sin embargo, debemos tratar algunos aspectos en esta aplicación MVC:

* Interceptar cuando el usuario escribe algo en el campo de búsqueda
* Proporcionar un objeto de datos a la vista principal para que pueda mostrar los resultados en la cuadrícula

Lo haremos todo a continuación.

* Agregar una comprobación para ver si hemos iniciado sesión

La aplicación no sirve de mucho si el usuario no ha iniciado sesión, por lo que se recomienda comprobar si ya hay un token en la memoria caché. De lo contrario, volveremos a la vista de inicio de sesión para el usuario inicie sesión. Como recordará, la mejor manera de realizar acciones cuando se carga una vista es usar el método `viewDidLoad()` que Apple proporciona.

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

* Actualizar la vista de tabla cuando se reciben datos

Cuando obtenemos datos de la API Graph, hay que mostrar los datos que se devuelven. Para simplificar, este es todo el código que necesita para actualizar la tabla. Solo tiene que pegar los valores correctos en el código reutilizable de MVC.

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

* Proporcionar una manera de llamar a la API Graph cuando alguien escribe en el campo de búsqueda

Cuando alguien escribe una búsqueda en el cuadro, hay que llevarla a la API Graph. Para separar mejor la funcionalidad de búsqueda de la presentación, vamos a crear a continuación otra clase denominada `GraphAPICaller`. De momento, sigamos adelante y vamos a escribir el código para permitir la alimentación de los caracteres de búsqueda en la API Graph. Para ello, se proporciona el método denominado `lookupInGraph` que toma la cadena que queremos buscar.

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

## Escritura de una clase auxiliar para acceder a la API Graph

Es el núcleo de nuestra aplicación. Mientras que el resto consistía en insertar código en el patrón MVC predeterminado de Apple, aquí escribimos código para consultar el gráfico cuando el usuario escribe y devolver los datos. Mostraremos el código y después se explicará con detalle.

* Cree un nuevo archivo de encabezado Objective C denominado `GraphAPICaller.h` con el código siguiente:

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Se puede ver que estamos especificando un método que toma una cadena y devuelve un elemento completionBlock. Este elemento completionBlock, como se habrá imaginado, actualizará la tabla al proporcionar un objeto con datos rellenados en tiempo real cuando el usuario realiza las búsquedas.


* Cree un nuevo archivo Objective C denominado `GraphAPICaller.m` y agregue el método siguiente:

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

Veamos este método con detalle

El núcleo de este código se encuentra en el método `NXOAuth2Request`, que toma los parámetros que hemos definido dentro del archivo settings.plist al principio. El primer paso es construir la llamada correcta a la API Graph. Como vamos a llamar a `/users`, lo especificamos mediante la anexión a nuestro recurso de API Graph junto con la versión. Se recomienda ponerlos en un archivo de configuración externo, ya que pueden cambiar cuando evoluciona la API.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Después, necesitamos especificar los parámetros, que también proporcionaremos a la llamada de API Graph. Es *muy importante* que no coloque los parámetros en el punto de conexión de los recursos ya que se eliminan todos los caracteres del identificador URI no compatibles en tiempo de ejecución. Todo el código de consulta debe indicarse en los parámetros.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Observará que se llama a un método `convertParamsToDictionary` que todavía no se ha escrito. Lo haremos ahora al final del archivo:

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

Por último, veamos cómo se devuelven los datos al método MasterViewController. Los datos vuelven a nosotros como serializados y tienen que deserializarse y cargarse en un objeto que puede consumir MainViewController. Por este motivo, el esquema tiene un archivo `User.m/h` que crea un objeto de usuario. Rellenamos ese objeto de usuario con la información del gráfico.

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


## Ejecución del ejemplo

Si ha utilizado el esquema o ha seguido todo el tutorial, la aplicación debería ejecutarse ahora. Inicie el simulador y haga clic en "Iniciar sesión" para usar la aplicación.

## Obtención de actualizaciones de seguridad para nuestro producto

Le animamos a que obtenga notificaciones de los incidentes de seguridad que se produzcan; para ello, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscríbase a las alertas de avisos de seguridad.

<!---HONumber=AcomDC_0629_2016-->