<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-ios" urlDisplayName="Notify iOS app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification iOS app" description="Learn how to request push notification registration in an iOS app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Registro del usuario actual para notificaciones de inserción con un servicio móvil
==================================================================================

[C\# para Tienda Windows](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "C# para Tienda Windows")[iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

Este tema muestra cómo solicitar un registro de notificación de inserción con Centros de notificaciones de Azure al realizar el registro mediante Servicios móviles de Azure. Este tema amplía el tutorial [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users). Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users).

1.  En Xcode, abra el archivo QSTodoService.h en el proyecto que creó cuando completó el tutorial de requisito previo [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-ios/) y agregue la siguiente propiedad **deviceToken**:

         @property (nonatomic) NSData* deviceToken;

	Esta propiedad almacena el token de dispositivo.

1.  En el archivo QSTodoService.m, agregue el siguiente método **getDeviceTokenInHex**:

             - (NSString*)getDeviceTokenInHex {
                 const unsigned *tokenBytes = [[self deviceToken] bytes];
                 NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                       ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                       ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                       ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                 return hexToken;
             }

    Este método convierte el token de dispositivo en un valor de cadena hexadecimal.

2.  En el archivo QSAppDelegate.m, agregue las siguientes líneas de código al método **didFinishLaunchingWithOptions**:

             [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    Con esto se habilitan las notificaciones de inserción en la aplicación.

3.  En el archivo QSAppDelegate.m, agregue el siguiente método:

            - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
                [QSTodoService defaultService].deviceToken = deviceToken;
            }

    Con esto se actualiza la propiedad **deviceToken**.

    **Nota:**

    En este punto, no debería haber ningún otro código en este método. Si ya tiene una llamada al método **registerNativeWithDeviceToken** que se agregó cuando realizó el tutorial [Introducción a los Centros de notificaciones](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios/), debe convertir la llamada en comentario o borrarla.

4.  (Opcional) En el archivo QSAppDelegate.m, agregue el siguiente método de controlador:

            - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
                NSLog(@"%@", userInfo);
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            }

	Este método muestra una alerta en la interfaz de usuario cuando su aplicación recibe notificaciones durante su ejecución.

1.  En el archivo QSTodoListViewController.m, agregue el método **registerForNotificationsWithBackEnd**:

             - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{     - (void)registerForNotificationsWithBackEnd {    
                 NSString* json = [NSString  stringWithFormat:@"{\"platform\":\"ios\", \"deviceToken\":\"%@\"}", [self.todoService getDeviceTokenInHex] ];
                    
                 [self.todoService.client invokeAPI:@"register_notifications" data:[json dataUsingEncoding:NSUTF8StringEncoding] HTTPMethod:@"POST" parameters:nil headers:nil completion:^(id result, NSHTTPURLResponse *response, NSError *error) {
                     if (error != nil) {
                         NSLog(@"Registration failed: %@", error);
                     } else {
                         // mostrar UIAlert con inicio de sesión correcto
                         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                         [alert show];
                     }
                 }];
             }


    Este método construye una carga json que contiene el token de dispositivo. Luego llama a la API personalizada en el Servicio móvil para registrarse para notificación. Este método crea un token de dispositivo para notificaciones de inserción y lo envía, junto con el tipo de dispositivo, al método de API personalizada que crea un registro en los Centros de notificaciones. La API personalizada se definió en [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users).

2.  Finalmente, en el método **viewDidAppear**, agregue una llamada a este nuevo método **registerForNotificationsWithBackEnd** una vez que el usuario se autentica correctamente, tal como aparece en el siguiente ejemplo:

            - (void)viewDidAppear:(BOOL)animated
            {
                MSClient *client = self.todoService.client;
                
                if (client.currentUser != nil) {
                    return;
                }
                    
                [client loginWithProvider:@"microsoftaccount" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                    [self registerForNotificationsWithBackEnd];
                }];
            }

    **Nota:**

    Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users) y actualice el servicio móvil para enviar notificaciones mediante Centros de notificaciones.

