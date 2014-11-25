<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/es-es/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Introducción a las notificaciones de inserción en Servicios móviles

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android">Android</a>
<!-- <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

Este tema muestra cómo puede usar Servicios móviles de Azure para enviar notificaciones de inserción a una aplicación de iOS. En este tutorial aprenderá a agregar notificaciones de inserción al proyecto de inicio rápido con el servicio de notificaciones de inserción de Apple (APNS). Cuando haya finalizado, el servicio móvil le enviará una notificación de inserción cada vez que se inserte un registro.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones de inserción:

1.  [Generación del archivo de solicitud de firma de certificado][Generación del archivo de solicitud de firma de certificado]
2.  [Registro de la aplicación y habilitación para las notificaciones de inserción][Registro de la aplicación y habilitación para las notificaciones de inserción]
3.  [Creación de un perfil de aprovisionamiento para la aplicación][Creación de un perfil de aprovisionamiento para la aplicación]
4.  [Descarga del servicio móvil de manera local][Descarga del servicio móvil de manera local]
5.  [Prueba del servicio móvil][Prueba del servicio móvil]
6.  [Actualización del servidor para enviar notificaciones de inserción][Actualización del servidor para enviar notificaciones de inserción]
7.  [Publicación del servicio móvil en Azure][Publicación del servicio móvil en Azure]
8.  [Incorporación de notificaciones de inserción a la aplicación][Incorporación de notificaciones de inserción a la aplicación]
9.  [Actualización de scripts para enviar notificaciones de inserción][Actualización de scripts para enviar notificaciones de inserción]
10. [Habilitación de notificaciones de inserción para pruebas locales][Habilitación de notificaciones de inserción para pruebas locales]
11. [Prueba de la aplicación con el servicio móvil publicado][Prueba de la aplicación con el servicio móvil publicado]

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

## <a name="download-the-service"></a><span class="short-header">Descarga del servicio</span>Descarga del servicio en el equipo local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">Prueba del servicio</span>Prueba del servicio móvil

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> Actualización del servidor para enviar notificaciones de inserción

1.  En el Explorador de soluciones de Visual Studio, muestre la carpeta **Controladores** en el proyecto de servicio móvil. Abra TodoItemController.cs. En la parte superior del archivo, agregue las siguientes instrucciones `using`:

        using System;
        using System.Collections.Generic;

2.  Actualice la definición de métoco `PostTodoItem` con el siguiente código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Este código enviará una notificación de inserción (con el texto del elemento insertado) tras insertar un elemento todo. En caso de error, el código agregará una entrada al registro de errores que aparecerá en la pestaña **Registros** del servicio móvil en el Portal de administración.

## <a name="publish-the-service"></a><span class="short-header">Publicación del servicio</span>Publicación del servicio móvil en Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

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

## <span id="local-testing"></span></a>Habilitación de notificaciones de inserción para pruebas locales

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## Pruebas de notificaciones de inserción en su aplicación

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **OK** para aceptar las notificaciones de inserción.

    ![][0]

    > [WACOM.NOTE] Debe aceptar de forma explícita las notificaciones de inserción desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el icono del signo más (**+**).

    ![][1]

3.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

    ![][2]

4.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente inserción.

    ![][3]

Ha completado correctamente este tutorial.

## Pasos siguientes

En este tutorial se muestran los conceptos básicos de la habilitación de una aplicación de iOS para que use Servicios móviles y Centros de notificaciones para enviar notificaciones de inserción. Para continuar, considere hacer el siguiente tutorial, [Envío de notificaciones de inserción a usuarios autenticados][Envío de notificaciones de inserción a usuarios autenticados], donde se muestra cómo usar etiquetas para enviar notificaciones de inserción desde un servicio móvil solo a un usuario autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Puede obtener más información acerca de los Servicios móviles y los Centros de notificaciones en los siguientes temas:

-   [Introducción a los datos][Introducción a los datos]

    Obtenga más información sobre cómo almacenar y consultar datos con los servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]

    Aprenda a autenticar a los usuarios de su aplicación con distintos tipos de cuentas con los servicios móviles.

-   [¿Qué son los Centros de notificaciones?][¿Qué son los Centros de notificaciones?]

    Aprenda cómo funcionan los Centros de notificaciones para entregar notificaciones a sus aplicaciones en todas las principales plataformas cliente.

<!-- Anchors.  --> 
 


  [Generación del archivo de solicitud de firma de certificado]: #certificates
  [Registro de la aplicación y habilitación para las notificaciones de inserción]: #register
  [Creación de un perfil de aprovisionamiento para la aplicación]: #profile
  [Descarga del servicio móvil de manera local]: #download-the-service-locally
  [Prueba del servicio móvil]: #test-the-service
  [Actualización del servidor para enviar notificaciones de inserción]: #update-server
  [Publicación del servicio móvil en Azure]: #publish-mobile-service
  [Incorporación de notificaciones de inserción a la aplicación]: #add-push
  [Actualización de scripts para enviar notificaciones de inserción]: #update-scripts
  [Habilitación de notificaciones de inserción para pruebas locales]: #local-testing
  [Prueba de la aplicación con el servicio móvil publicado]: #test-app
  [SDK de iOS para Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4,5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Introducción a los Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started
  [0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [Envío de notificaciones de inserción a usuarios autenticados]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
