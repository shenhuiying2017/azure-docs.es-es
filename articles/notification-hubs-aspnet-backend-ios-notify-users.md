<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in Objective-C using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"/>

# Notificación a los usuarios de Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/es-es/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
        <a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un back-end de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal y como se muestra en el tema de referencia [Registro desde el backend de la aplicación][]. Este tutorial se basa en el centro de notificaciones que creó en el tutorial **Introducción a los Centros de notificaciones**.

Este tutorial también es el requisito previo para el tutorial **Inserción segura**. Una vez completados los pasos del tutorial **Notificar a los usuarios**, puede continuar con el tutorial **Inserción segura**, en el que se muestra cómo modificar el código de **Notificar a los usuarios** para enviar una notificación de inserción de forma segura.

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal y como se describe en [Introducción a los Centros de notificaciones (iOS)][].

## Creación y configuración del centro de notificaciones

Antes de empezar este tutorial, debe crear un perfil de aprovisionamiento iOS y un certificado de inserción de desarrollo y, después, crear un Centro de notificaciones de Azure y conectarlo a esa aplicación. Siga los pasos de [Introducción a los Centros de notificaciones (iOS)][], especialmente las secciones 1 a 5.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][]]

## Modificación de la aplicación iOS

1.  Siga los pasos de [Introducción a los Centros de notificaciones (iOS)][], secciones 1 a 5, para crear una aplicación de vista de una sola página capaz de recibir notificaciones de inserción desde un centro de notificaciones.

> [AZURE.NOTE] En esta sección asumimos que configuró el proyecto con un nombre de organización vacío. Si no es el caso, tiene que anteponer el nombre de organización a todos los nombres de clase en el código siguiente.

1.  En Main.storyboard, agregue los siguientes componentes desde la biblioteca de objetos:

    -   UITextField con el texto de marcador de posición **Username**
    -   UITextField con el texto de marcador de posición **Password** y active la opción **Secure Text Entry** en el grupo de propiedades TextField del panel derecho.
    -   UIButton con la etiqueta **1. Log in** y desactive la casilla **Enabled** en el grupo de propiedades Accessibility del panel derecho.
    -   UIButton con la etiqueta **2. Send Push** y desactive la casilla **Enabled** en el grupo de propiedades Accessibility del panel derecho.

    El guión gráfico debe tener el aspecto siguiente:

    ![](./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png)

2.  Cree salidas para UITextField y UIButton en la parte de la interfaz de su ViewController.m.

        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

3.  Cree acciones para ambos botones en la implementación de ViewController.m:
     - (IBAction)LogInAction:(id)sender { }
     - (IBAction)SendPushAction:(id)sender { }

4.  Primero crearemos una clase RegisterClient a modo de interfaz con nuestro back-end. Cree una clase Objective-C llamada RegisterClient heredada de NSObject. Después agregue el siguiente código en la sección de interfaz RegisterClient.h:

        @property (strong, nonatomic) NSString* authenticationHeader;
        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5.  En RegisterClient.m, agregue la siguiente sección de interfaz:

        @interface RegisterClient ()

        @property (strong, nonatomic) NSURLSession* session;
        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

        @end

