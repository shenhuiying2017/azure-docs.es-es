<properties pageTitle="Envío de notificaciones de inserción a usuarios autenticados" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Envío de notificaciones de inserción a usuarios autenticados

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">Backend .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">Backend JavaScript</a></div>

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo iOS registrado. A diferencia del tutorial anterior [notificaciones de inserción][Introducción a las notificaciones de inserción], en este tutorial se cambia el servicio móvil con el fin de que requiera que un usuario se autentique para que el cliente pueda registrarse en el Centro de notificaciones para el uso de notificaciones de inserción. También se modifica el registro para agregar una etiqueta basada en el identificador de usuario asignado. Por último, se actualiza el código del servidor para enviar la notificación solo al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

+ [Actualización del servicio para que requiera autenticación para el registro]
+ [Actualización de la aplicación para que inicie sesión antes del registro]
+ [Prueba de la aplicación]

Este tutorial es válido para aplicaciones de la Tienda Windows y la Tienda de Windows Phone.

##Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones de inserción usando Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##<a name="register"></a>Actualización del servicio para que requiera autenticación para el registro

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Actualización de la aplicación para que inicie sesión antes del registro

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Actualización del servicio para que requiera autenticación para el registro]: #register
[Actualización de la aplicación para que inicie sesión antes del registro]: #update-app
[Prueba de la aplicación]: #test
[Pasos siguientes]:#next-steps


<!-- URLs. -->
[Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
