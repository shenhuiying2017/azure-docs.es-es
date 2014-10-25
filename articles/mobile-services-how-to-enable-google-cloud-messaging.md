<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Habilitación de Google Cloud Messaging

En este tema se muestra cómo habilitar una aplicación Android para notificaciones de inserción con Google Cloud Messaging (GCM). La clave de API obtenida se usa para registrar la aplicación Android para notificaciones de inserción en el [Portal de administración de Azure][Portal de administración de Azure]. Para obtener el tutorial completo que incluye actualizaciones de la aplicación, consulte [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción].

[WACOM.INCLUDE [Habilitación de GCM][Habilitación de GCM]]

Ahora ya puede usar el valor de esta clave de API para habilitar servicios para que se puedan autenticar en GCM y enviar notificaciones de inserción en nombre de la aplicación.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Habilitación de GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
