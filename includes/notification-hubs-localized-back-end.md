

En su aplicación back-end, ahora tiene que cambiar para enviar notificaciones de plantilla en lugar de cargas nativas. De esta forma, se simplificará el código back-end dado que no es necesario enviar varias cargas para las diferentes plataformas.

Cuando envía notificaciones de plantilla, solo es necesario proporcionar un conjunto de propiedades; en nuestro caso, enviaremos, por ejemplo, el conjunto de propiedades que contiene la versión localizada de las noticias de actualidad:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


En esta sección se muestra cómo enviar notificaciones de dos formas diferentes:

- mediante una aplicación de consola
- mediante un script de Servicios móviles

El código incluido se difunde tanto a los dispositivos de la Tienda Windows como a los iOS, dado que el back-end puede difundir a cualquiera de los dispositivos compatibles.



## Para enviar notificaciones mediante una aplicación de consola de C# ##

Simplemente modificaremos su método *SendNotificationAsync* mediante el envío de una sola notificación de plantilla.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Tenga en cuenta que esta simple llamada entregará la noticia localizada correcta a **todos** los dispositivos, con independencia de la plataforma, puesto que el Centro de notificaciones crea y entrega la carga nativa correcta a todos los dispositivos suscritos a una etiqueta específica.

### Servicios móviles

En su programador de Servicios móviles, sobrescriba el script con:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
Vea cómo en este caso no hay necesidad de enviar varias notificaciones para diferentes configuraciones regionales y plataformas.

<!---HONumber=July15_HO1-->