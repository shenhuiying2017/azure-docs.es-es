<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registro del usuario actual para notificaciones de inserción con un servicio móvil

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

Este tema muestra cómo solicitar un registro de notificación de inserción con Centros de notificaciones de Azure al realizar el registro mediante Servicios móviles de Azure. Este tema amplía el tutorial [Notificación a los usuarios con los Centros de notificaciones][]. Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con los Centros de notificaciones][].

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que creó cuando completó el tutorial [Introducción a la autenticación][], completado como requisito previo.

2.  En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Store** y, a continuación, haga clic en **Associate App with the Store...**.

    ![][]

    Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

3.  En el asistente, haga clic en **Sign in** y, a continuación, inicie sesión con su cuenta de Microsoft.

4.  Seleccione la aplicación que registró en [Notificación a los usuarios en los Centros de notificaciones][Notificación a los usuarios con los Centros de notificaciones], haga clic en **Siguiente** y, a continuación, en **Associate**.

    ![][1]

    Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

    <div class="dev-callout"><b>Nota:</b>
<p>Esto utiliza de nuevo el registro de la Tienda Windows desde la aplicaci&oacute;n del tutorial de Centros de notificaciones con esta aplicaci&oacute;n de Servicios m&oacute;viles. Esto puede impedir que la aplicaci&oacute;n del tutorial de Centros de notificaciones reciba notificaciones.</p>
</div>

5.  En el Explorador de soluciones, haga doble clic en el archivo de proyecto Package.appxmanifest para abrirlo en el editor de Visual Studio.

6.  Desplácese hasta **Todos los activos de imagen** y haga clic en **Logotipo del distintivo**. En **Notificaciones**, establezca **Capacidad de aviso** en **Yes**:

    ![][2]

    Esto permite que la aplicación del tutorial de Servicios móviles reciba notificaciones del sistema.

7.  En Visual Studio, abra el archivo MainPage.xaml.cs y agregue el siguiente código que define las clases **NotificationRequest** y **RegistrationResult**:

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

8.  En la clase **MainPage**, agregue el siguiente método:

        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método crea un canal para notificaciones de inserción y lo envía, junto con el tipo de dispositivo, al método de API personalizada que crea un registro en los Centros de notificaciones. La API personalizada se definió en [Notificación a los usuarios con los Centros de notificaciones][].

9.  Agregue la siguiente línea de código al método **OnNavigatedTo**, justo después de que se haya llamado al método **Authenticate**:

        await RegisterNotification();

    <div class="dev-callout"><b>Nota:</b>
<p>Esto garantiza que se solicitar&aacute; registro cada vez que se cargue la p&aacute;gina. En la aplicaci&oacute;n, es posible que solo desee realizar este registro de manera peri&oacute;dica para asegurarse de que el registro est&eacute; actualizado.</p>
</div>

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con los Centros de notificaciones][] y actualice el servicio móvil para enviar notificaciones mediante Centros de notificaciones.

  [C# para Tienda Windows]: /es-es/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "C# para Tienda Windows"
  [iOS]: /es-es/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS"
  [Notificación a los usuarios con los Centros de notificaciones]: /es-es/manage/services/notification-hubs/notify-users
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-dotnet/
  []: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
  [1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
  [2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png
