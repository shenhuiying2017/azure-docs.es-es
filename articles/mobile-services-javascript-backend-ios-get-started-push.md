<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Introducción a las notificaciones de inserción en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a><!---<a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end de .NET" >Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

> [WACOM.NOTE]En este tutorial se muestra la integración de Servicios móviles con Centros de notificaciones, que es como se envían notificaciones de inserción desde un servicio móvil. Si utiliza un servicio móvil anterior que usa una función de inserción heredada y aún no está actualizado para usar Centros de notificaciones, *es recomendable que lo actualice* como parte de este tutorial. Si opta por no actualizarlo ahora, debe seguir esta versión del tutorial: [Introducción a las notificaciones de inserción (heredadas)][Introducción a las notificaciones de inserción (heredadas)].

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado][Generación del archivo de solicitud de firma de certificado]
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción][Registro de la aplicación y habilitación para las notificaciones de inserción]
3.  [Creación de un perfil de aprovisionamiento para la aplicación][Creación de un perfil de aprovisionamiento para la aplicación]
4.  [Configuración de Servicios móviles][Configuración de Servicios móviles]
5.  [Incorporación de notificaciones de inserción a la aplicación][Incorporación de notificaciones de inserción a la aplicación]
6.  [Actualización de scripts para enviar notificaciones de inserción][Actualización de scripts para enviar notificaciones de inserción]
7.  [Inserción de datos para recibir notificaciones][Inserción de datos para recibir notificaciones]

Este tutorial requiere lo siguiente:

-   [SDK de iOS para Servicios móviles][SDK de iOS para Servicios móviles]
-   [XCode 4,5][XCode 4,5]
-   Dispositivo compatible con iOS 6,0 (o una versión posterior)
-   Pertenencia al programa para desarrolladores de iOS

> [WACOM.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

[WACOM.INCLUDE [Habilitación de notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

## Configuración de Servicios móviles para enviar solicitudes de inserción

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Incorporación de notificaciones de inserción a la aplicación

1.  En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  En QSTodoListViewController.m, importe el archivo QSAppDelegate.h para poder usar la delegación y obtener el token del dispositivo:

        #import "QSAppDelegate.h"

6.  En QSTodoListViewController.m, modifique la acción **(IBAction)onAdd** buscando la siguiente línea:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

   Reemplace esto por el siguiente código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

   De esta forma, se agrega una referencia a **QSAppDelegate** para obtener el token del dispositivo y después se modifica la carga de la solicitud para incluir ese token.

   > [WACOM.NOTE] Debe agregar este código antes de la llamada al método **addItem**.

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

## Actualización del script de inserción registrado en el Portal de administración

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **TodoItem**.

    ![][0]

2.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][1]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    Esto registra un nuevo script de inserción, que usa el [objeto apns][objeto apns] para enviar una notificación de inserción (texto insertado) al dispositivo indicado en la solicitud de inserción.

    > [WACOM.NOTE] Este script retrasa el envío de la notificación con el fin de proporcionarle tiempo para cerrar la aplicación y recibir una notificación de inserción.

## Pruebas de notificaciones de inserción en su aplicación

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

    ![][2]

    > [WACOM.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el icono del signo más (**+**).

    ![][3]

3.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

    ![][4]

4.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente inserción.

    ![][5]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de iOS para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. Después, considere hacer alguno de los siguientes tutoriales:

-   [Envío de notificaciones de inserción a usuarios autenticados][Envío de notificaciones de inserción a usuarios autenticados]

    Vea cómo usar etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

-   [Envío de notificaciones a suscriptores][Envío de notificaciones a suscriptores]

    Vea cómo pueden registrarse los usuarios y recibir notificaciones de inserción para categorías que les interesan.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    Encontrará más información acerca de Servicios móviles y Centros de notificaciones en los temas siguientes:

-   [Introducción a los datos][Introducción a los datos]

    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]

    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]

    Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

-   [Documentación conceptual sobre Objective-C en Servicios móviles][Documentación conceptual sobre Objective-C en Servicios móviles]

    Más información sobre el uso de Servicios móviles con Objective-C y iOS.

-   [Documentación de referencia sobre scripts de servidor para Servicios móviles][Documentación de referencia sobre scripts de servidor para Servicios móviles]

    Más información sobre el modo de implementar lógica empresarial en su servicio móvil.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs.   -->

  [Generación del archivo de solicitud de firma de certificado]: #certificates
  [Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
  [Creación de un perfil de aprovisionamiento para la aplicación]: #profile
  [Configuración de Servicios móviles]: #configure
  [Incorporación de notificaciones de inserción a la aplicación]: #add-push
  [Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
  [Inserción de datos para recibir notificaciones]: #test
  [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started
  [Habilitación de notificaciones de inserción de Apple]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [objeto apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [Envío de notificaciones a suscriptores]: /es-es/documentation/articles/notification-hubs-ios-send-breaking-news/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users
  [Documentación conceptual sobre Objective-C en Servicios móviles]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Documentación de referencia sobre scripts de servidor para Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
