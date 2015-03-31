<properties 
	pageTitle="Habilitación de notificaciones de inserción de Apple" 
	description="Siga este tutorial para crear un nuevo servicio mediante los Servicios móviles de Azure." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Habilitación de notificaciones de inserción de Apple

En este tema se muestra cómo habilitar una aplicación iOS para notificaciones de inserción con el Servicio de notificaciones de inserción de Apple (APNS). El certificado obtenido se usa para registrar la aplicación iOS para el uso de notificaciones de inserción en el [Portal de administración de Azure][Portal de administración]. Para obtener el tutorial completo que incluye actualizaciones de la aplicación, consulte [Introducción a las notificaciones de inserción]. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

Ahora ya puede usar este certificado .p12 para habilitar servicios para que se puedan autenticar en APNS y enviar notificaciones de inserción en nombre de la aplicación.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Introducción a las notificaciones de inserción]: /documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[SDK de Servicios móviles]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de administración]: https://manage.windowsazure.com/


<!--HONumber=47-->
