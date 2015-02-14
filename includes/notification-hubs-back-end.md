
En esta sección se muestra cómo enviar notificaciones desde una aplicación de consola .NET y desde cualquier otra aplicación.
Si está usando Servicios móviles, consulte los tutoriales de [Introducción a las notificaciones de inserción](/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/) . Si desea usar Java o PHP, consulte [Uso de los Centros de notificaciones desde Java o PHP](/es-es/documentation/articles/notification-hubs-java-backend-how-to/). Puede enviar notificaciones desde cualquier back-end mediante la [interfaz de REST de Centros de notificaciones].

El código siguiente envía notificaciones a dispositivos de la Tienda Windows, Windows Phone, iOS y Android. 

Omita los pasos 1-3 si creó una aplicación de consola cuando realizó el tutorial [Introducción a los Centros de notificaciones][get-started].

1. En Visual Studio, cree una aplicación de consola en Visual C#: 

   	![][13]

2. En el menú principal de Visual Studio, haga clic sucesivamente en **Herramientas**, **Administrador de paquetes de la biblioteca** y **Consola del administrador de paquetes**, y luego, en la ventana de la consola, escriba lo siguiente y presione **Entrar**:

        Install-Package WindowsAzure.ServiceBus
 	
	Se agrega una referencia al SDK de Bus de servicio de Azure mediante el paquete de NuGet <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

3. Abra el archivo Program.cs y agregue la siguiente instrucción  `using`:

        using Microsoft.ServiceBus.Notifications;

4. En la clase  `Program`, agregue el siguiente método o reemplácelo si ya existe:

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
	
	> [AZURE.NOTE] Este código de back-end es compatible con clientes de la Tienda Windows, Windows Phone , iOS y Android. Los métodos de envío devuelven una respuesta de error cuando el centro de notificaciones no se ha configurado aún para una plataforma cliente determinada. 

6. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with full access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultFullSharedAccessSignature* que obtuvo anteriormente.

7. Agregue las siguientes líneas al método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[Desde una aplicación de consola]: #console
[Desde los Servicios móviles]: #mobile-services
[Ejecución de la aplicación y generación de notificaciones]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Uso de los Centros de notificaciones para enviar notificaciones a los usuario]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Portal de administración de Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Información general acerca de los Centros de notificaciones ]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para la Tienda Windows]: http://msdn.microsoft.com/es-es/library/jj927172.aspx
[Interfaz de REST de los Centros de notificaciones]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
<!--HONumber=42-->
