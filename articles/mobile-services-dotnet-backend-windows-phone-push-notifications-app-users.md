<properties 
	pageTitle="Envío de notificaciones de inserción a usuarios autenticados" 
	description="Obtenga información acerca de cómo enviar notificaciones de inserción a específicos" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Envío de notificaciones de inserción a usuarios autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

En este tema se muestra cómo enviar notificaciones de inserción a un usuario autenticado en cualquier dispositivo registrado. A diferencia del tutorial de [notificaciones de inserción][Get started with push notifications] anterior, este tutorial cambia el dispositivo móvil para solicitar que un usuario se autentique antes de que el cliente pueda registrarse con el centro de notificaciones para notificaciones de inserción. El registro también se modifica para agregar una etiqueta basada en el identificador del usuario asignado. Por último, el script de servidor se actualiza para enviar la notificación solamente al usuario autenticado en lugar de a todos los registros.

Este tutorial le guiará en el siguiente proceso:

1. [Actualización del servicio para que requiera autenticación para el registro]
2. [Actualización de la aplicación para que inicie sesión antes del registro]
3. [Prueba de la aplicación]
 
Este tutorial es válido para aplicaciones de Windows Phone 8.0 y Windows Phone 8.1 Silverlight. Para aplicaciones de la Tienda de Windows Phone 8.1, consulte la [versión para la Tienda Windows de este tema](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Introducción a las notificaciones de inserción]<br/>Configura la aplicación de ejemplo TodoList para notificaciones de inserción usando Centros de notificaciones.

Una vez que haya realizado ambos tutoriales, puede impedir que usuarios no autorizados se registren para notificaciones de inserción desde su servicio móvil.

##<a name="register"></a>Actualización del servicio para solicitar autenticación para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##<a name="update-app"></a>Actualización de la aplicación para iniciar sesión antes del registro

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->

<!-- Anchors. -->
[Actualización del servicio para que requiera autenticación para el registro]: #register
[Actualización de la aplicación para que inicie sesión antes del registro]: #update-app
[Prueba de la aplicación]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introducción a la autenticación]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Introducción a las notificaciones de inserción]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=54-->