6.  Después agregue el siguiente código en la sección de implementación y sustituya el marcador de posición *{backend endpoint}* por el extremo usado para implementar el back-end de la aplicación en la sección anterior.

        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";
        NSString *const BackEndEndpoint = @"{backend endpoint}/api/register";

        - (instancetype)init
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description] stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""] uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long) httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSData*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token, @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/%@", BackEndEndpoint, registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults] objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@?handle=%@", BackEndEndpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange: NSMakeRange(1, [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    El código anterior implementa la lógica explicada en el artículo de referencia [Registro desde el backend de la aplicación][] usando NSURLSession para realizar llamadas REST al back-end de la aplicación y NSUserDefaults para almacenar localmente del identificador de registro devuelto por el centro de notificaciones.

    Tenga en cuenta que esta clase requiere que su propiedad **authorizationHeader** esté establecida para que funcione correctamente. Esta propiedad la establece la clase **ViewController** después del inicio de sesión.

7.  En ViewController.h, agregue la propiedad siguiente que contendrá el token del dispositivo:

        @property (strong, nonatomic) NSData* deviceToken;

8.  En ViewController.m, convierta la clase ViewController en UITextFieldDelegate, agregue una propiedad para hacer referencia a la instancia RegisterClient y agregue una declaración de método privado. La sección de la interfaz debe ser:

        @interface ViewController () <UITextFieldDelegate>
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

        @property (strong, nonatomic) RegisterClient* registerClient;

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        @end

    El método privado se usará para crear el encabezado de autorización para realizar la autenticación básica con el back-end de la aplicación.

> [AZURE.NOTE] Este no es un esquema de autenticación seguro; debe sustituir la implementación de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** por el mecanismo de autenticación específico que genera un token de autenticación para ser consumido por la clase de cliente del Registro, por ejemplo OAuth, Active Directory.

1.  Después, en la sección de implementación de ViewController.m, agregue el siguiente código:

        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }

        -(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData encoding:NSUTF8StringEncoding];
        }

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }

    Observe cómo al establecer el token del dispositivo se habilita el botón de inicio de sesión. Esto se debe a que, como una parte de la acción de inicio de sesión, el controlador de vista registrará notificaciones de inserción con el back-end de la aplicación. Por consiguiente, queremos evitar que el usuario presione el botón de inicio de sesión antes de que el token del dispositivo se haya configurado correctamente. En la aplicación, podría estar interesado en desacoplar el inicio de sesión del registro de pulsación siempre que aquel ocurra antes que este.

2.  En ViewController.m, agregue una constante para el extremo back-end y rellene las implementaciones de los métodos de acción para sus UIButton. Recuerde reemplazar el marcador de posición por el extremo back-end usado en la sección anterior.

        - (IBAction)LogInAction:(id)sender {
            // create authentication header and set it in register client
            NSString* username = self.UsernameField.text;
            NSString* password = self.PasswordField.text;

            [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

            __weak ViewController* selfie = self;
            [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil andCompletion: ^(NSError* error) {
                if (!error) {
                    dispatch_async(dispatch_get_main_queue(), ^{
                        selfie.SendPushButton.enabled = YES;

                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Success" message:
                                              @"Registered successfully!" delegate:nil cancelButtonTitle:
                                              @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    });
                }
            }];
        }

        NSString *const SendNotificationEndpoint = @"{backend endpoint}/api/notifications";

        - (IBAction)SendPushAction:(id)sender {
            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:SendNotificationEndpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"Error status: %d, request: %@", httpResponse.statusCode, error);
                }
            }];
            [dataTask resume];
        }

3.  Finalmente, en **ViewDidLoad**, agregue el siguiente código para crear una instancia de RegisterClient y establezca el delegado para sus campos de texto.

        self.UsernameField.delegate = self;
        self.PasswordField.delegate = self;
        self.registerClient = [[RegisterClient alloc] init];

4.  Ahora, en **AppDelegate.m**, quite todo el contenido del método **application:didRegisterForPushNotificationWithDeviceToken:** y reemplácelo por:

        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;

    Esto garantiza que el controlador de vista contiene el token de dispositivo más reciente recuperado de APN.

5.  Todavía en **AppDelegate.m**, asegúrese de que tiene el siguiente método:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1.  Asegúrese de que **AppBackend** se ha implementado en Azure. Si usa Visual Studio, ejecute la aplicación Web API **AppBackend**. Se mostrará una página web ASP.NET.

2.  En XCode, ejecute la aplicación en un dispositivo iOS físico (las notificaciones de inserción no funcionarán en el simulador).

3.  En la interfaz de usuario de la aplicación iOS, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

4.  En la interfaz de usuario de la aplicación iOS, haga clic en **Log in**. Después, haga clic en **Send push**.

  [Windows Universal]: /es-es/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /es-es/documentation/articles/notification-hubs-/ "iOS"
  [Android]: /es-es/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Registro desde el backend de la aplicación]: http://msdn.microsoft.com/en-us/library/dn743807.aspx
  [Introducción a los Centros de notificaciones (iOS)]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  
