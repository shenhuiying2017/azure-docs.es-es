

Las notificaciones push se envían normalmente en un servicio back-end como Aplicaciones móviles o ASP.NET mediante una biblioteca compatible. También puede usar la API de REST directamente para enviar mensajes de notificación si no hay disponible una biblioteca para su back-end. 

A continuación, presentamos una lista de algunos otros tutoriales que podría interesarle revisar para enviar notificaciones:

* Aplicaciones móviles de Azure: para ver un ejemplo de cómo enviar notificaciones desde un back-end de Aplicaciones móviles con Centros de notificaciones, consulte [Incorporación de notificaciones push a la aplicación iOS](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md).  
* ASP.NET : [Uso de los Centros de notificaciones para insertar notificaciones a los usuarios](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md).
* SDK de Java del Centro de notificaciones de Azure: consulte [Uso de los Centros de notificaciones desde Java](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md) para enviar notificaciones desde Java. Esto se probó en Eclipse para el desarrollo de Android.
* PHP: [Uso de los Centros de notificaciones desde PHP](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md).

En la siguiente sección del tutorial, aprenderá a usar la [Interfaz de REST de Centros de notificaciones](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) para enviar el mensaje de notificación directamente en su aplicación. Todos los dispositivos registrados reciben la notificación que envía cualquier dispositivo.  

