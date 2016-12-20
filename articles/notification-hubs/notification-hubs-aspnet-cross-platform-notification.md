---
title: "Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones (ASP.NET)"
description: "Obtenga información acerca de cómo utilizar las plantillas de los Centros de notificaciones para enviar, en una sola solicitud, una notificación independiente de plataforma que tenga como destino todas las plataformas."
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0cc620324c75ed6ffee26fe442014d61673ba1e6


---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones
En el tutorial anterior, [Notificación a los usuarios con Centros de notificaciones], aprendió a insertar notificaciones en todos los dispositivos que tiene registrado un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Los Centros de notificaciones son compatibles con plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Con esto se simplifica el envío de notificaciones entre plataformas. Este tema muestra cómo aprovecha las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Para más información sobre las plantillas, vea [Introducción a los Centros de notificaciones de Azure][Plantillas].

> [!NOTE]
> Los Centros de notificaciones permiten que un dispositivo registre varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Esto le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.
> 
> 

Lleve a cabo los siguientes pasos para enviar notificaciones entre plataformas mediante plantillas:

1. En el Explorador de soluciones en Visual Studio, expanda la carpeta **Controladores** y, a continuación, abra el archivo RegisterController.cs.
2. Encuentre el bloque de código en el método **Post**, que crea un registro, y reemplace el contenido del `switch` por el código siguiente:
   
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
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. No se deben modificar los registros existentes, dado que los registros de plantilla derivan de los registros nativos.
3. En el controlador **Notifications**, reemplace el método **sendNotification** por el código siguiente:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Este código envía una notificación a todas las plataformas al mismo tiempo y sin que sea necesario especificar una carga nativa. Los Centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor de *etiqueta* especificado, como se indica en las plantillas registradas.
4. Publique de nuevo su proyecto de back-end de WebApi.
5. Ejecute la aplicación cliente otra vez y compruebe que el registro se realice correctamente.
6. (Opcional) Implemente la aplicación cliente en un segundo dispositivo y, a continuación, ejecute la aplicación.
   
    Observe que aparecerá una notificación en cada dispositivo.

## <a name="next-steps"></a>Pasos siguientes
Ahora que completó este tutorial, obtenga más información acerca de los Centros de notificaciones y las plantillas en estos temas:

* **[Uso de los Centros de notificaciones para enviar noticias de última hora]** <br/>(C# para Tienda Windows / iOS)Muestra otro escenario para el uso de las plantillas
* **[Introducción a los Centros de notificaciones de Azure][Plantillas]**<br/>Este tema de información general contiene información más detallada sobre las plantillas.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Inserción a usuarios de ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Inserción a usuarios de Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express para Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Uso de los Centros de notificaciones para enviar noticias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Centros de notificaciones de Microsoft Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificación a los usuarios con Centros de notificaciones]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Plantillas]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Procedimientos del Centro de notificaciones para la Tienda Windows]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx



<!--HONumber=Nov16_HO3-->


