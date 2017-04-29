
En esta sección se muestra cómo enviar noticias de última hora como notificaciones de plantillas con etiquetas desde una aplicación de consola .NET.

Si usa aplicaciones Móviles, consulte el tutorial [Incorporación de notificaciones push para Mobile Apps] y seleccione su plataforma en la parte superior.

Si desea utilizar Java o PHP, consulte [Uso de Notification Hubs desde Java/PHP]. Puede enviar notificaciones desde cualquier back-end mediante la [Interfaz de REST de Notification Hubs].

Omita los pasos 1-3 si creó la aplicación de consola para enviar notificaciones cuando completó [Introducción a Notification Hubs].

1. En Visual Studio, cree una aplicación de consola en Visual C#:
   
       ![][13]
2. En el menú principal de Visual Studio, haga clic sucesivamente en **Herramientas**, **Administrador de paquetes de la biblioteca** y **Consola del administrador de paquetes**, y luego, en la ventana de la consola, escriba lo siguiente y presione **Entrar**:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Así se agrega una referencia al SDK de Centros de notificaciones de Azure mediante el [paquete NuGet Microsoft.Azure.Notification Hubs].
3. Abra el archivo Program.cs y agregue la siguiente instrucción `using` :
   
        using Microsoft.Azure.NotificationHubs;
4. En la clase `Program` , agregue el siguiente método o reemplácelo si ya existe:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Sending the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Este código envía una notificación de plantilla para cada una de las seis etiquetas en la matriz de cadenas. El uso de etiquetas ofrece la seguridad de que los dispositivos reciben notificaciones solo de las categorías registradas.
5. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with full access>` por su nombre del centro de notificaciones y la cadena de conexión para *DefaultFullSharedAccessSignature* del panel de su centro de notificaciones.
6. Agregue las siguientes líneas al método **Main** :
   
         SendTemplateNotificationAsync();
         Console.ReadLine();
7. Compile la aplicación de consola.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Introducción a Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Interfaz de REST de Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Incorporación de notificaciones push para Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Uso de Notification Hubs desde Java/PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[paquete NuGet Microsoft.Azure.Notification Hubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
