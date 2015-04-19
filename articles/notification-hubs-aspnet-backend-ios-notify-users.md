<properties 
	pageTitle="Notificación a los usuarios con los Centros de notificaciones de Azure" 
	description="Obtenga información acerca de cómo enviar notificaciones de inserción seguras en Azure. Ejemplos de código escritos en Objective-C con la API de .NET." 
	documentationCenter="ios" 
	authors="ysxu" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="yuaxu"/>

#Notificación a los usuarios con los Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding">
    	<a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-/" title="iOS" class="current">iOS</a>
		<a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles. Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivo concreto. Un back-end de ASP.NET WebAPI se usa para autenticar clientes y generar notificaciones, tal y como se muestra en el tema de referencia [Registro desde el back-end de la aplicación](http://msdn.microsoft.com/library/dn743807.aspx).

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el Centro de notificaciones tal y como se describe en [Introducción a los Centros de notificaciones (iOS)](http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/). Este tutorial también es el requisito previo para el tutorial [Inserción segura (iOS)](http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/).
> Si usa Servicios móviles como su servicio back-end, consulte la [versión de Servicios móviles](mobile-services-javascript-backend-ios-push-notifications-app-users.md) de este tutorial.


## Creación y configuración del Centro de notificaciones

Siga las secciones 1 a 5 en [Introducción a los Centros de notificaciones (iOS](http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/). Para obtener recursos adicionales sobre el aprovisionamiento de dispositivos iOS, consulte la guía en [Big Nerd Ranch](http://www.bignerdranch.com/we-teach/how-to-prepare/ios-device-provisioning.html)

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Modificación de la aplicación iOS

1. Abra la aplicación de vista de una página que creó siguiendo las secciones 1 a 5 en [Introducción a los Centros de notificaciones (iOS)](http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/)

> [AZURE.NOTE] En esta sección asumimos que configuró el proyecto con un nombre de organización vacío. Si no es así, debe anteponer el nombre de la organización a todos los nombres de clase.

2. En Main.storyboard, agregue los siguientes componentes desde la biblioteca de objetos:
	+ UITextField con el texto de marcador de posición **Username**
	+ UITextField con el texto de marcador de posición **Password** y marque la opción **Secure** en el Inspector de atributos, en Textfield Return Key
	+ Un UIButton etiquetado **1. Inicie sesión** y desactive la opción **Habilitado** en el Inspector de atributos, en Control y luego Contenido
	+ Un UIButton etiquetado **2. Enviar inserción** y desactive la opción **Habilitado**

	El guión gráfico debe tener el aspecto siguiente:

    ![][IOS1]

3. Cree salidas para UITextField y UIButton en la parte de la interfaz de su ViewController.h

	    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
		@property (weak, nonatomic) IBOutlet UITextField *PasswordField;
		@property (weak, nonatomic) IBOutlet UIButton *LogInButton;
		@property (weak, nonatomic) IBOutlet UIButton *SendPushButton;

		- (IBAction)LogInAction:(id)sender;
		- (IBAction)SendPushAction:(id)sender;

4. Primero crearemos una clase RegisterClient a modo de interfaz con nuestro back-end. Cree una clase Objective-C llamada RegisterClient heredada de NSObject. Después agregue el siguiente código en la sección de interfaz RegisterClient.h:

		@property (strong, nonatomic) NSString* authenticationHeader;
		-(void) registerWithDeviceToken:(NSData*) token tags:(NSSet*) tags andCompletion: (void(^)(NSError*)) completion;

5. En RegisterClient.m, agregue la siguiente sección de interfaz:

		@interface RegisterClient ()

		@property (strong, nonatomic) NSURLSession* session;
		-(void) tryToRegisterWithDeviceToken:(NSData*) token tags:(NSSet*) tags retry: (BOOL) retry andCompletion: (void(^)(NSError*)) completion;
		-(void) retrieveOrRequestRegistrationIdWithDeviceToken: (NSString*) token completion: (void(^)(NSString*, NSError*)) completion;
		-(void) upsertRegistrationWithRegistrationId: (NSString*) registrationId deviceToken: (NSString*) token tags: (NSSet*)tags andCompletion: (void(^)(NSURLResponse*, NSError*)) completion;

		@end

6. Después agregue el siguiente código en la sección de implementación RegisterClient.m y sustituya el marcador de posición *{backend endpoint}* por el extremo usado para implementar el back-end de la aplicación en la sección anterior.

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

	El código anterior implementa la lógica explicada en el artículo de referencia [Registro desde el back-end de la aplicación](http://msdn.microsoft.com/library/dn743807.aspx) usando NSURLSession para realizar llamadas REST al back-end de la aplicación y NSUserDefaults para almacenar localmente del identificador de registro devuelto por el Centro de notificaciones.

	Tenga en cuenta que esta clase requiere que su propiedad **authorizationHeader** esté establecida para que funcione correctamente. Esta propiedad la establece la clase **ViewController** después del inicio de sesión.

7. En ViewController.h, agregue la siguiente declaración para el token de dispositivo y haga referencia a una instancia de RegisterClient:

		@property (strong, nonatomic) NSData* deviceToken;
		@property (strong, nonatomic) RegisterClient* registerClient;

8. En ViewController.m, haga que la clase ViewController sea UITextFieldDelegate. A continuación, agregue una declaración de método privado:

		@interface ViewController () <UITextFieldDelegate>

		// create the Authorization header to perform Basic authentication with your app back-end
		-(void) createAndSetAuthenticationHeaderWithUsername: (NSString*) username AndPassword: (NSString*) password;

		@end

> [AZURE.NOTE] El siguiente fragmento de código no es un esquema de autenticación seguro; debe sustituir la implementación de **createAndSetAuthenticationHeaderWithUsername:AndPassword:** por el mecanismo de autenticación específico que genera un token de autenticación para ser consumido por la clase de cliente del registro, por ejemplo, OAuth, Active Directory.

9. Después, en la sección de implementación de ViewController.m, agregue el siguiente código:

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

	Observe cómo al establecer el token del dispositivo se habilita el botón de inicio de sesión. Esto se debe a que, como una parte de la acción de inicio de sesión, el controlador de vista registra notificaciones de inserción con el back-end de la aplicación. Por lo tanto, no se desea que la acción Iniciar sesión sea accesible hasta que el token del dispositivo se haya configurado correctamente. Es posible que desacople el inicio de sesión del registro de inserción, siempre que lo primero ocurra antes que lo último.

10. En ViewController.m, agregue una constante para el extremo backend y use los siguientes fragmentos de código para implementar los métodos de acción para los UIButtons. Reemplace el extremo de backend de marcador de posición por la dirección URL de destino que utilizó para el back-end.

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

11. En la función **ViewDidLoad**, agregue el siguiente código para crear una instancia de RegisterClient y establezca el delagado para sus campos de texto.

		self.UsernameField.delegate = self;
		self.PasswordField.delegate = self;
		self.registerClient = [[RegisterClient alloc] init];

12. Ahora, en **AppDelegate.m**, quite todo el contenido del método **application:didRegisterForPushNotificationWithDeviceToken:** y reemplácelo por lo siguiente para asegurarse de que el controlador de vista contiene el token de dispositivo más recienterecuperado desde APN:

	    ViewController* rvc = (ViewController*) self.window.rootViewController;
	    rvc.deviceToken = deviceToken;

13. Finalmente, en **AppDelegate.m**, asegúrese de que tiene el siguiente método:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		                          [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		                          @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

## Ejecución de la aplicación

1. En XCode, ejecute la aplicación en un dispositivo iOS físico (las notificaciones de inserción no funcionarán en el simulador).

2. En la interfaz de usuario de la aplicación iOS, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor. A continuación, haga clic en **Iniciar sesión**.

3. Debería ver una ventana emergente que le informa del éxito de registro. Haga clic en **OK**.

4. Haga clic en **Enviar inserción** y presione el botón de inicio. Una notificación de inserción aparecerá en breve.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users1.png

<!--HONumber=45--> 
