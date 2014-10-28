<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Introducción a las notificaciones de inserción en Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# para Tienda Windows">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE] Este tema admite *servicios móviles* existentes que *todavía no se hayan actualizado para usar* la integración con Centros de notificaciones. Al crear un *servicio móvil* nuevo, esta funcionalidad integrada se habilita automáticamente. Para servicios móviles nuevos, consulte [Introducción a las notificaciones de inserción][].
>
> La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. *Debería actualizar los servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible*. Una vez que haya realizado la actualización, consulte esta versión de [Introducción a las notificaciones de inserción][].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado][]
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción][]
3.  [Creación de un perfil de aprovisionamiento para la aplicación][]
4.  [Configuración de Servicios móviles][]
5.  [Incorporación de notificaciones de inserción a la aplicación][]
6.  [Actualización de scripts para enviar notificaciones de inserción][]
7.  [Inserción de datos para recibir notificaciones][]

Este tutorial requiere lo siguiente:

-   [SDK de iOS para Servicios móviles][]
-   [Xcode 4.5][]
-   Dispositivo compatible con iOS 5.0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS

> [WACOM.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][].

[WACOM.INCLUDE [Habilitar notificaciones de inserción de Apple][]]

## Configuración de Servicios móviles para enviar solicitudes de inserción

[WACOM.INCLUDE [mobile-services-apns-configure-push][]]

## Incorporación de notificaciones de inserción a la aplicación

1.  En Xcode, abra el archivo QSAppDelegate.h y agregue la siguiente propiedad debajo de la propiedad \***window**:

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] Cuando su servicio móvil tenga habilitado el esquema dinámico, se agregará automáticamente una nueva columna 'deviceToken' a la tabla **TodoItem** cuando se inserte un elemento nuevo que contenga esta propiedad.

2.  En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  En QSTodoListViewController.m, importe el archivo QSAppDelegate.h para poder usar la delegación y obtener el token del dispositivo:

        #import "QSAppDelegate.h"

7.  En QSTodoListViewController.m, modifique la acción **(IBAction)onAdd** buscando la siguiente línea:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

Reemplace esto por el siguiente código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

## Actualización del script de inserción registrado en el Portal de administración

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![][]

2.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][1]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    De esta forma, se registra un nuevo script de inserción, que usa [apns object][] para enviar una notificación de inserción (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.

    > [WACOM.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.

## Pruebas de notificaciones de inserción en su aplicación

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

    ![][2]

    > [WACOM.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el icono del signo más (**+**).

    ![][3]

3.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

    ![][4]

4.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

    ![][5]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este ejemplo simple, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que APNS utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación][], creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-ios-get-started-push/ "Back-end de JavaScript"
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Generación del archivo de solicitud de firma de certificado]: #certificates
  [Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
  [Creación de un perfil de aprovisionamiento para la aplicación]: #profile
  [Configuración de Servicios móviles]: #configure
  [Incorporación de notificaciones de inserción a la aplicación]: #add-push
  [Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
  [Inserción de datos para recibir notificaciones]: #test
  [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
  [Habilitar notificaciones de inserción de Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Notificaciones de inserción para usuarios de la aplicación]: /es-es/develop/mobile/tutorials/push-notifications-to-users-ios
