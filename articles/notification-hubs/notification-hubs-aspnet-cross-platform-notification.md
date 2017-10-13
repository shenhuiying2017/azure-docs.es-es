---
title: "Envío de notificaciones entre plataformas a usuarios con Azure Notification Hubs (ASP.NET)"
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
ms.openlocfilehash: 3c6dde338cb154f0cbe02642e4ff0f81d070aa25
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Envío de notificaciones entre plataformas a los usuarios con Centros de notificaciones
En un tutorial anterior, [Notificación a los usuarios con Notification Hubs], aprendió a insertar notificaciones en todos los dispositivos que están registrados en un usuario autenticado específico. En ese tutorial, se necesitaban varias solicitudes para enviar una notificación a cada plataforma de cliente compatible. Azure Notification Hubs admite plantillas, que le permiten especificar cómo un dispositivo específico desea recibir notificaciones. Este método simplifica el envío de notificaciones entre plataformas. 

En este artículo se muestra cómo aprovechar las plantillas para enviar, en una sola solicitud, una notificación independiente de la plataforma que se dirige a todas las plataformas. Para más información sobre las plantillas, consulte [Introducción a Azure Notification Hubs][Templates].

> [!IMPORTANT]
> Windows Phone 8.1 y versiones anteriores no se admiten en Visual Studio 2017. Para más información, consulte [Compatibilidad y destinatarios de la plataforma de Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Con Notification Hubs, un dispositivo puede registrar varias plantillas con la misma etiqueta. En este caso, un mensaje entrante dirigido a esa etiqueta da lugar a que se entreguen varias notificaciones al dispositivo, una por cada plantilla. Este proceso le permite mostrar el mismo mensaje en varias notificaciones visuales, como distintivo y como notificación del sistema en una aplicación de la Tienda Windows.
> 
> 

Para enviar notificaciones entre plataformas mediante plantillas, haga lo siguiente:

1. En el Explorador de soluciones de Visual Studio, expanda la carpeta **Controladores** y, a continuación, abra el archivo RegisterController.cs.

2. Encuentre el bloque de código en el método **Put** que crea un nuevo registro y reemplace el contenido de `switch` por el código siguiente:
   
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
   
    Este código llama al método específico de la plataforma para crear un registro de plantilla en lugar de un registro nativo. Como los registros de plantilla se obtienen de registros nativos, no es necesario modificar los registros existentes.

3. En el controlador **Notifications**, reemplace el método **sendNotification** por el código siguiente:
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    Este código envía una notificación a todas las plataformas al mismo tiempo, sin que sea necesario especificar una carga nativa. Los Centros de notificaciones crean y entregan la carga correcta a cada dispositivo con el valor de *etiqueta* especificado, como se indica en las plantillas registradas.

4. Publique de nuevo su proyecto de back-end de WebApi.

5. Vuelva a ejecutar la aplicación cliente y, a continuación, compruebe que el registro se ha realizado correctamente.

6. (Opcional) Implemente la aplicación cliente en un segundo dispositivo y, a continuación, ejecute la aplicación.
    Observe que aparecerá una notificación en cada dispositivo.

## <a name="next-steps"></a>Pasos siguientes
Ahora que completó este tutorial, obtenga más información acerca de los Centros de notificaciones y las plantillas en estos temas:

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Introducción a Azure Notification Hubs][Templates]: contiene información más detallada sobre las plantillas.

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificación a los usuarios con Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
