<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Registro del usuario actual para notificaciones de inserción con un servicio móvil
==================================================================================

[C\# para Tienda Windows](/en-us/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "C# para Tienda Windows")[iOS](/en-us/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

Este tema muestra cómo solicitar un registro de notificación de inserción con Centros de notificaciones de Azure al realizar el registro mediante Servicios móviles de Azure. Este tema amplía el tutorial [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users). Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users).

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/), completado como requisito previo.

2.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

	![](./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png) 
Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

1.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

2.  Seleccione la aplicación que registró en [Notificación a los usuarios en los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users), haga clic en **Siguiente** y, a continuación, en **Associate**.

	![][2]

	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.  

    **Nota:**

    Esto utiliza de nuevo el registro de la Tienda Windows desde la aplicación del tutorial de Centros de notificaciones con esta aplicación de Servicios móviles. Esto puede impedir que la aplicación del tutorial de Centros de notificaciones reciba notificaciones.

3.  En el Explorador de soluciones, haga doble clic en el archivo de proyecto Package.appxmanifest para abrirlo en el editor de Visual Studio.

4.  Desplácese hasta **Todos los activos de imagen** y haga clic en **Logotipo del distintivo**. En **Notificaciones**, establezca **Capacidad de aviso** en **Yes**:

	![][3]

    Esto permite que la aplicación del tutorial de Servicios móviles reciba notificaciones del sistema.

5.  En Visual Studio, abra el archivo MainPage.xaml.cs y agregue el siguiente código que define las clases **NotificationRequest** y **RegistrationResult**:

         public class NotificationRequest
         {
             public string channelUri { get; set; }
             public string platform { get; set; }
         }

         public class RegistrationResult
         {
             public string RegistrationId { get; set; }
             public string ExpirationTime { get; set; }
         }

    Estas clases retendrán, respectivamente, el cuerpo de la solicitud y el identificador de registro que se devuelven cuando se llama a la API personalizada.

6.  En la clase **MainPage**, agregue el siguiente método:

         private async System.Threading.Tasks.Task RegisterNotification()
         {
             string message;

             // Obtener un canal para las notificaciones de inserción.
             var channel =
                 await Windows.Networking.PushNotifications
                 .PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();

             // Crear el cuerpo de la solicitud.
             var body = new NotificationRequest 
             {
                 channelUri = channel.Uri, 
                 platform = "win8" 
             }; 

             try
             {
                 // Llamar al método personalizado API POST con el cuerpo suministrado.
                 var result = await App.MobileService
                     .InvokeApiAsync<NotificationRequest, 
                     RegistrationResult>("register_notifications", body,
                     System.Net.Http.HttpMethod.Post, null);

                 // Definir la respuesta, que es el identificador del registro.
                 message = string.Format("Registration ID: {0}", result.RegistrationId);
             }
             catch (MobileServiceInvalidOperationException ex)
             {
                 message = ex.Message;
             }

             // Mostrar un cuadro de diálogo de mensaje.
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Este método crea un canal para notificaciones de inserción y lo envía, junto con el tipo de dispositivo, al método de API personalizada que crea un registro en los Centros de notificaciones. La API personalizada se definió en [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users).

7.  Agregue la siguiente línea de código al método **OnNavigatedTo**, justo después de que se haya llamado al método **Authenticate**:

         await RegisterNotification();

    **Nota:**

    Esto garantiza que se solicitará registro cada vez que se cargue la página. En la aplicación, es posible que solo desee realizar este registro de manera periódica para asegurarse de que el registro esté actualizado.

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con los Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users) y actualice el servicio móvil para enviar notificaciones mediante Centros de notificaciones.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notify users with Notification Hubs]: /en-us/manage/services/notification-hubs/notify-users
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
