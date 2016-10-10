<properties
	pageTitle="Información general de SDK de iOS de Azure Mobile Engagement | Microsoft Azure"
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

#SDK de iOS para Azure Mobile Engagement

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de iOS. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-ios-get-started.md)

Haga clic para ver el [contenido del SDK](mobile-engagement-ios-sdk-content.md)

##Procedimientos de integración
1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md)


##Notas de la versión

### 4\.0.0 (09/12/2016)

-   Notificación fija no ejecutada en dispositivos iOS 10.
-   XCode 7 en desuso.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-ios-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-ios-upgrade-procedure.md).

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

###De 3.0.0 a 4.0.0

### XCode 8
XCode 8 es obligatorio a partir de la versión 4.0.0 del SDK.

> [AZURE.NOTE] Si realmente depende de XCode 7, puede usar el [SDK v3.2.4 de Engagement para iOS](https://aka.ms/r6oouh). Existe un problema conocido en el módulo de cobertura de esta versión anterior cuando se ejecuta en dispositivos iOS 10: las notificaciones del sistema no se ejecutan. Para solucionarlo, tendrá que implementar la API `application:didReceiveRemoteNotification:` en desuso en su delegado de aplicación de la manera siguiente:

	- (void)application:(UIApplication*)application
	didReceiveRemoteNotification:(NSDictionary*)userInfo
	{
	    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
	}

> [AZURE.IMPORTANT] **No se recomienda esta solución** ya que este comportamiento puede cambiar en la próxima actualización de la versión de iOS (por menor que sea) porque esta API de iOS está en desuso. Debe cambiar a XCode 8 tan pronto como sea posible.

#### Marco de UserNotifications
Debe agregar el marco `UserNotifications` en sus fases de compilación.

En el Explorador de proyectos, abra su panel de proyectos y seleccione el destino adecuado. A continuación, abra la pestaña **"Build phases"** (Fases de compilación) y en el menú **"Link Binary With Libraries"** (Vincular binarios con bibliotecas), agregue el marco `UserNotifications.framework` y establezca el vínculo como `Optional`.

#### Funcionalidad de inserción de la aplicación
XCode 8 puede restablecer la funcionalidad de inserción de su aplicación, solo tiene que marcarla dos veces en la pestaña `capability` del destino seleccionado.

#### Adición del nuevo código de registro de notificaciones de iOS 10
El fragmento de código anterior para registrar la aplicación para las notificaciones aún funciona, pero está utilizando API obsoletas mientras se ejecuta en iOS 10.

Importe la plataforma `User Notification`:

		#import <UserNotifications/UserNotifications.h>

En el método de delegado de aplicación `application:didFinishLaunchingWithOptions` reemplace:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

    		[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

por:

		if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
		{
			if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
			{
				[UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
			}else
			{
				[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
			}
			[application registerForRemoteNotifications];
		}
		else
		{
			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

#### Si ya tiene su propia implementación de UNUserNotificationCenterDelegate

El SDK tiene su propia implementación del protocolo UNUserNotificationCenterDelegate. Se usa para supervisar el ciclo de vida de las notificaciones de Engagement en dispositivos que se ejecutan en iOS 10 o superior. Si el SDK detecta que el delegado no usa su propia implementación porque puede que solo haya un delegado UNUserNotificationCenter por aplicación. Esto significa que tendrá que agregar la lógica de Engagement a su propio delegado.

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

<!---HONumber=AcomDC_0928_2016-->