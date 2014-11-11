<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Notificaciones de inserción para usuarios mediante Servicios móviles

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS" class="current">iOS</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

Este tema amplía el [tutorial de notificaciones de inserción anterior][tutorial de notificaciones de inserción anterior] mediante la incorporación de una nueva tabla para almacenar tokens del servicio de notificaciones de inserción de Apple (APNS). Estos tokens pueden usarse para enviar notificaciones de inserción a los usuarios de la aplicación para iPhone e iPad.

Este tutorial le guiará a través de estos pasos en la actualización de las notificaciones de inserción en su aplicación:

1.  [Creación de la tabla Devices][Creación de la tabla Devices]
2.  [Actualización de la aplicación][Actualización de la aplicación]
3.  [Actualización de los scripts de servidor][Actualización de los scripts de servidor]
4.  [Pruebas de notificaciones de inserción en su aplicación][Pruebas de notificaciones de inserción en su aplicación]

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior]. Antes de comenzar este tutorial, primero debe completar [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior].

## <a name="create-table"></a>

## <span class="short-header">Creación de la tabla</span>Creación de la nueva tabla Devices

</h2>
1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, en **Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Devices* en **Table name** y, a continuación, haga clic en el botón de comprobación.

    ![][2]

    Esto crea la tabla **Devices**, la cual almacena los URI de canal que se usan para enviar notificaciones de inserción de manera independiente de los datos de elementos.

A continuación, modificará la aplicación de notificaciones de inserción para almacenar datos en esta nueva tabla en lugar de en la tabla **TodoItem**.

## <a name="update-app"></a>Actualización de la aplicación

1.  En Xcode, abra el archivo QSTodoService.h y agregue la siguiente declaración de método:

        // Declare method to register device token for other users
        - (void)registerDeviceToken:(NSString *)deviceToken;

    De esta forma, se habilita App Delegate para registrar deviceToken con el Servicio móvil.

2.  En QSTodoService.m, agregue el siguiente método de instancia:

        // Instance method to register deviceToken in Devices table.
        // Called in AppDelegate.m when APNS registration succeeds.
        - (void)registerDeviceToken:(NSString *)deviceToken
        {
            MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
            NSDictionary *device = @{ @"deviceToken" : deviceToken };

            // Insert the item into the devices table and add to the items array on completion
            [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                if (error) {
                    NSLog(@"ERROR %@", error);
                }
            }];
        }

    Esto permite a otros autores de llamada registrar el token del dispositivo con Servicios móviles.

3.  En el archivo QSAppDelegate.m, agregue la siguiente instrucción de importación:

        #import "QSTodoService.h"

    Este código hace que AppDelegate tenga en cuenta la implementación de TodoService.

4.  En QSAppDelegate.m, reemplace el método **didRegisterForRemoteNotificationsWithDeviceToken** por el siguiente código:

        // We have registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {

           // Register the APNS deviceToken with the Mobile Service Devices table.
           NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
           NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];

           QSTodoService *instance = [QSTodoService defaultService];
           [instance registerDeviceToken:token];
        }

5.  En QSTodoListViewController.m, ubique el método **(IBAction)onAdd** y *quite* el siguiente código:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    Reemplace esto por el siguiente código:

        // We removed the delegate; this application no longer passes the deviceToken here.
        // Remove the device token from the payload
        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

La aplicación se encuentra ahora actualizada para usar la nueva tabla Devices y almacenar los tokens del dispositivo que se usan para enviar notificaciones de inserción de nuevo al dispositivo.

## <a name="update-scripts"></a>Actualización de los scripts de servidor

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **Devices**.

    ![][3]

2.  En **devices**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][4]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **Devices**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
           var devicesTable = tables.getTable('Devices');
           devicesTable.where({
               token: item.token
           }).read({
               success: insertTokenIfNotFound
           });

           function insertTokenIfNotFound(existingTokens) {
               if (existingTokens.length > 0) {
                   request.respond(200, existingTokens[0]);
               } else {
                   request.execute();
               }
           }
        }

    Este script comprueba la tabla **Devices** por si existiera un dispositivo con el mismo token. La inserción solo se lleva a cabo si no se encuentra ningún dispositivo que coincida. De esta forma, se evita la duplicación de registros en el dispositivo.

4.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

    ![][5]

5.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
          request.execute({
              success: function() {
                  request.respond();
                  sendNotifications();
              }
          });

          function sendNotifications() {
              var devicesTable = tables.getTable('Devices');
              devicesTable.read({
                  success: function(devices) {
                      // Set timeout to delay the notifications, 
                      // to provide time for the app to be closed 
                      // on the device to demonstrate toast notifications.
                      setTimeout(function() {
                          devices.forEach(function(device) {

                              push.apns.send(device.deviceToken, {
                                  alert: "Toast: " + item.text,
                                  payload: {
                                      inAppMessage: 
                                      "Hey, a new item arrived: '" + 
                                      item.text + "'"
                                  }
                              });
                          });
                      }, 2500);
                  }
              });
          }

    }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los dispositivos almacenados en la tabla **Devices**.

## <a name="test"></a><span class="short-header">Prueba de la aplicación</span>Pruebas de notificaciones de inserción en su aplicación

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, escriba un texto significativo en la aplicación, como *Nueva tarea de Servicios móviles*, y haga clic en el icono de suma (**+**).

    ![][6]

2.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

    ![][7]

3.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

    ![][8]

Ha completado correctamente este tutorial.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de las notificaciones de inserción. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    
	Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    
	Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    
	Obtenga más información acerca del registro y uso de scripts de servidor.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [tutorial de notificaciones de inserción anterior]: /es-es/develop/mobile/tutorials/get-started-with-push-ios
  [Creación de la tabla Devices]: #create-table
  [Actualización de la aplicación]: #update-app
  [Actualización de los scripts de servidor]: #update-scripts
  [Pruebas de notificaciones de inserción en su aplicación]: #test-app
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png
  [3]: ./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png
  [4]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png
  [5]: ./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png
  [7]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png
  [8]: ./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-ios
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-ios
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
