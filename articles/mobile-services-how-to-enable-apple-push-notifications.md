<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Habilitación de notificaciones de inserción de Apple

En este tema se muestra cómo habilitar una aplicación iOS para notificaciones de inserción con el Servicio de notificaciones de inserción de Apple (APNS). El certificado obtenido se usa para registrar la aplicación iOS para el uso de notificaciones de inserción en el [Portal de administración de Azure][Portal de administración de Azure]. Para obtener el tutorial completo que incluye actualizaciones de la aplicación, consulte [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].

[WACOM.INCLUDE [Habilitación de notificaciones de inserción de Apple](../includes/enable-apple-push-notifications.md)]

Ahora ya puede usar este certificado .p12 para habilitar servicios para que se puedan autenticar en APNS y enviar notificaciones de inserción en nombre de la aplicación.

 
 


  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
