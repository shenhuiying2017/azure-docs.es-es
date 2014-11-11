<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Notificaciones de inserción para usuarios mediante Servicios móviles

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>Este tema ampl&iacute;a el <a href="/es-es/develop/mobile/tutorials/get-started-with-push-android">tutorial de notificaciones de inserci&oacute;n anterior</a> mediante la incorporaci&oacute;n de una nueva tabla a los URI de registro del servicio de mensajer&iacute;a en la nube de Google (GCM), que puede usarse para enviar notificaciones de inserci&oacute;n a varios usuarios de la aplicaci&oacute;n de Android. En este tutorial, una actualizaci&oacute;n &uacute;nica generar&aacute; notificaciones de inserci&oacute;n para todos los dispositivos registrados cuando las inserciones se realicen en la tabla ToDoList. En el tutorial anterior, se envi&oacute; una notificaci&oacute;n solo al dispositivo realizando la inserci&oacute;n.</p>
</div>

Este tutorial le guiará a través de estos pasos en la actualización de las notificaciones de inserción en su aplicación:

1.  [Creación de la tabla Registration][Creación de la tabla Registration]
2.  [Actualización de la aplicación][Actualización de la aplicación]
3.  [Actualización de los scripts de servidor][Actualización de los scripts de servidor]
4.  [Pruebas de notificaciones de inserción en su aplicación][Pruebas de notificaciones de inserción en su aplicación]

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior]. Antes de comenzar este tutorial, primero debe completar [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior].

## <a name="create-table"></a>Creación de una tabla

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, en **Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Registration* en **Table name** y, a continuación, haga clic en el botón de comprobación.

    ![][2]

    Esto crea la tabla **Registration**, la cual almacena los URI de registro que se usan para enviar notificaciones de inserción de manera independiente de los datos de elementos.

A continuación, modificará la aplicación de notificaciones de inserción para almacenar datos de registro en esta nueva tabla en lugar de en la tabla **TodoItem**.

## <a name="update-app"></a>Actualización de la aplicación

1.  En Eclipse, en el Explorador de paquetes, haga clic con el botón secundario en el paquete (bajo el nodo `src`), haga clic en **Nuevo** y después en **Clase**.

2.  En **Nombre**, escriba `Registration` y haga clic en **Finalizar**.

    ![][3]

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
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
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
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
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

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

Ahora su aplicación está actualizada para que sea compatible con las notificaciones de inserción para los usuarios.

## <a name="update-scripts"></a>Actualización de los scripts de servidor

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **Registration**.

    ![][4]

2.  En **Registration**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][5]

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

    ![][6]

5.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
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
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
           }
         }

   Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los registros almacenados en la tabla **Registration**.

## <a name="test-app"></a>Prueba de la aplicación

1.  En Eclipse, en el menú **Ejecutar**, haga clic en **Ejecutar** para iniciar la aplicación.

2.  En la aplicación, escriba un texto significativo, como *Una nueva tarea de Servicios móviles* y, a continuación, haga clic en el botón **Agregar**.

3.  Un cuadro de notificación de color negro aparecerá brevemente en la parte inferior de la pantalla.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

Ha completado correctamente este tutorial.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de las notificaciones de inserción. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Uso de la biblioteca de cliente Android para Servicios móviles][Uso de la biblioteca de cliente Android para Servicios móviles]
    Obtenga más información sobre cómo usar Servicios móviles con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [tutorial de notificaciones de inserción anterior]: /es-es/develop/mobile/tutorials/get-started-with-push-android
  [Creación de la tabla Registration]: #create-table
  [Actualización de la aplicación]: #update-app
  [Actualización de los scripts de servidor]: #update-scripts
  [Pruebas de notificaciones de inserción en su aplicación]: #test-app
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-android
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-android
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Uso de la biblioteca de cliente Android para Servicios móviles]: /es-es/develop/mobile/how-to-guides/work-with-android-client-library
