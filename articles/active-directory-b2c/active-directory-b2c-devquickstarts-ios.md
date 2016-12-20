---
title: "Azure Active Directory B2C: llamada a una API web desde una aplicación iOS mediante bibliotecas de terceros| Microsoft Docs"
description: "Este artículo le mostrará cómo crear una aplicación iOS de &quot;lista de tareas pendientes&quot; que llama a una API web de Node.js con tokens de portador de OAuth 2.0 mediante una biblioteca de terceros."
services: active-directory-b2c
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: objectivec
ms.topic: hero-article
ms.date: 07/26/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 0175f4e83aace12d8e4607f2ad924893093c6734
ms.openlocfilehash: cc5e199816668a5a0f936019ab8096e93a7a2f5a


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C: Llamada a una API web desde una aplicación iOS mediante una biblioteca de terceros
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La plataforma Microsoft Identity utiliza estándares abiertos como OAuth2 y OpenID Connect. Esto permite a los desarrolladores aprovechar cualquier biblioteca que deseen integrar con nuestros servicios. Para ayudar a los desarrolladores en el uso de nuestra plataforma con otras bibliotecas, hemos escrito algunos tutoriales, como este, para demostrar cómo configurar bibliotecas de terceros para conectarse a la plataforma Microsoft Identity. La mayoría de las bibliotecas que implementan [la especificación OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) podrán conectarse a la plataforma Microsoft Identity.

Si no está familiarizado con OAuth2 o con OpenID Connect, es posible que gran parte de esta configuración de ejemplo no tenga mucho sentido para usted. Se recomienda que consulte una breve [información general sobre el protocolo que hemos documentado aquí](active-directory-b2c-reference-protocols.md).

> [!NOTE]
> Algunas características de nuestra plataforma que tienen una expresión en estos estándares, como el acceso condicional y la administración de directivas de Intune, deben usar nuestras bibliotecas de código abierto de Microsoft Azure Identity.
>
>

