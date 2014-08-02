<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="" solutions="" manager="" editor="" />

Notificaciones de inserción para usuarios mediante Servicios móviles
====================================================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Este tema amplía el [tutorial de notificaciones de inserción anterior](/en-us/develop/mobile/tutorials/get-started-with-push-ios) mediante la incorporación de una nueva tabla para almacenar tokens del servicio de notificaciones de inserción de Apple (APNS). Estos tokens pueden usarse para enviar notificaciones de inserción a los usuarios de la aplicación para iPhone e iPad.

Este tutorial le guiará a través de estos pasos en la actualización de las notificaciones de inserción en su aplicación:

1.  [Creación de la tabla Devices](#create-table)
2.  [Actualización de la aplicación](#update-app)
3.  [Actualización de los scripts de servidor](#update-scripts)
4.  [Pruebas de notificaciones de inserción en su aplicación](#test-app)

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-ios). Antes de comenzar este tutorial, primero debe completar [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-ios).

## <a name="create-table"></a><h2><span class="short-header">Creación de la tabla</span>Creación de una tabla Devices</h2>


1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png)

2.  Haga clic en la pestańa **Data** y, a continuación, en **Create**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png)

     Se muestra el cuadro de diálogo **Crear nueva tabla**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Devices* en **Table name** y, a continuación, haga clic en el botón de comprobación.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png)

   Esto crea la tabla **Devices**, la cual almacena los URI de canal que se usan para enviar notificaciones de inserción de manera independiente de los datos de elementos.

A continuación, modificará la aplicación de notificaciones de inserción para almacenar datos en esta nueva tabla en lugar de en la tabla **TodoItem**.

Actualización de la aplicación
------------------------------

1.  En Xcode, abra el archivo QSTodoService.h y agregue la siguiente declaración de método:

         // Declare el método para registrar el token del dispositivo para otros usuarios.
         - (void)registerDeviceToken:(NSString *)deviceToken;

    De esta forma, se habilita App Delegate para registrar deviceToken con el Servicio móvil.

2.  En QSTodoService.m, agregue el siguiente método de instancia:

         // Método de instancia para registrar deviceToken en la tabla Devices.
         // Se realiza la llamada en AppDelegate.m cuando el registro de APNS se realiza correctamente.
         - (void)registerDeviceToken:(NSString *)deviceToken
         {
             MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
             NSDictionary *device = @{ @"deviceToken" : deviceToken };

             // Inserte el elemento en la tabla Devices y agregue la matriz de elementos en la finalización.
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

         // Ya ha realizado el registro. Ahora registre el token del dispositivo (como una cadena) en la instancia AppDelegate
         // teniendo en cuenta que debe eliminar primero los corchetes angulares.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {

            // Registre el APNS deviceToken con la tabla Devices de Servicios móviles.
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
                
            QSTodoService *instance = [QSTodoService defaultService];
            [instance registerDeviceToken:token];
         }

5.  En QSTodoListViewController.m, ubique el método **(IBAction)onAdd** y *quite* el siguiente código:

         // Obtenga una referencia a AppDelegate para recuperar fácilmente deviceToken.
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // Agregue la propiedad del token del dispositivo a la carga de elementos todo.
             @"deviceToken" : delegate.deviceToken
         };
         
    Reemplace esto por el siguiente código:

         // Hemos quitado la delegación; esta aplicación ya no pasa deviceToken aquí.
         // Quite el token del dispositivo de la carga.
         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

La aplicación se encuentra ahora actualizada para usar la nueva tabla Devices y almacenar los tokens del dispositivo que se usan para enviar notificaciones de inserción de nuevo al dispositivo.

Actualización de los scripts de servidor
----------------------------------------

1.  En el Portal de administración, haga clic en la pestańa **Data** y, a continuación, en la tabla **Devices**.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png)

2.  En **devices**, haga clic en la pestańa **Script** y seleccione **Insert**.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png)

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

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png)

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
                       // Establezca el tiempo de espera para retrasar las notificaciones, 
                       // proporcionar tiempo para que se cierre la aplicación 
                       // en el dispositivo y que se muestren las notificaciones del sistema.
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

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

1.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en un dispositivo compatible con iOS. A continuación, escriba un texto significativo en la aplicación, como *Nueva tarea de Servicios móviles*, y haga clic en el icono de suma (**+**).

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png)

1.  Compruebe que se ha recibido la notificación y, a continuación, haga clic en **OK** para descartarla.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png)

1.  Repita el paso 2 y cierre de inmediato la aplicación. A continuación, compruebe que se muestra la siguiente notificación.

    ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png)

Ha completado correctamente este tutorial.

Pasos siguientes
----------------

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de las notificaciones de inserción. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-ios)
    
    Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    Obtenga más información acerca del registro y uso de scripts de servidor.


