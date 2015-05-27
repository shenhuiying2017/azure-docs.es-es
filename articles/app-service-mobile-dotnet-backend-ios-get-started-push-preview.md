<properties 
	pageTitle="Incorporación de notificaciones push a la aplicación iOS con el Servicio de aplicaciones de Azure" 
	description="Obtenga información acerca de cómo usar el Servicio de aplicaciones de Azure para enviar notificaciones push a su aplicación iOS." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	manager="dwrede"
	editor="" 
	authors="ysxu"/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article"
	ms.date="02/19/2015" 
	ms.author="yuaxu"/>


# Incorporación de notificaciones push a su aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Este tema muestra cómo usar el Servicio de aplicaciones de Azure para enviar notificaciones push a una aplicación iOS con el Servicio de notificaciones push de Apple (APNS). Cuando haya terminado, el back-end de .NET enviará una notificación push a la aplicación QuickStart ToDo cada vez que se inserte un registro. La aplicación es compatible con iOS 8 y versiones anteriores.

Este tutorial le guiará a través de estos pasos básicos para habilitar las notificaciones push:

1. [Generación de la solicitud de firma de certificado iOS]
2. [Registro de la aplicación y habilitación para las notificaciones push]
3. [Creación de un perfil de aprovisionamiento para la aplicación]
4. [Configuración del back-end móvil para enviar solicitudes de inserción]
5. [Actualización del servidor para enviar notificaciones push](#update-server)
6. [Publicación del back-end móvil en Azure]
7. [Incorporación de notificaciones push a la aplicación]
8. [Prueba de la aplicación]

Este tutorial requiere lo siguiente:

+ [SDK de iOS de Aplicaciones móviles de Azure]
+ [Nuget de la base de datos central de notificaciones de Azure]
+ [XCode 6.0][Install Xcode]
+ Dispositivo compatible con iOS 6.0 (o una versión posterior)
+ Pertenencia al programa para desarrolladores de iOS

   >[AZURE.NOTE]Debido a los requisitos de la configuración de las notificaciones push, debe implementar y realizar una prueba de las notificaciones de inserción en un dispositivo compatible con iOS (iPhone o iPad) en lugar de hacerlo en un emulador.

Este tutorial se basa en el inicio rápido de aplicaciones móviles del Servicio de aplicaciones. Antes de iniciar este tutorial, debe completar primero [Introducción a las aplicaciones móviles del Servicio de aplicaciones].

[AZURE.INCLUDE [Habilitación de notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

## Configuración de aplicaciones móviles para enviar solicitudes de inserción

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Actualización del servidor para enviar notificaciones push

1. En Visual Studio, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

2. Busque **Microsoft.Azure.NotificationHubs** y haga clic en **Instalar** para todos los proyectos de la solución.

3. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controladores** en el proyecto de back-end móvil. Abra TodoItemController.cs. Al principio del archivo, agregue las siguientes instrucciones `using`:

		using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Agregue el siguiente fragmento al método `PostTodoItem` después de la llamada **InsertAsync**:

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    Este código indica a la base de datos central de notificaciones asociada a esta aplicación móvil que envíe una notificación push después de la inserción de un elemento de tareas pendientes.


## <a name="publish-the-service"></a>Publicación del back-end móvil en Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Incorporación de notificaciones de inserción a la aplicación
1. Descargue y agregue la referencia al SDK de cliente de aplicaciones móviles del Servicio de aplicaciones en xcode.

2. En **QSAppDelegate.m**, agregue lo siguiente a **application:didFinishLaunchingWithOptions** para registrar el cliente con el Servicio de notificaciones push de Apple:

        // register iOS8 or previous devices for notifications
        if ([[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && 	
        	[[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)]) {
            [[UIApplication sharedApplication] registerForRemoteNotifications];
        } else {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
        }

3. En el mismo archivo, agregue el siguiente método de controlador dentro de la implementación **QSAppDelegate**:

        // registration with APNs is successful
        - (void)application:(UIApplication *)application 
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {

            // make sure you have imported "QSTodoService.h"
            QSTodoService *todoService = [QSTodoService defaultService];
            MSClient *client = todoService.client;

            [client.push registerDeviceToken:deviceToken completion:^(NSError *error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
            }];
        }

4. A continuación, agregue el método de control de errores dentro de la implementación:

        // handle any failure to register
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

5. En QSAppDelegate.m, agregue el siguiente método de controlador dentro de la implementación:

        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application 
              didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            
            NSDictionary *apsPayload = userInfo[@"aps"];
            NSString *alertString = apsPayload[@"alert"];
    
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" 
                                                            message:alertString 
                                                           delegate:nil 
                                                  cancelButtonTitle:@"OK" 
                                                  otherButtonTitles:nil];
            [alert show];
        }

Ahora su aplicación está actualizada para que sea compatible con las notificaciones push.

## Prueba de las notificaciones push en su aplicación

1. Presione el botón **Ejecutar** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, haga clic en **Aceptar** para aceptar las notificaciones push.

  	![][23]

    > [AZURE.NOTE]Debe aceptar de forma explícita las notificaciones push desde su aplicación. Esta solicitud solo se produce la primera vez que se ejecuta la aplicación.

2. En la aplicación, escriba un texto significativo, como _Una nueva tarea de Servicios móviles_ y, a continuación, haga clic en el icono del signo más (**+**).

  	![][24]

3. Compruebe que se ha recibido la notificación y, a continuación, haga clic en **Aceptar** para descartarla.

  	![][25]

4. Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente inserción.

  	![][26]

Ha completado correctamente este tutorial.

<!-- Anchors.  -->
[Generación de la solicitud de firma de certificado iOS]: #certificates
[Registro de la aplicación y habilitación para las notificaciones push]: #register
[Creación de un perfil de aprovisionamiento para la aplicación]: #profile
[Incorporación de notificaciones push a la aplicación]: #add-push
[Configuración del back-end móvil para enviar solicitudes de inserción]: #configure
[Update the server to send push notifications]: #update-server
[Publicación del back-end móvil en Azure]: #publish-mobile-service
[Prueba de la aplicación]: #test-the-service

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
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK de iOS de Aplicaciones móviles de Azure]: https://go.microsoft.com/fwLink/?LinkID=529823
[Nuget de la base de datos central de notificaciones de Azure]: https://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!--HONumber=54-->