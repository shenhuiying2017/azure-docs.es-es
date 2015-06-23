<properties 
	pageTitle="Envío de notificaciones de inserción a usuarios autenticados" 
	description="Obtenga información acerca de cómo enviar notificaciones de inserción a específicos" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Información general

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo registrado. A diferencia del tutorial anterior sobre [notificaciones de inserción][Introducción a las notificaciones de inserción], en este tutorial se cambia su servicio móvil para que requiera que un usuario se autentique a fin de que el cliente pueda registrarse en el centro de notificaciones para el uso de notificaciones de inserción. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el script de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.


Este tutorial es válido para aplicaciones Android.

##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Agregar autenticación a la aplicación de Servicios móviles]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones de inserción mediante el uso de los Centros de notificaciones. 

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##Actualización del servicio para solicitar autenticación para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##Actualización de la aplicación para iniciar sesión antes del registro

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](mobile-services-android-push-notifications-app-users.md)] 

##Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Agregar autenticación a la aplicación de Servicios móviles]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Introducción a las notificaciones de inserción]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
 