<properties 
	pageTitle="Envío de notificaciones de inserción a usuarios autenticados" 
	description="Obtenga información acerca de cómo enviar notificaciones de inserción a específicos" 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title=".NET backend" class="current">Back-end de .NET</a> | <a href="/es-es/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="JavaScript backend">Back-end de JavaScript</a></div>

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo iOS registrado. A diferencia del tutorial anterior [notificación de inserción][Introducción a las notificaciones de inserción], en este tutorial se cambia su servicio móvil para que requiera que un usuario se autentique a fin de que el cliente pueda registrarse en el centro de notificaciones para el uso de notificaciones de inserción. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el código de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

+ [Actualización del servicio para que requiera autenticación para el registro]
+ [Actualización de la aplicación para que inicie sesión antes del registro]
+ [Prueba de la aplicación]

Este tutorial es válido para aplicaciones de la Tienda Windows y la Tienda de Windows Phone.

##Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones de inserción mediante el uso de Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##<a name="register"></a>Actualización del servicio para que requiera autenticación para el registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Actualización de la aplicación para que inicie sesión antes del registro

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

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


<!--HONumber=42-->
