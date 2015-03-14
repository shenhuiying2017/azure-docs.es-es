<properties 
	pageTitle="Habilitación de Google Cloud Messaging" 
	description="Siga este tutorial para crear un nuevo servicio mediante los Servicios móviles de Azure." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Habilitación de Google Cloud Messaging

En este tema se muestra cómo habilitar una aplicación Android para notificaciones de inserción con Google Cloud Messaging (GCM). La clave de API obtenida se usa para registrar la aplicación Android para notificaciones de inserción en el [Portal de administración de Azure][Portal de administración]. Para obtener el tutorial completo que incluye actualizaciones de la aplicación, consulte [Introducción a las notificaciones de inserción]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ahora ya puede usar el valor de esta clave de API para habilitar servicios para que se puedan autenticar en GCM y enviar notificaciones de inserción en nombre de la aplicación.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[SDK de servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de administración]: https://manage.windowsazure.com/
[Versión para el back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started


<!--HONumber=42-->
