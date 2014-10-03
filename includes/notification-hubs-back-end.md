En esta sección se muestra cómo enviar notificaciones desde una aplicación de la consola .NET y cualquier otra.
Si está utilizando Servicios móviles, consulte los tutoriales [Introducción a la inserción][]. Si desea utilizar Java o PHP, consulte [Uso de Centro de notificaciones desde Java/PHP][]. Puede enviar notificaciones desde cualquier back-end mediante la [API REST de notificaciones de inserción][].

El código siguiente envía notificaciones a dispositivos de la Tienda Windows, Windows Phone, iOS y Android.

Si creó una aplicación de consola cuando realizó el tutorial [Introducción a los Centros de notificaciones][], omita los pasos del 1 al 3.

1.  En Visual Studio, cree una aplicación de consola en Visual C\#:

    ![][]

2.  En el menú principal de Visual Studio, haga clic sucesivamente en **Herramientas**, **Library Package Manager** y **Consola del Administrador de paquetes**; a continuación, en la ventana de la consola, escriba lo siguiente y presione **Entrar**:

        Install-Package WindowsAzure.ServiceBus

    Se agrega una referencia al SDK de Bus de servicio de Azure mediante [WindowsAzure.ServiceBus NuGet package][].

3.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:

        using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método o sustitúyalo si ya existe:

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};

            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    Este código envía notificaciones para cada una de las seis etiquetas de la matriz de cadenas a dispositivos de la Tienda Windows, Windows Phone y iOS. El uso de etiquetas ofrece la seguridad de que los dispositivos reciben notificaciones solo de las categorías registradas.

    <div class="dev-callout"><strong>Nota:</strong> 
    <p>Este c&oacute;digo de back-end es compatible con clientes de la Tienda Windows, Windows Phone , iOS y Android. Los m&eacute;todos de env&iacute;o devuelven una respuesta de error cuando el centro de notificaciones no se ha configurado a&uacute;n para una plataforma cliente determinada. </p>
</div>

5.  En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with full access>` por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* que obtuvo anteriormente.

6.  Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors -->
<!-- Images. -->
<!-- URLs. -->

  [Introducción a la inserción]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Uso de Centro de notificaciones desde Java/PHP]: /en-us/documentation/articles/notification-hubs-java-backend-how-to/
  [API REST de notificaciones de inserción]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [Introducción a los Centros de notificaciones]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet package]: http://nuget.org/packages/WindowsAzure.ServiceBus/
