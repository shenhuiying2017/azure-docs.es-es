<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones
======================================================================================

[Servicios móviles](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Servicios móviles") [ASP.NET](/es-es/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

En el tutorial anterior, [Notificación a los usuarios con Centros de notificaciones](/es-es/manage/services/notification-hubs/notify-users), aprendió a insertar notificaciones en todos los dispositivos que tiene registrado un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Los centros de notificaciones son compatibles con plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Con esto se simplifica el envío de notificaciones entre plataformas. Este tema muestra cómo aprovecha las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Si desea obtener información más detallada sobre las plantillas, consulte [Información general acerca de los centros de notificaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Nota:**

Los centros de notificaciones permiten que un dispositivo registre varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.

Lleve a cabo los siguientes pasos para enviar notificaciones entre plataformas mediante plantillas:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

  ![][0]

2.  Haga clic en la pestaña **API** y, a continuación, en la entrada **register\_notifications** de la tabla de API.

  ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png)

3.  Haga clic en la pestaña **Script**, busque el bloque **else** que crea un registro cuando el valor de `existingRegs` es **false** y reemplácelo por el siguiente código:

		else {
		     // Cree un nuevo registro.
		     var template;
		     if (platform === 'win8') {                
		         template = { text1: '$(message)' };              
		         hub.wns.createToastText01Registration(request.body.channelUri, 
		         [userId, installationId], template, registrationComplete);
		     } else if (platform === 'ios') {
		         template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
		         hub.apns.createTemplateRegistration(request.body.deviceToken, 
		         [userId, installationId], template, registrationComplete);
		     } else {
		         response.send(500, 'Unknown client.');
		     }
		}

    Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No se deben modificar los registros existentes, dado que los registros de plantilla derivan de los registros nativos.

4.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

  ![][2]

5.  En **todoitem**, haga clic en la pestaña **Script**, seleccione **Insert** y, a continuación, reemplace la función **insert** existente por el siguiente código:

  ![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png)

   Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

1.  Reemplace la función de inserción por el código siguiente:

         function insert(item, user, request) {
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
             '<FULL_SAS_CONNECTION_STRING>');
            
             // Ejecutar la solicitud y enviar notificaciones.
             request.execute({
                 success: function() {
                     // Escribir la respuesta predeterminada y enviar una notificación 
                     // al usuario en todos los dispositivos usando la etiqueta userId.
                     request.respond();
                     // Crear una carga basada en plantilla.
                     var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                     // Enviar una notificación al usuario actual en todas las plataformas. 
                     hub.send(user.userId, payload,  
                     function(error, outcome){
                         // Hacer aquí algo con el resultado o el error.
                     });     
                 }
             });
         }

    Este código envía una notificación a todas las plataformas al mismo tiempo y sin que sea necesario especificar una carga nativa. Los centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor *tag* proporcionado, tal como se especifica en las plantillas registradas.

2.  Actualice el script para reemplazar *`<NOTIFICATION_HUB_NAME>`* y *`<FULL_SAS_CONNECTION_STRING>`* por los valores de su centro de notificaciones y, a continuación, haga clic en **Save**.

3.  Detenga el emulador de dispositivo o desinstale la aplicación cliente existente del dispositivo.

    Con esto se asegura que el cliente de Servicios móviles genera un nuevo identificador de instalación. Si no lo hace, el servicio intentará utilizar el registro existente no basado en plantilla.

4.  Implemente y ejecute nuevamente la aplicación cliente y compruebe que el registro se realice correctamente y que aparezca un identificador de registro nuevo.

5.  Escriba texto como antes y agregue el elemento nuevo.

    Tenga en cuenta que después de que se termina la inserción, la aplicación recibe una notificación de inserción desde los Centros de notificaciones.

6.  (Opcional) Implemente la aplicación cliente en un segundo dispositivo y después ejecute la aplicación e inserte el texto.

    Observe que aparecerá una notificación en cada dispositivo.

Pasos siguientes
----------------

Ahora que completó este tutorial, obtenga más información acerca de los centros de notificaciones y las plantillas en estos temas:

-   **Uso de los Centros de notificaciones para enviar noticias de última hora ([C\# para Tienda Windows](/es-es/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/es-es/manage/services/notification-hubs/breaking-news-ios))**
    Muestra otro escenario para el uso de las plantillas

-   **[Información general acerca de los centros de notificaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Este tema de información general contiene información más detallada sobre las plantillas.

-   **[Notification Hub How to for Windows Store](http://msdn.microsoft.com/es-es/library/windowsazure/jj927172.aspx)**
    Incluye una referencia al lenguaje de la expresión de plantilla.


<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png