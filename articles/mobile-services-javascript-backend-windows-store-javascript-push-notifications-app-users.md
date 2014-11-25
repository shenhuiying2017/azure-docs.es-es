<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Envío de notificaciones de inserción a usuarios autenticados

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="C# para Tienda Windows">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="JavaScript para Tienda Windows" class="current">JavaScript para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/es-es/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/" title="Back-end de .NET">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/"  title="Back-end de JavaScript" class="current">Back-end de JavaScript</a></div>

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado o a cualquier dispositivo registrado. A diferencia del tutorial de [notificaciones de inserción][notificaciones de inserción] anterior, este tutorial cambia el dispositivo móvil para solicitar que un usuario se autentique antes de que el cliente pueda registrarse con el centro de notificaciones para notificaciones de inserción. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el script de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará a través del siguiente proceso:

-   [Actualización del servicio para solicitar autenticación para registro][Actualización del servicio para solicitar autenticación para registro]
-   [Actualización de la aplicación para iniciar sesión antes del registro][Actualización de la aplicación para iniciar sesión antes del registro]
-   [Prueba de la aplicación][Prueba de la aplicación]

Este tutorial es compatible con aplicaciones tanto de la Tienda Windows como de la Tienda de Windows Phone.

## Requisitos previos

Antes de empezar este tutorial, debe haber completado primero estos tutoriales de Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

-   [Introducción a las notificaciones de inserción][notificaciones de inserción]
    Configura la aplicación de ejemplo TodoList para notificaciones de inserción usando centros de notificación.

Después de haber completado ambos tutoriales, puede impedir que los usuarios autenticados se registren para notificaciones de inserción desde el servicio móvil.

## <a name="register"></a>Actualización del servicio para solicitar autenticación para registro

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

1.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Guardar**:

        function insert(item, user, request) {
            // Define a payload for the Windows Store toast notification.
            var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
            '<binding template="ToastText01"><text id="1">' +
            item.text + '</text></binding></visual></toast>';

            // Get the ID of the logged-in user.
            var userId = user.userId;       

            request.execute({
                success: function() {
                    // If the insert succeeds, send a notification to all devices 
                    // registered to the logged-in user as a tag.
                        push.wns.send(userId, payload, 'wns/toast', {
                        success: function(pushResponse) {
                            console.log("Sent push:", pushResponse);
                            request.respond();
                            },              
                            error: function (pushResponse) {
                                    console.log("Error Sending push:", pushResponse);
                                request.respond(500, { error: pushResponse });
                                }
                            });
                        }
                    });
        }

    Este script de inserción usa la etiqueta del identificador de usuario para enviar una notificación de inserción (con el texto del elemento insertado) a todos los registros de aplicaciones de la Tienda Windows creados por el usuario que ha iniciado sesión.

## <a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro

[WACOM.INCLUDE [mobile-services-windows-store-javascript-push-notifications-app-users](../includes/mobile-services-windows-store-javascript-push-notifications-app-users.md)]

## <a name="test"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)]

<!---## <a name="next-steps"> </a>Next steps 

 In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]--> 



  [notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Actualización del servicio para solicitar autenticación para registro]: #register
  [Actualización de la aplicación para iniciar sesión antes del registro]: #update-app
  [Prueba de la aplicación]: #test
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
