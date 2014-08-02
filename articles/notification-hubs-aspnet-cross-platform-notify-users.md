<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones
======================================================================================

[Servicios móviles](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Servicios móviles")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

En el tutorial anterior, [Notificación a los usuarios con Centros de notificaciones](/en-us/manage/services/notification-hubs/notify-users-aspnet), aprendió a insertar notificaciones en todos los dispositivos que tiene registrado un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Los centros de notificaciones son compatibles con plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Con esto se simplifica el envío de notificaciones entre plataformas. Este tema muestra cómo aprovecha las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Si desea obtener información más detallada sobre las plantillas, consulte [Información general acerca de los centros de notificaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Nota:**

Los centros de notificaciones permiten que un dispositivo registre varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.

Lleve a cabo los siguientes pasos para enviar notificaciones entre plataformas mediante plantillas:

1.  En el Explorador de soluciones en Visual Studio, expanda la carpeta **Controladores** y, a continuación, abra el archivo RegisterController.cs.

2.  Coloque el bloque de código en el método **Post** que crea un nuevo registro cuando el valor de `updated` es **false** y sustitúyalo por el siguiente código:

		if (!updated)
		{
			switch (platform)
			{
				case "windows":
					var template = @"<toast data-morhtml="true"><visual data-morhtml="true"><binding data-morhtml="true" template=""ToastText01""><text data-morhtml="true" id=""1"">$(message)</text></binding></visual></toast>";
					await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
					break;
				case "ios":
					template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
					await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
					break;
			} 
		}

    Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No se deben modificar los registros existentes, dado que los registros de plantilla derivan de los registros nativos.

3.  Reemplace el método **sendNotification** por el código siguiente:

         // Enviar una notificación entre plataformas mediante plantillas. 
         private async Task sendNotification(string notificationText, string tag)
         {           
                 var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                 await hubClient.SendTemplateNotificationAsync(notification, tag);        
         }

    Este código envía una notificación a todas las plataformas al mismo tiempo y sin que sea necesario especificar una carga nativa. Los centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor *tag* proporcionado, tal como se especifica en las plantillas registradas.

4.  Ejecute la aplicación cliente otra vez y compruebe que el registro se realice correctamente.

5.  (Opcional) Implemente la aplicación cliente en un segundo dispositivo y, a continuación, ejecute la aplicación.

    Observe que aparecerá una notificación en cada dispositivo.

Pasos siguientes
----------------

Ahora que completó este tutorial, obtenga más información acerca de los centros de notificaciones y las plantillas en estos temas:

-   **Uso de los Centros de notificaciones para enviar noticias de última hora ([C\# para Tienda Windows](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    Muestra otro escenario para el uso de las plantillas

-   **[Información general acerca de los centros de notificaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Este tema de información general contiene información más detallada sobre las plantillas.

-   **[Notification Hub How to for Windows Store](http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx)**
    Incluye una referencia al lenguaje de la expresión de plantilla.


