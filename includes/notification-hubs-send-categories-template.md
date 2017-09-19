
En esta sección se envían noticias de última hora como notificaciones de plantilla con etiquetas desde una aplicación de consola .NET.

Si usa la característica Mobile Apps de Microsoft Azure App Service, consulte el tutorial sobre la [incorporación de notificaciones push para Mobile Apps] y seleccione su plataforma en la parte superior.

Si desea utilizar Java o PHP, consulte el artículo sobre el [uso de Notification Hubs desde Java o PHP]. Puede enviar notificaciones desde cualquier back-end mediante la [interfaz de REST de Notification Hubs].

Si creó la aplicación de consola para enviar notificaciones cuando completó el artículo de [introducción a Notification Hubs], omita los pasos 1-3.

1. En Visual Studio, cree una nueva aplicación de consola en Visual C#:
   
      ![Vínculo a la aplicación de consola][13]

2. En el menú principal de Visual Studio, seleccione **Herramientas** > **Administrador de paquetes de la biblioteca** > **Consola del administrador de paquetes** y, en la ventana de la consola, escriba la cadena siguiente:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Seleccione **Entrar**.  
    Esta acción agrega una referencia al SDK de Azure Notification Hubs mediante el [paquete NuGet Microsoft.Azure.NotificationHubs].

4. Abra el archivo Program.cs y agregue la siguiente instrucción `using`:
   
        using Microsoft.Azure.NotificationHubs;

5. En la clase `Program` , agregue el siguiente método o reemplácelo si ya existe:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Este código envía una notificación de plantilla para cada una de las seis etiquetas en la matriz de cadenas. El uso de etiquetas garantiza que los dispositivos reciben notificaciones solo de las categorías registradas.

5. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with full access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultFullSharedAccessSignature* del panel del centro de notificaciones.

6. Agregue las siguientes líneas al método **Main**:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Compile la aplicación de consola.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[introducción a Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interfaz de REST de Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[incorporación de notificaciones push para Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[uso de Notification Hubs desde Java o PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[paquete NuGet Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
