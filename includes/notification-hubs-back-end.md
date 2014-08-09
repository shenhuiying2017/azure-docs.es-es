En esta sección se muestra cómo enviar notificaciones de dos formas diferentes:

* [Desde una aplicación de consola](#console)
* [Desde los Servicios móviles](#mobile-services)

Ambos back-ends envían notificaciones a dispositivos de la Tienda Windows e iOS. Puede enviar notificaciones desde cualquier back-end mediante la [API REST de notificaciones de inserción][1].

<h3><a name="console"></a>Para enviar notificaciones desde una aplicación de consola en C#</h3>


Si creó una aplicación de consola cuando realizó el tutorial [Introducción a los Centros de notificaciones](/es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/), omita los pasos del 1 al 3.

1.  En Visual Studio, cree una aplicación de consola en Visual C#:

	![][13]

2.  En el menú principal de Visual Studio, haga clic sucesivamente en **Herramientas**, **Library Package Manager** y **Consola del Administrador de paquetes**; a continuación, en la ventana de la consola, escriba lo siguiente y presione **Entrar**:
    
         Install-Package WindowsAzure.ServiceBus
    
    Se agrega una referencia al SDK de Bus de servicio de Azure mediante <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.

3.  Abra el archivo Program.cs y agregue la siguiente instrucción `using`:
    
         using Microsoft.ServiceBus.Notifications;

4.  En la clase `Program`, agregue el siguiente método o sustitúyalo si ya existe:
    
         private static async void SendNotificationAsync()
         {
             // Defina el centro de notificaciones.
             NotificationHubClient hub = 
                 NotificationHubClient.CreateClientFromConnectionString(
                     "<connection  string with full access>", "<hub  name>");
        	
             // Cree un conjunto de categorías de últimas noticias.
             var categories = new string[] { "World", "Politics", "Business", 
                 "Technology", "Science", "Sports"};
        	
             foreach (var category in categories)
             {
                 try
                 {
                     // Defina una notificación de la Tienda Windows.
                     var wnsToast = "<toast><visual><binding  template=\"ToastText01\">" 
                         + "<text  id=\"1\">Breaking " + category + " News!" 
                         + "</text></binding></visual></toast>";
                     // Envíe una notificación WNS utilizando la plantilla.            
                     await hub.SendWindowsNativeNotificationAsync(wnsToast, category);
        
                     // Defina una notificación de Windows Phone.
                     var mpnsToast =
                         "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                         "<wp :Notification xmlns:wp=\"WPNotification\">" +
                             "<wp :Toast>" +
                                 "<wp :Text1>Breaking " + category + " News!</wp:Text1>" +
                             "</wp:Toast> " +
                         "</wp:Notification>";
                     // Envíe una notificación MPNS utilizando la plantilla.            
                     await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);
        
                     // Defina una alerta iOS.
                     var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                     // Envíe una notificación APNS utilizando la plantilla.
                     await hub.SendAppleNativeNotificationAsync(alert, category);
                 }
                 catch (ArgumentException)
                 {
                     // Se genera una excepción cuando el centro de notificaciones no se ha 
                     // registrado en la plataforma de iOS, Tienda Windows o Windows Phone. 
                 }
             }
          }
    
    Este código envía notificaciones para cada una de las seis etiquetas de la matriz de cadenas a dispositivos de la Tienda Windows, Windows Phone y iOS. El uso de etiquetas ofrece la seguridad de que los dispositivos reciben notificaciones solo de las categorías registradas.

	<div class="dev-callout">
	<b>Nota:</b>
	<p>Este código de back-end es compatible con clientes de la Tienda
	Windows, Windows Phone y iOS. Los métodos de envío devuelven una
	respuesta de error cuando el centro de notificaciones no se ha
	configurado aún para una plataforma cliente determinada.</p>
</div>


5.  En el código anterior, sustituya los marcadores de posición `<hub name>` y `<connection  string with full access>` por el nombre del centro de notificaciones y la cadena de conexión de *FirmaDeAccesoCompartidoCompletoPredeterminado* que obtuvo anteriormente.

6.  Agregue las siguientes líneas al método **Main**:
    
          SendNotificationAsync();
          Console.ReadLine();

Ahora puede continuar con [Ejecución de la aplicación y generación de notificaciones](#test-app).

### <a name="mobile-services"></a>Para enviar notificaciones desde los Servicios móviles

Para enviar una notificación mediante un servicio móvil, lleve a cabo los siguientes pasos:

1.  Realice el tutorial [Introducción a los Servicios móviles](/es-es/develop/mobile/tutorials/get-started/#create-new-service) para crear el servicio móvil.

2.  Inicie sesión en el [Portal de administración de Azure], haga clic en Servicios móviles y, a continuación, haga clic en el servicio móvil.

3.  Haga clic en la pestaña **Scheduler** y, a continuación, en **Create**.  

	![][15]

4.  En **Create new job**, escriba un nombre, seleccione **On demand** y, a continuación, haga clic en la marca de comprobación para aceptar.
	
	![][16]

5.  Después de crear el trabajo, haga clic en el nombre del trabajo y, a continuación, en la pestaña **Script**, inserte el siguiente script en la función de trabajo programado:
    
        var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService(
                 '<hub  name>', 
                 '<connection  string with full access>');
        
        	 // Cree un conjunto de categorías de últimas noticias.
             var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
             for (var i = 0; i < categories.length; i++) {
                 // Envíe una notificación WNS.
                 notificationHubService.wns.sendToastText01(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // Envíe una notificación MPNS.
                 notificationHubService.mpns.sendToast(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // Envíe una notificación APNS.
                 notificationHubService.apns.send(categories[i], {
                     alert: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
             }
    
    Este código envía notificaciones para cada una de las seis etiquetas de la matriz de cadenas a dispositivos de la Tienda Windows, Windows Phone y iOS. El uso de etiquetas ofrece la seguridad de que los dispositivos reciben notificaciones solo de las categorías registradas.

6.  En el código anterior, sustituya los marcadores de posición `<hub name>` y `<connection  string with full access>` por el nombre del centro de notificaciones y la cadena de conexión de *FirmaDeAccesoCompartidoCompletoPredeterminado* que obtuvo anteriormente.

7.  Agregue la siguiente función auxiliar después de la función de trabajo programado y, a continuación, haga clic en **Save**:
    
         function sendComplete(error) {   
    		if (error) {
                 // Se genera una excepción cuando no se han registrado dispositivos para 
                 // las plataformas iOS, la Tienda Windows o Windows Phone. Considere utilizar en su lugar las notificaciones 
                 // de plantilla.
                 //console.warn("Notification failed:" + error);
             }
         }

	<div class="dev-callout">

	<b>Nota:</b>
	<p>Este código es compatible con clientes de la Tienda Windows, Windows
	Phone e iOS. Los métodos de envío devuelven una respuesta de error
	cuando no existe un registro para una determinada plataforma. Para
	evitar esto, tiene la posibilidad de utilizar registros de
	plantillas para enviar una única notificación a varias plataformas.
	Por ejemplo, consulte <a href="/es-es/manage/services/notification-hubs/breaking-news-localized-dotnet/">Uso de los Centros de notificaciones para	difundir noticias de última hora localizadas</a>.</p>
</div>


Ahora puede continuar con [Ejecución de la aplicación y generación de notificaciones](#test-app).

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /es-es/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Use Notification Hubs to send notifications to users]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /es-es/develop/mobile/tutorials/get-started/#create-new-service
[Portal de administración de Azure]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Uso de los Centros de notificaciones para	difundir noticias de última hora localizadas]: http://msdn.microsoft.com/es-es/library/jj927172.aspx
[1]: http://msdn.microsoft.com/es-es/library/windowsazure/dn223264.aspx
