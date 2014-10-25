<properties title="Azure Notification Hubs Secure Push" pageTitle="Azure Notification Hubs Secure Push" metaKeywords="Azure push notifications, Azure notification hubs, secure push" description="Learn how to send secure push notifications to an iOS app from Azure. Code samples written in Objective-C and C#." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"/>

# Inserción segura de los Centros de notificaciones de Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/es-es/documentation/articles/notification-hubs-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS" class="current">iOS</a>
        <a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android">Android</a>
</div>

La compatibilidad con las notificaciones de inserción en Microsoft Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones de inserción tanto en aplicaciones de consumidor, como en aplicaciones empresariales para plataformas móviles.

Debido a restricciones reguladoras o de seguridad, algunas veces una aplicación podría querer incluir algo en la notificación que no se puede trasmitir a través de la infraestructura de las notificaciones de inserción estándar. En este tutorial se describe cómo lograr la misma experiencia enviando información importante a través de una conexión segura y autenticada entre el dispositivo cliente y el back-end de la aplicación.

A un alto nivel, el flujo es el siguiente:

1.  El back-end de la aplicación:

    -   Almacena la carga segura en la base de datos back-end.
    -   Envía el identificador de esta notificación al dispositivo (no se envía información segura).

2.  La aplicación del dispositivo, cuando recibe la información:

    -   El dispositivo entra en contacto con el back-end que solicita la carga segura.
    -   La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante tener en cuenta que en el flujo anterior (y en este tutorial), asumimos que el dispositivo almacena un token de autenticación localmente y, después, el usuario inicia sesión. Esto garantiza una experiencia sin ningún problema, ya que el dispositivo puede recuperar la carga segura de la notificación usando este token. Si la aplicación no almacena tokens de autenticación en el dispositivo, o si estos tokens han expirado, la aplicación del dispositivo, al recibir la notificación, debe mostrar una notificación genérica pidiendo al usuario que inicie la aplicación. Después, la aplicación autentica al usuario y muestra la carga de la notificación.

Este tutorial Inserción segura muestra cómo enviar una notificación de inserción de forma segura. El tutorial se basa en el tutorial **Notificar a los usuarios**, por lo que debe completar los pasos de ese tutorial primero.

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal y como se describe en [Introducción a los Centros de notificaciones (iOS)][].

[WACOM.INCLUDE [notification-hubs-aspnet-backend-securepush][]]

## Modificación del proyecto iOS

Ahora que modificó el back-end de la aplicación para enviar solamente el *identificador* de una notificación, tiene que cambiar la aplicación iOS para administrar esa notificación y devolver la llamada a su back-end para recuperar el mensaje seguro que se debe mostrar.

Para lograr este objetivo, tenemos que escribir la lógica para recuperar el contenido seguro del back-end de la aplicación.

1.  En su **AppDelegate.m** agregue una sección de implementación en la parte superior con la siguiente declaración:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

2.  Después, en la sección de implementación, agregue el siguiente código, sustituyendo el marcador de posición `{back-end endpoint}` por el extremo para el back-end obtenido anteriormente:

        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];    
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

    Este método llama al back-end de la aplicación para recuperar el contenido de la notificación usando las credenciales almacenadas en las preferencias compartidas.

3.  Ahora tenemos que administrar la notificación entrante y usar el método anterior para recuperar el contenido para mostrar. Primero, tenemos que habilitar la aplicación iOS para que se ejecute en segundo plano cuando reciba una notificación de inserción. En **XCode**, seleccione el proyecto de aplicación en el panel izquierdo y después haga clic en el destino de la aplicación principal en la sección **Targets** del panel central.

4.  Después, haga clic en la pestaña **Capabilities** situada en la parte superior del panel central y active la casilla **Remote Notifications**.

    ![](./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png)

5.  En **AppDelegate.m** agregue el siguiente método para administrar notificaciones de inserción:

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Tenga en cuenta que es preferible administrar los casos de propiedad de encabezado de autenticación ausente o rechazo por el back-end. La administración específica de estos casos depende principalmente de la experiencia del usuario de destino. Una opción es mostrar una notificación con un mensaje genérico para el usuario con el fin de que se autentique para recuperar la notificación real.

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1.  Asegúrese de que **AppBackend** se ha implementado en Azure. Si usa Visual Studio, ejecute la aplicación Web API **AppBackend**. Se mostrará una página web ASP.NET.

2.  En XCode, ejecute la aplicación en un dispositivo iOS físico (las notificaciones de inserción no funcionarán en el simulador).

3.  En la interfaz de usuario de la aplicación iOS, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

4.  En la interfaz de usuario de la aplicación iOS, haga clic en **Log in**. Después, haga clic en **Send push**. Debe ver la notificación segura mostrada en el centro notificaciones.

  [Windows Universal]: /es-es/documentation/articles/notification-hubs-windows-dotnet-secure-push/ "Windows Universal"
  [iOS]: /es-es/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/ "iOS"
  [Android]: /es-es/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/ "Android"
  [Introducción a los Centros de notificaciones (iOS)]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-ios-get-started/
  [notification-hubs-aspnet-backend-securepush]: ../includes/notification-hubs-aspnet-backend-securepush.md
   
