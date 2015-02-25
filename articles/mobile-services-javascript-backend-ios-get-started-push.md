<properties pageTitle="Introducción a las notificaciones de inserción (iOS) | Centro de desarrollo móvil" description="Obtenga información acerca de cómo usar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación iOS." services="mobile-services, notification-hubs" documentationCenter="ios" manager="dwrede" editor="" authors="krisragh"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="12/15/2014" ms.author="krisragh"/>

# Incorporación de notificaciones de inserción a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a través del servicio de notificaciones de inserción de Apple (APNS) a una aplicación de iOS. Este tutorial le permite habilitar notificaciones de inserción con los Centros de notificaciones de Azure para el [proyecto de inicio rápido](http://azure.microsoft.com/es-es/documentation/articles/mobile-services-ios-get-started/). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Generación del archivo de solicitud de firma de certificado](#certificates)
2. [Registro de la aplicación y habilitación para las notificaciones de inserción](#register)
3. [Creación de un perfil de aprovisionamiento para la aplicación](#profile)
4. [Configuración de Servicios móviles](#configure)
5. [Incorporación de notificaciones de inserción a la aplicación](#add-push)
6. [Actualización de scripts para enviar notificaciones de inserción](#update-scripts)
7. [Inserción de datos para recibir notificaciones](#test)

Este tutorial requiere lo siguiente:

+ [SDK de iOS para Servicios móviles]
+ [XCode 4.5][Instalación de Xcode]
+ Dispositivo compatible con iOS 6.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   > [AZURE.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de empezar este tutorial, primero debe completar [Introducción a Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente][Introducción a los datos].


[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]


## <a id="configure"></a>Configuración de servicios móviles para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## <a id="update-scripts"></a>Actualización del script de inserción registrado en el Portal de administración

1. En el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**.

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

   	De esta forma, se registra un nuevo script de inserción, que usa el [objeto apns] para enviar una notificación de inserción (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.


   	> [AZURE.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación de inserción.

## <a id="add-push"></a>Incorporación de notificaciones de inserción a la aplicación

1. En QSAppDelegate.m, inserte el fragmento siguiente para importar el SDK de iOS para Servicios móviles:

        #import <MicrosoftAzureMobileServices/MicrosoftAzureMobileServices.h>

2. En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. In QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación. Asegúrese de copiar los valores de dirección URL y de clave de aplicación del servicio móvil y cambiarlos para los marcadores de posición:

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

## <a id="test"></a>Prueba de las notificaciones de inserción en su aplicación

1. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** (Aceptar) para aceptar las notificaciones de inserción.

  	![][23]

    > [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba texto significativo, como _A new Mobile Services task_ y, a continuación, haga clic en el icono del signo más (**+**).

  	![][24]

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** (Aceptar) para descartarla.

  	![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente inserción.

  	![][26]

Ha completado correctamente este tutorial.

## <a id="next-steps"></a>Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de iOS para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. A continuación, considere completar uno de los siguientes tutoriales:

+ [Envío de notificaciones de inserción a usuarios autenticados]
	<br/>Aprenda a usar las etiquetas para enviar notificaciones de inserción desde un servicio móvil a solo un usuario autenticado.

+ [Envío de notificaciones de difusión a los suscriptores]
	<br/>Sepa cómo los usuarios se pueden registrar y recibir notificaciones de inserción de categorías que les interesan.
<!---
+ [Envío de notificaciones basadas en plantillas a los suscriptores]
	<br/>Obtenga información sobre cómo usar las plantillas para enviar notificaciones de inserción desde un servicio móvil sin tener que diseñar cargas específicas de plataforma en el back-end.
-->
Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

* [Introducción a los datos]
  <br/>Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

* [Introducción a la autenticación]
  <br/>Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los Servicios móviles.

* [¿Qué son los Centros de notificaciones?]
  <br/>Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

* [Depuración de aplicaciones de los Centros de notificaciones](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenga orientación para solucionar y depurar las soluciones de Centros de notificaciones. 

* [Referencia conceptual de Servicios móviles de Objective-C]
  <br/>Obtenga más información sobre el uso de Servicios móviles con Objective-C y iOS.

* [Referencia del script de servidor de Servicios móviles]
  <br/>Obtenga más información sobre cómo implementar lógica empresarial al servicio móvil.

<!-- Anchors. -->


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
[Instalar Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificación de inserción de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a los servicios móviles]: /es-es/documentation/articles/mobile-services-ios-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-ios-get-started-data
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users
[Portal de administración de Azure]: https://manage.windowsazure.com/
[apns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=272333

[Referencia del script de servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293

[Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/

[¿Qué son los Centros de notificaciones?]: /es-es/documentation/articles/notification-hubs-overview/
[Envío de notificaciones de difusión a los suscriptores]: /es-es/documentation/articles/notification-hubs-ios-send-breaking-news/
[Envío de notificaciones basadas en plantillas a los suscriptores]: /es-es/documentation/articles/notification-hubs-ios-send-localized-breaking-news/

[Referencia conceptual de Servicios móviles de Objective-C]: /es-es/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


<!--HONumber=42-->
