<properties linkid="" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

Notificaciones de inserción para usuarios mediante Servicios móviles
====================================================================

[Windows Phone](/es-es/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/es-es/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/es-es/develop/mobile/tutorials/push-notifications-to-users-android "Android")

Este tema amplía el [tutorial de notificaciones de inserción anterior](/es-es/develop/mobile/tutorials/get-started-with-push-android) mediante la incorporación de una nueva tabla a los URI de registro del servicio de mensajería en la nube de Google (GCM), que puede usarse para enviar notificaciones de inserción a varios usuarios de la aplicación de Android. En este tutorial, una actualización única generará notificaciones de inserción para todos los dispositivos registrados cuando las inserciones se realicen en la tabla ToDoList. En el tutorial anterior, se envió una notificación solo al dispositivo realizando la inserción.

Este tutorial le guiará a través de estos pasos en la actualización de las notificaciones de inserción en su aplicación:

1.  [Creación de la tabla Registration](#create-table)
2.  [Actualización de la aplicación](#update-app)
3.  [Actualización de los scripts de servidor](#update-scripts)
4.  [Pruebas de notificaciones de inserción en su aplicación](#test-app)

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-android). Antes de comenzar este tutorial, primero debe completar [Introducción a las notificaciones de inserción](/es-es/develop/mobile/tutorials/get-started-with-push-android).

Creación de una tabla
---------------------

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

  ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, en **Create**.

  ![][1]

  Se muestra el cuadro de diálogo **Create new table**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Registration* en **Table name** y, a continuación, haga clic en el botón de comprobación.

  ![][2]

Esto crea la tabla **Registration**, la cual almacena los URI de registro que se usan para enviar notificaciones de inserción de manera independiente de los datos de elementos.

A continuación, modificará la aplicación de notificaciones de inserción para almacenar datos de registro en esta nueva tabla en lugar de en la tabla **TodoItem**.

Actualización de la aplicación
------------------------------

1.  En Eclipse, en el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y, a continuación, en **Clase**.

2.  En **Name**, escriba `Registration` y, a continuación, haga clic en **Finish**.

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png)

    Con esto se crea la clase Registration.

3.  Abra el archivo ToDoItem.java y corte el siguiente código:

         @com.google.gson.annotations.SerializedName("handle")
         private String mHandle;

         public String getHandle() {
             return mHandle;
         }

         public final void setHandle(String handle) {
             mHandle = handle;
         }

4.  Pegue el código que cortó en el paso anterior en el cuerpo de la clase **Registration** que creó anteriormente.

5.  Agregue el siguiente código a la clase **Registration**:

         @com.google.gson.annotations.SerializedName("id")
         private int mId;

         /**
          * Devuelve el identificador del elemento.
          */
         public int getId() {
             return mId;
         }

         /**
          * Establece el identificador del elemento.
          * 
          * @param id : id to set
          */
         public final void setId(int id) {
             mId = id;
         }

6.  Abra el archivo **ToDoActivity.java** y, en el método `addItem`, elimine las siguientes líneas:

        item.setHandle(MyHandler.getHandle());

7.  Busque la propiedad `mClient` y reemplácela por el siguiente código:

         /**
          * Referencia del cliente de Servicios móviles
          */
         private static MobileServiceClient mClient;

         /**
          * Devuelve la referencia del cliente.
          */
         public static MobileServiceClient getClient() {
             return mClient;
         }

8.  En el archivo **MyHandler**, agregue las siguientes instrucciones de importación:

         import android.util.Log;
            
         import com.microsoft.windowsazure.notifications.NotificationsHandler;

         import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
         import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
         import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
         import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Agregue el siguiente código al final del método `onRegistered`:

         MobileServiceClient client = ToDoActivity.getClient();
         MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

         // Crear un nuevo registro.
         Registration registration = new Registration();
         registration.setHandle(gcmRegistrationId);

         // Insertar el nuevo registro.
         registrations.insert(registration, new TableOperationCallback<Registration>() {

             public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                 if (exception != null) {
                     Log.e("MyHandler", exception.getMessage());
                 } else {
                     Log.e("MyHandler", "Registro correcto");
                 }
             }
         });

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción para los usuarios.

Actualización de los scripts de servidor
----------------------------------------

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **Registration**.

  ![][3]

2.  En **Registration**, haga clic en la pestaña **Script** y seleccione **Insert**.

  ![][4]

  Se muestra la función que se invoca cuando se produce una inserción en la tabla **Registration**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
             var registrationTable = tables.getTable('Registration');
             registrationTable
                 .where({ handle: item.handle })
                 .read({ success: insertRegistrationIfNotFound });
             function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }

   			 }

         }

  Este script comprueba la tabla **Registration** por si existiera un registro existente con el mismo URI. La inserción solo se lleva a cabo si no se encuentra ningún registro que coincida. De esta forma, se evita la duplicación de registros en el registro.

4.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

  ![][5]

5.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Escriba la respuesta y luego envíe la notificación en segundo plano
                     request.respond();
                     sendNotifications(item.text);
            
                 }
             });
            
         function sendNotifications(item_text) {
             var registrationTable = tables.getTable('Registration');
             registrationTable.read({
                 success: function(registrations) {
                     registrations.forEach(function(registration) {
                         push.gcm.send(registration.handle, item_text, {
                             success: function(response) {
                                 console.log('Notificación de inserción enviada: ', response);
                             }, error: function(error) {
                                 console.log('Error al enviar notificación de inserción: ', error);
                             }
                         });
                     });
                 }
             });
         }

    }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los registros almacenados en la tabla **Registration**.

Prueba de la aplicación
-----------------------

1.  En Eclipse, en el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el botón **Agregar**.

3.  Un cuadro de notificación de color negro aparecerá brevemente en la parte inferior de la pantalla.

  ![](./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png)

Ha completado correctamente este tutorial.

Pasos siguientes
----------------

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de las notificaciones de inserción. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos](/es-es/develop/mobile/tutorials/get-started-with-data-android)
  <br/>Obtenga más información sobre almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación](/es-es/develop/mobile/tutorials/get-started-with-users-android)
  <br/>Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Uso de la biblioteca de cliente Android para Servicios móviles](/es-es/develop/mobile/how-to-guides/work-with-android-client-library)
  <br/>Obtenga más información sobre cómo usar Servicios móviles con .NET.


<!-- Images. -->
[0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
[3]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
[4]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
[5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
[6]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png

[27]: ./media/mobile-services-android-push-notifications-app-users/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png