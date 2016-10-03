<properties
	pageTitle="Procedimiento de actualización del SDK de iOS de Azure Mobile Engagement | Microsoft Azure"
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="piyushjo" />

#Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

##De 3.0.0 a 4.0.0

### XCode 8
XCode 8 es obligatorio a partir de la versión 4.0.0 del SDK.

> [AZURE.NOTE] Si realmente depende de XCode 7, puede usar el [SDK v3.2.4 de Engagement para iOS](https://aka.ms/r6oouh). Existe un problema conocido en el módulo de cobertura de esta versión anterior cuando se ejecuta en dispositivos iOS 10: las notificaciones del sistema no se ejecutan. Para solucionarlo, tendrá que implementar la API `application:didReceiveRemoteNotification:` en desuso en su delegado de aplicación de la manera siguiente:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **No se recomienda esta solución** ya que este comportamiento puede cambiar en la próxima actualización de la versión de iOS (por menor que sea) porque esta API de iOS está en desuso. Debe cambiar a XCode 8 tan pronto como sea posible.

### Marco de UserNotifications
Debe agregar el marco `UserNotifications` en sus fases de compilación.

En el Explorador de proyectos, abra su panel de proyectos y seleccione el destino adecuado. A continuación, abra la pestaña **"Build phases"** (Fases de compilación) y en el menú **"Link Binary With Libraries"** (Vincular binarios con bibliotecas), agregue el marco `UserNotifications.framework` y establezca el vínculo como `Optional`.

### Funcionalidad de inserción de la aplicación
XCode 8 puede restablecer la funcionalidad de inserción de su aplicación, solo tiene que marcarla dos veces en la pestaña `capability` del destino seleccionado.

### Si ya tiene su propia implementación de UNUserNotificationCenterDelegate

El SDK también tiene su propia implementación del protocolo UNUserNotificationCenterDelegate. Se usa para supervisar el ciclo de vida de las notificaciones de Engagement en dispositivos que se ejecutan en iOS 10 o superior. Si el SDK detecta que el delegado no usa su propia implementación porque puede que solo haya un delegado UNUserNotificationCenter por aplicación. Esto significa que tendrá que agregar la lógica de Engagement a su propio delegado.

Hay dos formas de hacerlo:

Reenviar las llamadas del delegado al SDK:

	#import <UIKit/UIKit.h>
	#import "EngagementAgent.h"
	#import <UserNotifications/UserNotifications.h>


	@interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
	@end

	@implementation MyAppDelegate

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
	}
	@end

O bien, heredarla de la clase `AEUserNotificationHandler`

	#import "AEUserNotificationHandler.h"
	#import "EngagementAgent.h"

	@interface CustomUserNotificationHandler :AEUserNotificationHandler
	@end

	@implementation CustomUserNotificationHandler

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
	}

	- (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
	{
	  // Your own logic.

	  [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
	}

	@end

> [AZURE.NOTE] Puede determinar si una notificación proviene de Engagement o no pasando su diccionario `userInfo` al método de clase `isEngagementPushPayload:` del agente.

##De 2.0.0 a 3.0.0
Soporte de iOS 4.X eliminado. A partir de esta versión, el destino de implementación de la aplicación debe ser como mínimo iOS 6.

Si usa Reach en la aplicación, debe agregar el valor `remote-notification` a la matriz `UIBackgroundModes` en el archivo Info.plist para recibir notificaciones remotas.

El método `application:didReceiveRemoteNotification:` debe reemplazarse por `application:didReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de aplicación.

"AEPushDelegate.h" es una interfaz desusada y debe quitar todas las referencias. Esto incluye eliminar `[[EngagementAgent shared] setPushDelegate:self]` y los métodos delegados del delegado de la aplicación:

	-(void)willRetrieveLaunchMessage;
	-(void)didFailToRetrieveLaunchMessage;
	-(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##De 1.16.0 a 2.0.0
A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement. Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.16 en primer lugar luego aplique el siguiente procedimiento.

>[AZURE.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

### Agente

El método `registerApp:` se ha reemplazado por el nuevo método `init:`. El delegado de la aplicación deben actualizarse como corresponda y usar una cadena de conexión:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Seguimiento de SmartAd se ha quitado del SDK y solo tiene que quitar todas las instancias de la clase `AETrackModule`

### Cambios del nombre de la clase

Como parte de la reconfiguración de marca, hay algunos nombres de clase/archivo que deben cambiarse.

Todas las clases precedidas de "CP" cambian su nombre introduciendo el prefijo "AE".

Ejemplo:

-   `CPModule.h` cambia su nombre a `AEModule.h`.

Todas las clases con el prefijo "Capptain" cambian su nombre para introducir el prefijo "Engagement".

Ejemplos:

-   La clase `CapptainAgent` cambia su nombre a `EngagementAgent`.
-   La clase `CapptainTableViewController` cambia su nombre a `EngagementTableViewController`.
-   La clase `CapptainUtils` cambia su nombre a `EngagementUtils`.
-   La clase `CapptainViewController` cambia su nombre a `EngagementViewController`.

<!---HONumber=AcomDC_0921_2016-->