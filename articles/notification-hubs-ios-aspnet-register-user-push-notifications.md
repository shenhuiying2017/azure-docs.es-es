<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-ios" urlDisplayName="Notify iOS app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification iOS app" description="Learn how to request push notification registration in an iOS app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="" solutions="" manager="" editor="" />

Registro del usuario actual para las notificaciones de inserción mediante ASP.NET
=================================================================================

[C\# para Tienda Windows](/en-us/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "C# para Tienda Windows")[iOS](/en-us/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS")

En este tema se describe cómo solicitar el registro de las notificaciones de inserción con los Centros de notificaciones de Azure al realizar el registro mediante ASP.NET Web API. Este tema amplía el tutorial [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-aspnet). Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-aspnet).

1.  En su MainStoryboard\_iPhone.storyboard, agregue los siguientes componentes desde la biblioteca de objetos:

    -   **Label**: "Push to User with Notification Hubs"
    -   **Label**: "InstallationId"
    -   **Label**: "User"
    -   **Text Field**: "User"
    -   **Label**: "Password"
    -   **Text Field**: "Password"
    -   **Button**: "Login"

    En este punto, su storyboard debe presentar la siguiente apariencia:

  ![][0] 

2.  En el assistant editor, cree outlets para todos los switched controls y llámelos, conecte los campos de texto con View Controller (delegate) y cree una **Action** para el botón **login**.

  ![][1]

  El archivo BreakingNewsViewController.h ahora debe contener el siguiente código:
                
		@property (weak, nonatomic) IBOutlet UILabel *installationId;
		@property (weak, nonatomic) IBOutlet UITextField *User;
		@property (weak, nonatomic) IBOutlet UITextField *Password;
            
		- (IBAction)login:(id)sender;

3.  Cree una clase llamada **DeviceInfo** y copie el siguiente código en la sección de la interfaz del archivo DeviceInfo.h:

         @property (readonly, nonatomic) NSString* installationId;
         @property (nonatomic) NSData* deviceToken;

4.  Copie el siguiente código en la sección de implementación del archivo DeviceInfo.m:

             @synthesize installationId = _installationId;

             - (id)init {
                 if (!(self = [super init]))
                     return nil;
                    
                 NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                 _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                 if(!_installationId) {
                     CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                     _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                     CFRelease(newUUID);
                        
                     //Almacenar el ID de instalación para que no generemos uno nuevo la próxima vez
                     [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                     [defaults synchronize];
                 }
                    
                 return self;
             }
                
             - (NSString*)getDeviceTokenInHex {
                 const unsigned *tokenBytes = [[self deviceToken] bytes];
                 NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                       ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                       ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                       ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                 return hexToken;
             }

5.  En PushToUserAppDelegate.h, agregue la siguiente propiedad singleton:

         @property (strong, nonatomic) DeviceInfo* deviceInfo;

6.  En el método **didFinishLaunchingWithOptions** en PushToUserAppDelegate.m, agregue el siguiente código:

         self.deviceInfo = [[DeviceInfo alloc] init];
            
         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    La primera línea inicializa el singleton **DeviceInfo**. La segunda línea inicia el registro de notificaciones de inserción, que ya existe si ya ha completado el tutorial [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios).

7.  En PushToUserAppDelegate.m, implemente el método **didRegisterForRemoteNotificationsWithDeviceToken** en su AppDelegate y agregue el siguiente código:

         self.deviceInfo.deviceToken = deviceToken;

    De este modo se configura el token del dispositivo para la solicitud.

    **Nota:**

    En este punto, no debería haber ningún otro código en este método. Si ya tiene una llamada al método **registerNativeWithDeviceToken** que se agregó cuando realizó el tutorial [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/), debe convertir la llamada en comentario o borrarla.

8.  En el archivo PushToUserAppDelegate.m, agregue el siguiente método de controlador:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

    Este método muestra una alerta en la interfaz de usuario cuando su aplicación recibe notificaciones durante su ejecución.

9.  Abra el archivo PushToUserViewController.m y escriba la siguiente implementación:

         - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
             if (theTextField == self.User || theTextField == self.Password) {
                 [theTextField resignFirstResponder];
             }
             return YES;
         }

10. En el método **viewDidLoad** del archivo PushToUserViewController.m, inicialice la etiqueta installationId como se muestra a continuación:

         DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
         Self.installationId.text = deviceInfo.installationId;

11. Agregue las siguientes propiedades en la interfaz en PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

12. Luego, agregue la siguiente implementación:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }
                
            // Condificación base64
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];
                    
                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";
                    
                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;
                    
                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;
                            
                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }
                        
                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }
                    
                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }

13. Copie el siguiente código en el método controlador **login** creado por XCode:

		DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

		// Compilar JSON
		NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];
	    
		// Compilar authString
		NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];
	    
		NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
		[request setHTTPMethod:@"POST"];
		[request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
		[request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
		[request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
		[request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];
	    
		// Conectar con POST
		[NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
			// Agregar UIAlert dependiendo de la respuesta.
			if (error != nil) {
				NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
				if ([httpResponse statusCode] == 200) {
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
					[alert show];
				} else {
					NSLog(@"status: %ld", (long)[httpResponse statusCode]);
				}
			} else {
				NSLog(@"error: %@", error);
			}
		}];

    Este método obtiene un ID de instalación y un canal para las notificaciones de inserción y los envía, junto con el tipo de dispositivo, al método de Web API autenticado que crea un registro en los Centros de notificaciones. Esta Web API se definió en [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-aspnet).

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-aspnet) y actualice el servicio móvil para enviar notificaciones mediante Centros de notificaciones.

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png