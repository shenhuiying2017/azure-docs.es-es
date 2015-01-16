<properties urlDisplayName="Notify Users xplat aspnet" pageTitle="Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones (ASP.NET)" metaKeywords="" description="Obtenga información acerca de cómo utilizar las plantillas de los centros de notificaciones para enviar, en una sola solicitud, una notificación independiente de plataforma que tenga como destino todas las plataformas." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />
# Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones


En el tutorial anterior, [Notificación a los usuarios con Centros de notificaciones], aprendió a insertar notificaciones en todos los dispositivos que tiene registrado un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Los centros de notificaciones son compatibles con plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Con esto se simplifica el envío de notificaciones entre plataformas. Este tema muestra cómo aprovecha las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Si desea obtener información más detallada sobre las plantillas, consulte [Información general acerca de los Centros de notificaciones de Azure][Templates].

<div class="dev-callout"><b>Nota:</b>
	<p>Los centros de notificaciones permiten que un dispositivo registre varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.</p>
</div>

Lleve a cabo los siguientes pasos para enviar notificaciones entre plataformas mediante plantillas:

1. En el Explorador de soluciones en Visual Studio, expanda la carpeta **Controladores** y, a continuación, abra el archivo RegisterController.cs. 

2. Encuentre el bloque de código en el método **Post** que crea un nuevo registro y reemplace el contenido de "switch" por el código siguiente:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":

                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"msg\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No se deben modificar los registros existentes, dado que los registros de plantilla derivan de los registros nativos.

3. En el controlador **Notifications**, reemplace el método **sendNotification** por el siguiente código:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	Este código envía una notificación a todas las plataformas al mismo tiempo y sin que sea necesario especificar una carga nativa. Los centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor _tag_ proporcionado, tal como se especifica en las plantillas registradas.

4. Publique de nuevo su proyecto de back-end de WebApi.

5. Ejecute la aplicación cliente otra vez y compruebe que el registro se realice correctamente.

6. (Opcional) Implemente la aplicación cliente en un segundo dispositivo y, a continuación, ejecute la aplicación. 

	Observe que aparecerá una notificación en cada dispositivo.

## Pasos siguientes

Ahora que completó este tutorial, obtenga más información acerca de los centros de notificaciones y las plantillas en estos temas:

+ **[Uso de los Centros de notificaciones para enviar noticias de última hora]** <br/>Muestra otro escenario para el uso de las plantillas 

+  **[Información general acerca de los Centros de notificaciones de Azure][Templates]**<br/>Este tema de información general contiene información más detallada sobre las plantillas.

+  **[Procedimientos en los Centros de notificaciones para la Tienda Windows]**<br/>Incluye una referencia al lenguaje de la expresión de plantilla.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Inserción a usuarios de ASP.NET]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Inserción a usuarios de Servicios móviles]: /en-us/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express para Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Portal de administración]: https://manage.windowsazure.com/
[Uso de los Centros de notificaciones para enviar noticias de última hora]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Centros de notificaciones de Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificación a los usuarios con los Centros de notificaciones]: /en-us/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[Plantillas]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Procedimientos en los Centros de notificaciones para la Tienda Windows]: http://msdn.microsoft.com/en-us/library/windowsazure/jj927172.aspx

<!--HONumber=35.1-->