No todas las características y escenarios de Azure Active Directory son compatibles con la plataforma B2C.  Para determinar si debe utilizar la plataforma B2C, lea acerca de las [limitaciones de B2C](active-directory-b2c-limitations.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar.

## <a name="create-an-application"></a>Creación de una aplicación
A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Tanto la aplicación como la API web se representarán mediante un único **identificador de aplicación** en este caso, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir un **dispositivo móvil** en la aplicación.
* Copiar el **id. de aplicación** asignado a la aplicación. También lo necesitará más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Crear sus directivas
En Azure AD B2C, cada experiencia de usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene una experiencia de identidad: una combinación de inicio de sesión y registro. Es necesario crear una directiva así de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Al crear la directiva, tenga en cuenta lo siguiente:

* Elija los atributos de **nombre para mostrar** y de registro de la directiva.
* Elija las notificaciones de aplicación de **nombre para mostrar** e **id. de objeto** de cada directiva. Puede elegir también otras notificaciones.
* Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`.  Necesitará el nombre de la directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las directivas, está listo para compilar la aplicación.

## <a name="download-the-code"></a>Descargar el código
El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Para continuar, puede [descargar la aplicación como un archivo .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) o clonarla:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Otra alternativa es descargar el código completado y comenzar inmediatamente:

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Descarga de la biblioteca de terceros nxoauth2 e inicio de un área de trabajo
Para este tutorial, usaremos OAuth2Client desde GitHub, una biblioteca de OAuth2 para Mac OS X e iOS (Cocoa y Cocoa touch). Esta biblioteca se basa en el borrador 10 de la especificación OAuth2. Implementa el perfil de la aplicación nativa y admite el punto de conexión de autorización del usuario final. Esto es todo lo que vamos a necesitar para integrarlo con la plataforma de Microsoft Identity.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Adición de la biblioteca al proyecto mediante CocoaPods
CocoaPods es un administrador de dependencias para proyectos de Xcode. Administra automáticamente los pasos de instalación anteriores.

```
$ vi Podfile
```
Agregue lo siguiente a este podfile:

```
 platform :ios, '8.0'

 target 'SampleforB2C' do

 pod 'NXOAuth2Client'

 end
```

Ahora cargue el podfile mediante cocoapods. Esto creará una nueva área de trabajo de XCode que cargará.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>La estructura del proyecto
Hemos configurado la siguiente estructura para nuestro proyecto en el esquema:

* Una **Vista Patrón** con un panel de tareas.
* Una **Vista para agregar tareas** para los datos sobre la tarea seleccionada.
* Una **Vista de inicio de sesión** que permite al usuario iniciar sesión en la aplicación.

Pasaremos a los diversos archivos del proyecto para agregar autenticación. Otras partes del código, como el código visual, no son relevantes para la identidad y se incluyen también.

## <a name="create-the-settingsplist-file-for-your-application"></a>Creación del archivo `settings.plist` para la aplicación
Si tenemos una ubicación centralizada donde colocar los valores de configuración, la configuración resultará más sencilla. También ayuda entender lo que hace cada valor de configuración de la aplicación. Aprovecharemos la *lista de propiedades* como forma de proporcionar estos valores a la aplicación.

* Cree o abra el archivo `settings.plist` en `Supporting Files` en el área de trabajo de la aplicación.
* Escriba los siguientes valores (los revisaremos con más detalle en un momento).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Vamos a verlos con detalle.

Para `authURL`, `loginURL`, `bhh` y `tokenURL`, observará que debe rellenar su nombre de inquilino. Se trata del nombre del inquilino B2C que se le ha asignado. Por ejemplo, `kidventusb2c.onmicrosoft.com`. Si utiliza nuestras bibliotecas de identidad de código abierto de Microsoft Azure, estos datos los extraeríamos mediante nuestro punto de conexión de metadatos. Hemos hecho lo más difícil al extraer estos valores.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain` es el contenedor que la biblioteca NXOAuth2Client utilizará para crear una cadena de claves con el fin de almacenar los tokens. Si desea obtener el inicio de sesión único en varias aplicaciones, puede especificar la misma cadena de claves en cada una de las aplicaciones, así como solicitar el uso de esa cadena de claves en el código XCode. Esto se trata en la documentación de Apple.

El valor de `<policy name>` al final de cada dirección URL es el lugar donde se colocará la directiva creada anteriormente. La aplicación llama a estas directivas en función del flujo.

El valor de `taskAPI` es el punto de conexión de REST que llamaremos con el token B2C para agregar tareas o consultar las tareas existentes. Lo hemos configurado específicamente para este ejemplo. No es necesario modificarlo para que el ejemplo funcione.

El resto de estos valores se necesitan para usar la biblioteca y crear sitios para llevar los valores en el contexto.

Ahora que hemos creado el archivo `settings.plist` , necesitamos código para leerlo.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Configuración de una clase AppData para leer nuestra configuración
Vamos a crear un archivo sencillo que analice nuestro archivo `settngs.plist` creado anteriormente y permita que esa configuración se encuentre disponible en el futuro para cualquier clase. Dado que no quiere crear una nueva copia de los datos cada vez que una clase los pida, usaremos un patrón Singleton y cada vez que se realice una solicitud de la configuración se devolverá la misma instancia creada.

* Cree un archivo `AppData.h` :

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Cree un archivo `AppData.m` :

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Ahora podemos obtener fácilmente nuestros datos con solo llamar a `  AppData *data = [AppData getInstance];` en cualquiera de nuestras clases, como verá a continuación.

## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Configuración de la biblioteca NXOAuth2Client en AppDelegate
La biblioteca de NXOAuthClient requiere algunos valores para configurar la aplicación. Una vez completada, puede usar el token obtenido para llamar a la API de REST. Como sabemos que se llamará a `AppDelegate` cada vez que se cargue la aplicación, tiene sentido colocar nuestros valores de configuración en ese archivo.

* Abra el archivo `AppDelegate.m` .
* Importe algunos archivos de encabezado, los usaremos más adelante.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Agregue el método `setupOAuth2AccountStore` en AppDelegate.

Necesitamos crear un objeto AccountStore y luego suministrarle los datos que acabamos de leer del archivo `settings.plist` .

En este punto, hay algunas cosas que debemos tener en cuenta respecto al servicio B2C que harán que este código sea más comprensible:

1. Azure AD B2C utiliza la *directiva* proporcionada por los parámetros de consulta para atender la solicitud. De esta forma, Azure Active Directory puede actuar como un servicio independiente para su aplicación. Para suministrar estos parámetros de consulta adicionales, debemos proporcionar al método `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` nuestros parámetros de directiva personalizados.
2. Azure AD B2C utiliza ámbitos de forma muy parecida a otros servidores OAuth2. Sin embargo, como el uso de B2C va encaminado tanto a la autenticación de un usuario como al acceso a los recursos, algunos ámbitos son absolutamente necesarios para que el flujo funcione correctamente. Este es el caso del ámbito `openid` . Nuestros SDK de Microsoft Identity proporcionan el ámbito `openid` automáticamente así que no lo verá en nuestra configuración del SDK. Sin embargo, puesto que estamos usando una biblioteca de terceros, debemos especificar este ámbito.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
A continuación, asegúrese de llamarlo en AppDelegate en el método `didFinishLaunchingWithOptions:` .

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Creación de una clase `LoginViewController` que usaremos para administrar las solicitudes de autenticación
Usaremos una vista web para el inicio de sesión de la cuenta. Esto nos permite solicitar al usuario factores adicionales, como mensaje de texto SMS (si están configurados) o devolver mensajes de error al usuario. Aquí también estableceremos la vista web y, después, escribiremos el código para controlar las devoluciones de llamada que se realizarán en la vista web desde el servicio de Microsoft Identity.

* Cree una clase `LoginViewController.h` .

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

A continuación, crearemos cada uno de estos métodos.

> [!NOTE]
> Asegúrese de enlazar `loginView` a la vista web actual que está dentro de su guión gráfico. Si no, no tendrá una vista web que aparezca cuando sea el momento de autenticarse.
>
>

* Cree una clase `LoginViewController.m` .
* Agregue algunas variables para que incluyan el estado a medida que nos autenticamos.

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE;
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Invalidación de los métodos de la vista web para controlar la autenticación

Es necesario indicar a la vista web el comportamiento que queremos cuando un usuario necesita iniciar sesión, tal como se ha explicado anteriormente. Solo hay que cortar y pegar el código siguiente.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Escriba el código para controlar el resultado de la solicitud de OAuth2.

Necesitaremos el código que controlará la URL de redirección que proviene de la vista web. Si no se realiza correctamente, volveremos a intentarlo. Mientras tanto, la biblioteca proporcionará el error que se puede ver en la consola o lo controlará de forma asincrónica.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configure las fábricas de notificaciones.

Crearemos el mismo método que en la clase `AppDelegate` anteriormente, pero esta vez agregaremos algunos objetos `NSNotification` para que nos digan lo que sucede en nuestro servicio. Configuramos un observador que nos dice los cambios que se produzcan con el token. Una vez obtenido el token, devolvemos al usuario a `masterView`.

```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Agregue código que administre al usuario cada vez que se inicie una solicitud de inicio de sesión nativo.

Vamos a crear un método que se llamará cada vez que tengamos una solicitud de autenticación. Este será el método que realmente cree una vista web.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Por último, vamos a llamar a todos estos métodos que hemos escrito anteriormente cada vez que se cargue `LoginViewController` . Para ello, agregaremos estos métodos a nuestro método `viewDidLoad` que nos da Apple.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Ahora ha terminado de crear la forma principal en que interactuamos con nuestra aplicación para iniciar sesión. Una vez que hemos iniciado sesión, debemos usar los tokens que hemos recibido. Para ello, crearemos código auxiliar que llamará a las API de REST automáticamente mediante esta biblioteca.

## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Creación de una clase `GraphAPICaller` para administrar nuestras solicitudes a una API de REST
Tenemos cargada una configuración cada vez que cargamos nuestra aplicación. Ahora debemos hacer algo con ella una vez que tengamos un token.

* Cree un archivo `GraphAPICaller.h`.

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Según este código puede ver que vamos a crear dos métodos: uno para obtener las tareas de una API y otro para agregar tareas a la API.

Ahora que hemos configurado nuestra interfaz, vamos a agregar la implementación real:

* Creación de una clase `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

//
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

//
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Ejecutar la aplicación de ejemplo
Por último, cree y ejecute la aplicación en xCode. Regístrese o inicie sesión en la aplicación y cree las tareas de un usuario que haya iniciado sesión. Cierre la sesión y vuelva a iniciarla como otro usuario y cree las tareas de dicho usuario.

Observe que las tareas se almacenan por usuario en la API, ya que la API extrae la identidad del usuario del token de acceso que recibe.

## <a name="next-steps"></a>Pasos siguientes
Ahora puede pasar a temas más avanzados de B2C. Puede probar:

[Llamada a una API web de Node.js desde una aplicación web de Node.js (Llamada a una API web de Node.js desde una aplicación web de Node.js)]()

[Personalización de la experiencia de usuario en una aplicación B2C]()



<!--HONumber=Dec16_HO1-->


