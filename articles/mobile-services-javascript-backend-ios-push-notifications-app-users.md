<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Envío de notificaciones de inserción a usuarios autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone" >Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo iOS registrado. A diferencia del tutorial anterior sobre [notificaciones de inserción][notificaciones de inserción], en este tutorial se cambia su servicio móvil para que requiera que un usuario se autentique para que el cliente iOS pueda registrarse en el centro de notificaciones para el uso de notificaciones de inserción. También se modifica el registro para agregar una etiqueta basada en el identificador de usuario asignado. Finalmente, se actualiza el script del servidor para enviar la notificación solo al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

-   [Actualización del servicio para que requiera autenticación para el registro][Actualización del servicio para que requiera autenticación para el registro]
-   [Actualización de la aplicación para que inicie sesión antes del registro][Actualización de la aplicación para que inicie sesión antes del registro]
-   [Prueba de la aplicación][Prueba de la aplicación]

Este tutorial es válido para aplicaciones de Windows Phone 8.0 y Windows Phone 8.1 ("Silverlight"). Para aplicaciones de la Tienda de Windows Phone 8.1, consulte la [versión para la Tienda Windows de este tema][versión para la Tienda Windows de este tema].

## Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

-   [Introducción a las notificaciones de inserción][notificaciones de inserción]
    Configura la aplicación de ejemplo TodoList para notificaciones de inserción usando Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

## <a name="register"></a>Actualización del servicio para que requiera autenticación para el registro

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users][mobile-services-javascript-backend-push-notifications-app-users]]

1.  Reemplace la función de inserción por el siguiente código y haga clic en **Guardar**:

        function insert(item, user, request) {

                function insert(item, user, request) {
                    request.execute();
                    setTimeout(function() {
                        push.apns.send(null, {
                            alert: "Alert: " + item.text,
                            payload: {
                                "Hey, a new item arrived: '" + item.text + "'"
                            }
                        });
                    }, 2500);
                }

        }

    Este script de inserción utiliza la etiqueta de identificador de usuario para enviar una notificación de inserción (con el texto del elemento insertado) a todos los registros de aplicación de Windows Phone (MPNS) creados por el usuario que inició sesión.

## <a name="update-app"></a>Actualización de la aplicación para que inicie sesión antes del registro

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login][mobile-services-ios-push-notifications-app-users-login]]

## <a name="test"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app][mobile-services-ios-push-notifications-app-users-test-app]]

<!-- Anchors. --> 
<!-- URLs. -->

  [C\# para Tienda Windows]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "Back-end de JavaScript"
  [notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Actualización del servicio para que requiera autenticación para el registro]: #register
  [Actualización de la aplicación para que inicie sesión antes del registro]: #update-app
  [Prueba de la aplicación]: #test
  [versión para la Tienda Windows de este tema]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-ios-get-started-users/
  [mobile-services-javascript-backend-push-notifications-app-users]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
  [mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md
