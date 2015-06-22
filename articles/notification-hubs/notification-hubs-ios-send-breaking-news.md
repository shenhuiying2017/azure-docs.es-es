<properties 
	pageTitle="Tutorial de noticias de última hora en los Centros de notificaciones: iOS" 
	description="Obtenga información acerca del uso de Centros de notificaciones de Azure para enviar notificaciones de noticias de última hora en dispositivos iOS." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="wesmc"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora
<div class="dev-center-tutorial-selector sublanding">
	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS" class="current">iOS</a>
	<a href="/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

##Información general

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación iOS. Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc.

Los escenarios de difusión se habilitan mediante la inclusión de una o más _etiquetas_ cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para obtener información sobre las etiquetas, consulte [Información general acerca de los Centros de notificaciones].


##Requisitos previos

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones][get-started]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones][get-started].

##Incorporación de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario al guión gráfico existente que permiten al usuario seleccionar las categorías que se van a registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas.

2. En MainStoryboard_iPhone.storyboard, agregue los siguientes componentes desde la biblioteca de objetos:
	+ Una etiqueta con el texto "Breaking News",
	+ Etiquetas con los textos de categoría "World", "Politics", "Business", "Technology", "Science", "Sports",
	+ Seis modificadores, uno por categoría,
	+ Un botón etiquetado con "Subscribe"

	El guión gráfico debe tener el aspecto siguiente:

	![][3]

3. En el editor del asistente, cree medios para todos los modificadores y llámelos "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch" y "SportsSwitch".

	![][4]

4. Cree una Acción para el botón con nombre "subscribe". El BreakingNewsViewController.h debe contener lo siguiente:

		@property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
		@property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

		- (IBAction)subscribe:(id)sender;

5. Cree una nueva clase y nómbrela  `Notifications`. Copie el siguiente código en la sección de interfaz del archivo Notifications.h:

		@property NSData* deviceToken;

		- (void)storeCategoriesAndSubscribeWithCategories:(NSArray*) categories completion: (void (^)(NSError* error))completion;
		- (void)subscribeWithCategories:(NSSet*) categories completion:(void (^)(NSError *))completion;

6. Agregue la siguiente directiva de importación a Notifications.m:

		#import <WindowsAzureMessaging/WindowsAzureMessaging.h>

7. Copie el siguiente código en la sección de implementación del archivo Notifications.m:

		- (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
		    [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

		    [self subscribeWithCategories:categories completion:completion];
		}

		- (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion{
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string with listen access>" notificationHubPath:@"<hub name>"];

		    [hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];
		}

	Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. También contiene métodos para registrar estas categorías.

4. En el código anterior, reemplace los marcadores de posición  `<hub name>`  y  `<connection string with listen access>`  por el nombre de su centro de notificaciones y la cadena de conexión para  *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

	> [AZURE.NOTE] Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

8. En el archivo BreakingNewsAppDelegate.h, agregue la siguiente propiedad:

		@property (nonatomic) Notifications* notifications;

	Esto crea una instancia singleton de la clase Notification en AppDelegate.

9. En el método **didFinishLaunchingWithOptions** en BreakingNewsAppDelegate.m, agregue el siguiente código delante de **registerForRemoteNotificationTypes**:

		self.notifications = [[Notifications alloc] init];

	Así se inicializa el singleton de Notification.

10. En el método **didRegisterForRemoteNotificationsWithDeviceToken** en BreakingNewsAppDelegate.m, quite la llamada a **registerNativeWithDeviceToken** y agregue el siguiente código:

		self.notifications.deviceToken = deviceToken;

	Tenga en cuenta que, en este punto, no debe haber otro código en el método **didRegisterForRemoteNotificationsWithDeviceToken**.

11.	Agregue el siguiente método en BreakingNewsAppDelegate.m:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:
			(NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
		    [alert show];
	    }

	Este método controla las notificaciones recibidas cuando la aplicación está en ejecución mostrando un **UIAlert** sencillo.

9. En BreakingNewsViewController.m, copie el siguiente código en el método **subscribe** generado con XCode:

		NSMutableArray* categories = [[NSMutableArray alloc] init];

	    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
	    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
	    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
	    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
	    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
	    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

	    Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
	        if (!error) {
	            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
	                                  @"Subscribed!" delegate:nil cancelButtonTitle:
	                                  @"OK" otherButtonTitles:nil, nil];
	            [alert show];
	        } else {
	            NSLog(@"Error subscribing: %@", error);
	        }
	    }];

	Este método crea un **NSMutableArray** de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el Centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el Centro de notificaciones siempre que el usuario cambie la selección de categorías.

##Registro de notificaciones

Estos pasos permiten registrar el Centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local.

> [AZURE.NOTE] Puesto que el token del dispositivo asignado por el Servicio de notificaciones push de Apple (APNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

1. Agregue el siguiente método en la sección de interfaz del archivo Notifications.h:

		- (NSSet*)retrieveCategories;

	Este código recupera las categorías en la clase Notifications.

2. Agregue la implementación correspondiente en el archivo Notifications.m:

		- (NSSet*)retrieveCategories {
		    NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

		    NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

		    if (!categories) return [[NSSet alloc] init];
		    return [[NSSet alloc] initWithArray:categories];
		}

2. Agregue el siguiente código al método **didRegisterForRemoteNotificationsWithDeviceToken**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];
	    [notifications subscribeWithCategories:categories completion:^(NSError* error) {
	        if (error != nil) {
	            NSLog(@"Error registering for notifications: %@", error);
	        }
	    }];

	De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías.

3. En BreakingNewsViewController.h, agregue el siguiente código al método **viewDidLoad**:

		Notifications* notifications = [(BreakingNewsAppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

	    NSSet* categories = [notifications retrieveCategories];

	    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
	    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
	    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
	    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
	    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
	    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;

	De esta forma, se actualiza la interfaz de usuario en el inicio según el estado de las categorías guardadas anteriormente.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el Centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

##Envío de notificaciones desde su back-end

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Ejecución de la aplicación y generación de notificaciones

1. Presione el botón Ejecutar para compilar el proyecto e iniciar la aplicación.

	![][2]

	Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse.

2. Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Subscribe**.

	Al elegir **Suscribirse**, la aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas desde el Centro de notificaciones.

4. Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

	+ **Aplicación de consola:** inicio de la aplicación de consola.

	+ **Java/PHP:** ejecute su aplicación o script.

5. Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

## Pasos siguientes

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de Centros de notificaciones avanzados:

+ **[Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]**

	Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas.

+ **[Notificación a los usuarios con los Centros de notificaciones]**

	Conozca cómo insertar notificaciones para usuarios autenticados específicos. Esta es una buena solución para enviar notificaciones solo a usuarios específicos.



<!-- Images. -->
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png
[4]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios3.png







<!-- URLs. -->
[Trabajo Centros de notificaciones de Bus de servicio (aplicaciones iOS)]: http://msdn.microsoft.com/library/jj927168.aspx
[Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Servicio móvil]: /develop/mobile/tutorials/get-started
[Notificación a los usuarios con los Centros de notificaciones]: /manage/services/notification-hubs/notify-users/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Información general acerca de los Centros de notificaciones]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/

<!--HONumber=49--> 