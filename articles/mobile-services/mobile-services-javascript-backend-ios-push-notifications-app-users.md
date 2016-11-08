---
title: Envío de notificaciones de inserción a usuarios autenticados en iOS (back-end JavaScript)
description: Obtenga información acerca de cómo enviar notificaciones de inserción a usuarios específicos
services: mobile-services,notification-hubs
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Envío de notificaciones de inserción a usuarios autenticados
[!INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para información sobre la versión de Aplicaciones móviles equivalente de este tema, consulte la sección sobre [cómo enviar notificaciones de inserción a un usuario autenticado mediante etiquetas](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#push-user).
> 
> 

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en iOS. Antes de comenzar este tutorial, complete en primer lugar [Introducción a la autenticación] e [Introducción a las notificaciones de inserción].

En este tutorial, se requiere que los usuarios se autentiquen primero, registrar con el centro de notificaciones para las notificaciones de inserción y actualizar scripts de servidor para enviar las notificaciones únicamente a los usuarios autenticados.

## <a name="register"></a>Actualización del servicio para solicitar autenticación para registro
[!INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Reemplace la función `insert` por el siguiente código y, a continuación, haga clic en **Guardar**. Este script de inserción usa la etiqueta de Id. de usuario para enviar una notificación de inserción a todos los registros de aplicación de iOS desde el usuario que ha iniciado sesión:

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

## <a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro
[!INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Aplicación de prueba
[!INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introducción a la autenticación]: mobile-services-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=AcomDC_0727_2016-->