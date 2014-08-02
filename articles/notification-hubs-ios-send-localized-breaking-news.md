<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-iOS" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial for iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications (iOS)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news to iOS devices" authors="ricksal" solutions="" manager="" editor="" />

Uso de los Centros de notificaciones para enviar noticias de última hora localizadas a dispositivos iOS
=======================================================================================================

[C\# para Tienda Windows](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet "C# para Tienda Windows")[iOS](/en-us/manage/services/notification-hubs/breaking-news-localized-ios "iOS")

Este tema muestra cómo usar la característica de **plantilla** de los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora localizadas por lenguaje y dispositivo. En este tutorial comenzará con la aplicación de la Tienda Windows que se creó en el tutorial [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-ios). Una vez que lo haya completado, podrá registrarse en las categorías que le interesan, especificar un idioma para recibir las notificaciones y recibir solo notificaciones de inserción para las categorías seleccionadas en dicho idioma.

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1.  [Conceptos de plantilla](#concepts)
2.  [La interfaz de usuario de la aplicación](#ui)
3.  [Creación de la aplicación iOS](#building-client)
4.  [Envío de notificaciones desde su back-end](#send)

Este escenario tiene dos partes:

-   La aplicación iOS permite que los dispositivos cliente especifiquen un idioma y que se suscriban a distintas categorías de noticias de última hora;

-   el back-end difunde las notificaciones, mediante las características de **etiqueta** y **plantilla** de los Centros de notificaciones de Azure.

Requisitos previos
------------------

Debe haber completado el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-ios) y debe tener disponible el código, porque este tutorial se basa directamente en ese código.

También necesita Visual Studio 2012.

conceptosConceptos de plantilla
-------------------------------

En el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-ios) creó una aplicación que utilizó **etiquetas** para suscribirse a notificaciones para distintas categorías de noticias. Sin embargo, muchas aplicaciones están dirigidas a varios mercados y requieren localización. Esto significa que el contenido de las notificaciones mismas se debe localizar y entregar al conjunto de dispositivos correcto. En este tema podremos mostrar cómo usar la característica de **plantilla** de los Centros de notificaciones para entregar fácilmente notificaciones de noticias de última hora localizadas.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y chino mandarín, necesitaríamos tres etiquetas distintas para noticias mundiales: "mundo\_in", "mundo\_fr" y "mundo\_ch". Luego tendríamos que enviar una versión localizada de las noticias mundiales a cada una de estas etiquetas. En este tema usamos plantillas para evitar la proliferación de etiquetas y el requisito de enviar varios mensajes.

A un alto nivel, las plantillas son una forma de especificar la manera en que un dispositivo específico debe recibir una notificación. La plantilla especifica el formato de carga exacto haciendo referencia a las propiedades que forman parte del mensaje enviado por el back-end de la aplicación. En nuestro caso, enviaremos un mensaje independiente de la configuración regional que contengan todos los idiomas compatibles:

    {
        "Noticias_Inglés": "...",
        "Noticias_Francés": "...",
        "Noticias_Mandarín": "..."
    }

Esto garantizará que los dispositivos se registren con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación iOS que desea registrarse para noticias en francés se registrará de la siguiente manera:

    {
        aps:{
            alert: "$(Noticias_Francés)"
        }
    }

Las plantillas son una característica muy potente de la que puede aprender más en nuestro artículo [Información general acerca de los Centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/jj927170.aspx). Puede encontrar una referencia sobre el lenguaje de la expresión de plantilla en [Centros de notificaciones de bus de servicio (aplicaciones iOS)](http://msdn.microsoft.com/en-us/library/jj927168.aspx).

Interfaz de usuario de aplicaciónLa interfaz de usuario de la aplicación
------------------------------------------------------------------------

Ahora modificaremos la aplicación de noticias de última hora que creó en el tema [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-ios) para enviar noticias de última hora localizadas con plantillas.

En MainStoryboard\_iPhone.storyboard, agregue un control segmentado con los tres idiomas compatibles: inglés, francés y chino mandarín

![](./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png)

A continuación, asegúrese de agregar un IBOutlet en ViewController.h tal como se muestra a continuación:

![](./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png)

Interfaz de usuario de aplicaciónCreación de la aplicación iOS
--------------------------------------------------------------

Con la finalidad de adaptar sus aplicaciones clientes para que reciban mensajes localizados, debe reemplazar sus registros *nativos* (es decir, registros que especifica en una plantilla) por registros de plantilla.

1.  En Notification.h, agregue el método *retrieveLocale*, modifique el almacén y suscríbase a métodos tal como se muestra a continuación:

         - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

         - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
            
         - (NSSet*) retrieveCategories;
            
         - (int) retrieveLocale;

    En Notification.m, modifique el método *storeCategoriesAndSubscribe* agregando el parámetro de configuración regional y almacenándolo en los valores predeterminados del usuario:

         - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
             NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
             [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
             [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
             [defaults synchronize];
                
             [self subscribeWithLocale: locale categories:categories completion:completion];
         }

    Luego modifique el método *subscribe* para incluir la configuración
    regional:
    
         - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection  string>" notificationHubPath:@"<hub  name>"];
        	    
             NSString* localeString;
             switch (locale) {
                 case 0:
                     localeString = @"Inglés";
                     break;
                 case 1:
                     localeString = @"Francés";
                     break;
                 case 2:
                     localeString = @"Mandarín";
                     break;
             }
        	    
             NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
        	    
             [hub registerTemplateWithDeviceToken:self.deviceToken name:@"newsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
         }

    Observe cómo usamos ahora el método *registerTemplateWithDeviceToken* en lugar de *registerNativeWithDeviceToken*. Cuando nos registramos para obtener una plantilla, tenemos que proporcionar la plantilla json y también ponerle un nombre (dado que nuestra aplicación puede querer registrar distintas plantillas). Asegúrese de registrar sus categorías como etiquetas, debido a que queremos asegurarnos de recibir las notificaciones para dichas noticias.

    Finalmente, agregue un método para recuperar la configuración regional a partir de los ajustes predeterminados del usuario:

         - (int) retrieveLocale {
             NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
                
             int locale = [defaults integerForKey:@"BreakingNewsLocale"];
                
             return locale < 0?0:locale;
         }

2.  Ahora que hemos modificado nuestra clase de notificaciones, debemos asegurarnos de que nuestro ViewController utilice el nuevo UISegmentControl. Agregue la siguiente línea en el método *viewDidLoad* para asegurarse de mostrar la configuración regional actualmente seleccionada:

         self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    A continuación, en el método *subscribe*, cambie la llamada a *storeCategoriesAndSubscribe* a lo siguiente:

         [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
             if (!error) {
                 UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                       @"Subscribed!" delegate:nil cancelButtonTitle:
                                       @"OK" otherButtonTitles:nil, nil];
                 [alert show];
             } else {
                 NSLog(@"Error subscribing: %@", error);
             }
         }];

3.  Finalmente, debe actualizar el método *didRegisterForRemoteNotificationsWithDeviceToken* en AppDelegate.m, para que pueda actualizar correctamente el registro cuando se inicie la aplicación. Cambie la llamada al método *subscribe* de notificaciones con lo siguiente:

         NSSet* categories = [notifications retrieveCategories];
         int locale = [notifications retrieveLocale];
         [notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
             if (error != nil) {
                 NSLog(@"Error de registro de notificaciones: %@", error);
             }
         }];

Envío de notificaciones localizadasEnvío de notificaciones localizadas desde el back-end
----------------------------------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

Pasos siguientes
----------------

Para obtener más información sobre el uso de plantillas, consulte:

-   [Notificación a los usuarios con los Centros de notificaciones: ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet)
-   [Notificación a los usuarios con los Centros de notificaciones: Servicios móviles](/en-us/manage/services/notification-hubs/notify-users)
-   [Información general acerca de los Centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/jj927170.aspx)

Puede encontrar una referencia para el lenguaje de la expresión de plantilla en [Procedimientos de los Centros de notificaciones para iOS](http://msdn.microsoft.com/en-us/library/jj927168.aspx).

