<properties urlDisplayName="Get Started with Push (iOS)" pageTitle="Introducción a las notificaciones de inserción (iOS) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a través del servicio de notificaciones de inserción de Apple (APNS) a una aplicación iOS. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el [proyecto de inicio rápido](http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started/). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Generación del archivo de solicitud de firma de certificado]
2. [Registro de la aplicación y habilitación para las notificaciones de inserción]
3. [Creación de un perfil de aprovisionamiento para la aplicación]
4. [Configuración de Servicios móviles]
5. [Incorporación de notificaciones de inserción a la aplicación]
6. [Actualización de scripts para enviar notificaciones de inserción]
7. [Inserción de datos para recibir notificaciones]

Este tutorial requiere lo siguiente:

+ [SDK de iOS para Servicios móviles]
+ [XCode 4.5][Instalación de Xcode]
+ Dispositivo compatible con iOS 6,0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   > [WACOM.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, debe haber completado el tutorial [Introducción a Servicios móviles].


[WACOM.INCLUDE [Habilite las notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]


## Configuración de Servicios móviles para enviar solicitudes de inserción

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Incorporación de notificaciones de inserción a la aplicación

1. En QSAppDelegate.m, inserte el fragmento siguiente para importar el SDK de iOS para servicios móviles:

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

2. En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación. Asegúrese de copiar los valores de dirección URL del servicio móvil y clave de la aplicación para reemplazar los marcadores de posición:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            // TODO: update @"MobileServiceUrl" and @"AppKey" placeholders
			MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"];

            [client.push registerNativeWithDeviceToken:deviceToken tags:@[@"uniqueTag"] completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Handle any failure to register.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:  

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

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

## Actualización del script de inserción registrado en el Portal de administración

1. En el Portal de administración, haga clic en la pestaña **Datos** y elija la tabla **TodoItem**.

   	![][21]

2. En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

  	![][22]

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

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

   	Esto registra un nuevo script de inserción que usa el [objeto apns] para enviar una notificación de inserción (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.


   	> [WACOM.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación de inserción.

## Probar las notificaciones de inserción en su aplicación

1. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

  	![][23]

    > [WACOM.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba un texto significativo, como _Nueva tarea de Servicios móviles_ y haga clic en el icono (**+**).

  	![][24]

3. 	Compruebe que se ha recibido la notificación y haga clic en **Aceptar** para descartarla.

  	![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente inserción.

  	![][26]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de iOS para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. A continuación, considere completar uno de los siguientes tutoriales:

+ [Envío de notificaciones de inserción a usuarios autenticados]
	<br/>Aprenda a utilizar etiquetas para enviar notificaciones de inserción desde un servicio móvil a solo un usuario autenticado.

+ [Envío de notificaciones de difusión a suscriptores]
	<br/>Información acerca de cómo los usuarios pueden registrar y recibir notificaciones de inserción para las categorías que les interesan.
<!---
+ [Send template-based notifications to subscribers]
	<br/>Información sobre cómo utilizar las plantillas para enviar notificaciones de inserción de un servicio móvil, sin necesidad de cargas de trabajo específicas de la plataforma en el back-end.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Más información sobre cómo almacenar y consultar datos con servicios móviles.

* [Introducción a la autenticación]
  <br/>Vea cómo autenticar a los usuarios de su aplicación con diferentes tipos de cuenta con servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en las principales plataformas de cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga información orientativa para solucionar y depurar soluciones de Centros de notificaciones. 

* [Documentación de referencia conceptual de Servicios móviles con Objective-C]
  <br/>Más información acerca de cómo utilizar Servicios móviles con Objective-C y iOS.

* [Referencia del script del servidor de Servicios móviles]
  <br/>Más información acerca de cómo implementar la lógica empresarial en el servicio móvil.

<!-- Anchors. -->
[Generación del archivo de solicitud de firma de certificado]: #certificates
[Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
[Creación de un perfil de aprovisionamiento para la aplicación]: #profile
[Configuración de Servicios móviles]: #configure
[Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
[Incorporación de notificaciones de inserción a la aplicación]: #add-push
[Inserción de datos para recibir notificaciones]: #test
[Pasos siguientes]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs.   -->
[Instalación de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificaciones de inserción de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users
[Portal de administración de Azure]: https://manage.windowsazure.com/
[apns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a suscriptores]: /es-es/documentation/articles/notification-hubs-ios-send-breaking-news/
[Envío de notificaciones basadas en plantillas a suscriptores]: /es-es/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Documentación de referencia conceptual de Servicios móviles con Objective-C]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
