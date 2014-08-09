<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Notificación a los usuarios con los Centros de notificaciones
=============================================================

[Servicios móviles](/es-es/manage/services/notification-hubs/notify-users "Servicios móviles") [ASP.NET](/es-es/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

Este tutorial muestra cómo puede utilizar los Centros de notificaciones de Azure para enviar notificaciones de inserción a un usuario de aplicaciones determinado en un dispositivos concreto. Un back-end de Servicios móviles de Azure se usa para autenticar a los clientes y generar notificaciones. Este tutorial se basa en el centro de notificaciones que ha creado en el tutorial anterior, **Introducción a los Centros de notificaciones**. El código de registro de notificación se desplaza del cliente al servicio back-end. Esto asegura que el registro solo se completa una vez que el servicio haya autenticado de forma positiva al cliente. También significa que las credenciales del centro de notificaciones no se distribuyen con la aplicación cliente. El servicio también controla las etiquetas solicitadas durante el registro.

Este tutorial le guiará a través de los siguientes pasos básicos:

-   [Actualización de su servicio móvil para registrar notificaciones](#register-notification)
-   [Actualización de la aplicación para iniciar sesión y solicitar el registro](#update-app)
-   [Actualización de su servicio móvil para enviar notificaciones](#send-notifications)

Requisitos previos
------------------

Antes de empezar este tutorial, debe completar primero los siguientes tutoriales:

-   **Introducción a los Centros de notificaciones** ([C\# para Tienda Windows](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/es-es/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/es-es/manage/services/notification-hubs/get-started-notification-hubs-android)).

-   **Introducción a la autenticación en Servicios móviles** ([C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/es-es/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/es-es/develop/mobile/tutorials/get-started-with-users-android/))

Este tutorial se basa en la aplicación y el centro de notificaciones que creó en **Introducción a los Centros de notificaciones**. Aprovecha también el servicio móvil autenticado que configuró en **Introducción a la autenticación en Servicios móviles**.

**Nota:**

De manera predeterminada, el tutorial **Introducción a la autenticación en Servicios móviles** usa la autenticación de Facebook. No es posible usar la autenticación de la cuenta Microsoft en este tutorial, debido a que dos aplicaciones de la Tienda Windows no pueden compartir un solo registro de Live Connect. Para usar la autenticación de la cuenta Microsoft, el servicio móvil y el centro de notificaciones deben estar registrados en la misma aplicación en Live Connect.

Registro para notificacionesActualización de su servicio móvil para registrarse para recibir notificaciones
-----------------------------------------------------------------------------------------------------------

Debido a que el registro de notificaciones solo debe completarse después de que un cliente se haya autenticado positivamente a través del servicio, al registro se le realiza una API personalizada definida en el servicio móvil. Un cliente autenticado llama a esta API personalizada para solicitar el registro de notificaciones. En esta sección, actualizará el servicio móvil autenticado que definió cuando completó el tutorial **Introducción a la autenticación en Servicios móviles**.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Bus de servicio**, su espacio de nombres, **Centros de notificaciones**, luego seleccione su centro den notificaciones y haga clic en **Connection Information**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  Anote el nombre de su centro de notificaciones y copie la cadena de conexión para **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    Utilizará esta cadena de conexión, junto con el nombre del centro de notificaciones, tanto para registrar como para enviar notificaciones.

3.  Estando todavía en el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  Haga clic en la pestańa **API** y, a continuación, haga clic en **Create a custom API**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

    Esto muestra el cuadro de diálogo **Create a new custom API**.

5.  Escriba *register\_notifications* en **API name**, seleccione **Only Authenticated Users** para **POST Permissions** y, a continuación, haga clic en el botón de comprobación.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	De esta manera se crea la API que exige que los usuarios se autentiquen antes de llamar usando el método HTTP POST. No es necesario establecer los otros métodos HTTP porque no los implementaremos.

1.  Haga clic en la entrada nueva **register\_notifications** en la tabla de API.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  Haga clic en la pestańa **Script** y reemplace el código existente por el siguiente:

         exports.post = function(request, response) {

             // Crear una instancia de centro de notificaciones.
             var azure = require('azure');
		     var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
				'<FULL_SAS_CONNECTION_STRING>');
            
             // Obtenga la información de registro que necesitamos de la solicitud. 
             var platform = request.body.platform;
             var userId = request.user.userId;
             var installationId = request.header('X-ZUMO-INSTALLATION-ID');
                
             // Función llamada cuando se complete el registro.
             var registrationComplete = function(error, registration) {
                 if (!error) {
                     // Devolver el registro.
                     response.send(200, registration);
                 } else {
                     response.send(500, 'Registration failed!');
                 }
             }
             // Función llamada para registrar errores.
             var logErrors = function(error) {
                 if (error) {
                     console.error(error)
                 }
             }
             // Obtener registros existentes.
             hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
                 var firstRegistration = true;
                 if (existingRegs.length > 0) {
                      for (var i = 0; i < existingRegs.length; i++) {
                         if (firstRegistration) {
                             // Actualizar un registro existente.
                             if (platform === 'win8') {
                                 existingRegs[i].ChannelUri = request.body.channelUri;                        
                                 hub.updateRegistration(existingRegs[i], registrationComplete);                        
                             } else if (platform === 'ios') {
                                 existingRegs[i].DeviceToken = request.body.deviceToken;
                                 hub.updateRegistration(existingRegs[i], registrationComplete);
                             } else {
                                 response.send(500, 'Unknown client.');
                             }
                             firstRegistration = false;
                         } else {
                             // No debemos tener registros adicionales; de lo contrario, habrá que eliminarlos.
                             hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
                         }
                     }
                 } else {
                     // Cree un nuevo registro.
                     if (platform === 'win8') {                
                         hub.wns.createNativeRegistration(request.body.channelUri, 
                         [userId, installationId], registrationComplete);
                     } else if (platform === 'ios') {
                         hub.apns.createNativeRegistration(request.body.deviceToken, 
                         [userId, installationId], registrationComplete);
                     } else {
                         response.send(500, 'Unknown client.');
                     }
                 }
             });
         }

    Este código obtiene información de la plataforma y del identificador del dispositivo a partir del cuerpo del mensaje. Estos datos, junto con el identificador de instalación del encabezado de la solicitud y el identificador de usuario del usuario que inició sesión, se usan para actualizar un registro, si lo hay, o bien crear uno nuevo. Este registro se etiqueta con el identificador de usuario y el identificador de instalación.

3.  Actualice el script para reemplazar _`<NOTIFICATION_HUB_NAME>`_ y _`<FULL_SAS_CONNECTION_STRING>`_ por los valores de su centro de notificaciones y, a continuación, haga clic en **Save**.

    **Nota:**

    Asegúrese de utilizar **DefaultFullSharedAccessSignature** para _`<FULL_SAS_CONNECTION_STRING>`_. Esta notificación permite que su método de API personalizado cree y actualice los registros.

Actualización de la aplicaciónActualización de la aplicación para iniciar sesión y solicitar el registro
--------------------------------------------------------------------------------------------------------

A continuación, necesita actualizar la aplicación TodoList para solicitar el registro de notificaciones llamando a la API personalizada nueva.

1.  Siga los pasos de una de las siguientes versiones de **Registro del usuario actual para notificaciones de inserción con un servicio móvil**, según su plataforma cliente:

    -   [Versión C\# de Tienda Windows](/es-es/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [Versión de iOS](/es-es/manage/services/notification-hubs/register-users-ios)

2.  Ejecute la aplicación actualizada, inicie sesión con Facebook y luego verifique que aparece el identificador de registro asignado a la notificación.

Envío de notificacionesActualización de su servicio móvil para enviar notificaciones
------------------------------------------------------------------------------------

El paso final es agregar código que envía notificaciones en el servicio móvil. Este código de notificación se agrega al script del servidor registrado en el controlador de inserciones de la tabla **TodoItem**.

1.  Nuevamente en el Portal de administración, haga clic en la pestańa **Data** y, a continuación, en la tabla **TodoItem**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  En **todoitem**, haga clic en la pestańa **Script** y seleccione **Insert**.

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 
	
	Se muestra la función que se invoca cuando se produce una inserción en la tabla **TodoItem**.

1.  Reemplace la función de inserción por el código siguiente:

         function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
 		   // Crear la carga para una aplicación de la Tienda Windows.
		    var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Ejecutar la solicitud y enviar notificaciones.
		    request.execute({
		        success: function() {
		            // Escribir la respuesta predeterminada y enviar una notificación 
		            // al usuario en todos los dispositivos usando la etiqueta userId.
		            request.respond();
		            // Enviar a las aplicaciones de la Tienda Windows.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Enviar a las aplicaciones de iOS.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}

    De esta manera se intenta enviar notificaciones a la etiqueta para el usuario que inició sesión actual en las aplicaciones de la Tienda Windows e iOS.

1.  Actualice el script para reemplazar _`<NOTIFICATION_HUB_NAME>`_ y _`<FULL_SAS_CONNECTION_STRING>`_ por los valores de su centro de notificaciones y, a continuación, haga clic en **Save**.

    De esta forma, se registra un nuevo script de inserción, que usa Centros de notificaciones para enviar una notificación de inserción (el texto insertado) al canal indicado en la solicitud de inserción.

    **Nota:**

   Asegúrese de utilizar **DefaultFullSharedAccessSignature** para _`<FULL_SAS_CONNECTION_STRING>`_. Esta notificación le otorga acceso completo al script de inserción, incluida la posibilidad de enviar notificaciones a los clientes registrados.

Prueba de la aplicaciónPruebas de notificaciones de inserción en su aplicación
------------------------------------------------------------------------------

Ahora que las notificaciones están configuradas, es el momento de probar la aplicación insertando datos para generar una notificación.

1.  Ejecute la aplicación.

    Se vuelve a mostrar una notificación de registro en el inicio.

2.  Escriba texto como antes y agregue el elemento nuevo.

    Tenga en cuenta que después de que se termina la inserción, la aplicación recibe una notificación de inserción desde los Centros de notificaciones.

    **Nota:**

    Se genera un error en el back-end cuando no hay ningún registro de una plataforma para la cual se solicita se envíe una notificación. En este caso, se puede ignorar el error. Para ver cómo utilizar las plantillas con el fin de evitar esta situación, consulte [Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

3.  (Opcional) Implemente la aplicación cliente en un segundo dispositivo y después ejecute la aplicación e inserte el texto.

    Se mostrará una notificación en cada dispositivo.

Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere la realización de los tutoriales siguientes:

-   **Uso de los Centros de notificaciones para enviar noticias de última hora ([Versión C\# de Tienda Windows](/es-es/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/es-es/manage/services/notification-hubs/breaking-news-ios))**
    Este tutorial específico de plataforma muestra cómo utilizar las etiquetas para que los usuarios puedan suscribirse a los tipos de notificaciones en los que están interesados.

-   **[Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services)**
    Este tutorial amplía el tutorial actual **Notificación a los usuarios con los Centros de notificaciones** para utilizar plantillas específicas de plataforma para registrarse para las notificaciones. Esto le permite enviar notificaciones desde un único método en su código del servidor.

Para obtener más información acerca de los Centros de notificaciones, consulte [Centros de notificaciones de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/jj927170.aspx).

