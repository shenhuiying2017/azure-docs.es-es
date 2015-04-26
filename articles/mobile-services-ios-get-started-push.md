<properties 
	pageTitle="Introducción a las notificaciones de inserción (iOS) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo utilizar Servicios móviles de Azure para enviar notificaciones de inserción a su aplicación de iOS (inserción heredada)." 
	services="mobile-services" 
	documentationCenter="ios" 
	manager="dwrede" 
	editor="" 
	authors="krisragh"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Incorporación de notificaciones de inserción a su aplicación de Servicios móviles (inserción heredada)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
    <a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/es-es/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/es-es/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">Back-end de JavaScript</a></div>

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.


>[AZURE.NOTE]Este tema admite Servicios móviles <em>existentes</em> que <em>todavía no se han actualizado</em> para usar la integración de Centros de notificaciones. Cuando crea un <em>nuevo</em> servicio móvil, esta funcionalidad integrada se habilita automáticamente. Para los nuevos Servicios móviles, consulte [Introducción a las notificaciones de inserción](/ es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).
>
>La solución Servicios móviles se integra con Centros de notificaciones de Azure para ofrecer compatibilidad con la funcionalidad de notificación de inserción adicional, como plantillas, varias plataformas y escala. <em>Debería actualizar los Servicios móviles existentes para que usen Centros de notificaciones siempre que sea posible.</em> Una vez que haya actualizado, vea esta versión de [Introducción a las notificaciones de inserción](/ es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/).

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1. [Generación del archivo de solicitud de firma de certificado]
2. [Registro de la aplicación y habilitación para las notificaciones de inserción]
3. [Creación de un perfil de aprovisionamiento para la aplicación]
3. [Configuración de Servicios móviles]
4. [Incorporación de notificaciones de inserción a la aplicación]
5. [Actualización de scripts para enviar notificaciones de inserción]
6. [Inserción de datos para recibir notificaciones]

Este tutorial requiere lo siguiente:

+ [SDK de iOS para Servicios móviles]
+ [Xcode 4.5][Instalación de Xcode]
+ Dispositivo compatible con iOS 5.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   > [AZURE.NOTE] Debido a los requisitos de la configuración de las notificaciones de inserción, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial está basado en el inicio rápido de Servicios móviles. Antes de comenzar este tutorial, primero debe completar [Introducción a los Servicios móviles].

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## Configuración de Servicios móviles para enviar solicitudes de inserción

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## Incorporación de notificaciones de inserción a la aplicación

1. En Xcode, abra el archivo QSAppDelegate.h y agregue la siguiente propiedad debajo de la propiedad ***window***:

        @property (strong, nonatomic) NSString *deviceToken;

    > [AZURE.NOTE] Si su servicio móvil tiene habilitado el esquema dinámico, se agregará automáticamente una nueva columna  'deviceToken' a la tabla **TodoItem** cuando se inserte un elemento nuevo que contenga esta propiedad.

2. En QSAppDelegate.m, reemplace el siguiente método de controlador dentro de la implementación:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:  

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

5. En QSTodoListViewController.m, importe el archivo QSAppDelegate.h para poder usar la delegación y obtener el token del dispositivo:

        #import "QSAppDelegate.h"

6. En QSTodoListViewController.m, modifique la acción **(IBAction)onAdd** buscando la siguiente línea:

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

   Replace this with the following code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	De esta forma, se agrega una referencia a **QSAppDelegate** para obtener el token del dispositivo y después se modifica la carga de la solicitud para incluir ese token.

   	> [AZURE.NOTE] Debe agregar este código antes de la llamada al método <strong>addItem</strong>.

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción.

## Actualización del script de inserción registrado en el Portal de administración

1. En el Portal de administración, haga clic en la pestaña **Datos** y, a continuación, en la tabla **TodoItem**.

   	![][21]

2. En **todoItem**, haga clic en la pestaña **Script** y seleccione **Insertar**.

  	![][22]

   	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

3. Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

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

   	De esta forma, se registra un nuevo script de inserción, que usa el [objeto apns] para enviar una notificación de inserción (el texto insertado) al dispositivo proporcionado en la solicitud de inserción.


   	> [AZURE.NOTE] Este script retrasa el envío de la notificación para proporcionarle tiempo para cerrar la aplicación y recibir una notificación del sistema.

## Prueba de las notificaciones de inserción en su aplicación

1. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** (Aceptar) para aceptar las notificaciones de inserción.

  	![][23]

    > [AZURE.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba texto significativo, como _A new Mobile Services task_ y, a continuación, haga clic en el icono del signo más (**+**).

  	![][24]

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** (Aceptar) para descartarla.

  	![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

  	![][26]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este sencillo ejemplo, un usuario recibe una notificación de inserción con los datos que se acaban de insertar. El cliente en la solicitud suministra al servicio móvil el token de dispositivo que APNS utiliza. En el siguiente tutorial, [Notificaciones de inserción para usuarios de la aplicación], creará una tabla de dispositivos independiente en la que puede almacenar tokens de dispositivos y enviar una notificación de inserción a todos los canales almacenados cuando se produce una inserción.

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

<!-- URLs. -->
[Instalar Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Portal de aprovisionamiento de iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Servicio de notificación de inserción de Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-ios
[Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
[Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
[Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
[Notificaciones de inserción a los usuarios de aplicaciones]: /es-es/develop/mobile/tutorials/push-notifications-to-users-ios
[Autorización de usuarios con scripts]: /es-es/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Portal de administración de Azure]: https://manage.windowsazure.com/
[apns, objeto]: http://go.microsoft.com/fwlink/p/?LinkId=272333



<!--HONumber=42-->
