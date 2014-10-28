<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Envío de notificaciones de inserción a usuarios autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript para Tienda Windows" class="current">JavaScript para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/" title="Back-end de .NET" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/"  title="Back-end de JavaScript">Back-end de JavaScript</a></div>

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo registrado. A diferencia del tutorial anterior sobre [notificaciones de inserción][notificaciones de inserción], en este tutorial se cambia su servicio móvil para que requiera que un usuario se autentique para que el cliente pueda registrarse en el centro de notificaciones para notificaciones de inserción. También se modifica el registro para agregar una etiqueta basada en el identificador de usuario asignado. Finalmente, se actualiza el código del servidor para enviar la notificación solo al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

-   [Actualización del servicio para que requiera autenticación para el registro][Actualización del servicio para que requiera autenticación para el registro]
-   [Actualización de la aplicación para que inicie sesión antes del registro][Actualización de la aplicación para que inicie sesión antes del registro]
-   [Prueba de la aplicación][Prueba de la aplicación]

Este tutorial es válido para aplicaciones de la Tienda Windows y la Tienda de Windows Phone.

## Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

-   [Introducción a las notificaciones de inserción][notificaciones de inserción]
    Configura la aplicación de ejemplo TodoList para notificaciones de inserción usando Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

## <a name="register"></a>Actualización del servicio para que requiera autenticación para el registro

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][mobile-services-dotnet-backend-push-notifications-app-users]]

## <a name="update-app"></a>Actualización de la aplicación para que inicie sesión antes del registro

[WACOM.INCLUDE [mobile-services-windows-store-javascript-push-notifications-app-users][mobile-services-windows-store-javascript-push-notifications-app-users]]

## <a name="test"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-windows-test-push-users][mobile-services-windows-test-push-users]]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]--> 

<!-- Anchors. --> 
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/ "Back-end de JavaScript"
  [notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Actualización del servicio para que requiera autenticación para el registro]: #register
  [Actualización de la aplicación para que inicie sesión antes del registro]: #update-app
  [Prueba de la aplicación]: #test
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-windows-store-javascript-push-notifications-app-users]: ../includes/mobile-services-windows-store-javascript-push-notifications-app-users.md
  [mobile-services-windows-test-push-users]: ../includes/mobile-services-windows-test-push-users.md
