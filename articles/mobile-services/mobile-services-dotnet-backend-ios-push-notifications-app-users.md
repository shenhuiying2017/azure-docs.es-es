---
title: Envío de notificaciones de inserción a usuarios autenticados (backend .NET)
description: Obtenga información acerca de cómo enviar notificaciones de inserción a específicos
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

> Para información sobre la versión de aplicaciones móviles equivalente de este tema, consulte la sección sobre [cómo enviar notificaciones push a un usuario autenticado](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#push-user).
> 
> 

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en iOS. Antes de comenzar este tutorial, complete en primer lugar [Introducción a la autenticación] e [Introducción a las notificaciones de inserción].

En este tutorial, se requiere que los usuarios se autentiquen primero, registrar con el centro de notificaciones para las notificaciones de inserción y actualizar scripts de servidor para enviar las notificaciones únicamente a los usuarios autenticados.

## <a name="register"></a>Actualización del servicio para solicitar autenticación para registro
[!INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

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
[Introducción a la autenticación]: mobile-services-dotnet-backend-ios-get-started-users.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_0727_2016-->