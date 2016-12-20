---
title: "Envío de notificaciones push seguras con los Centros de notificaciones de Azure y Node.js"
description: "Aprenda a usar los Centros de notificaciones para enviar notificaciones de inserción desde una aplicación Node.js."
keywords: "notificación push,notificaciones push,inserción de node.js,inserción de ios"
services: notification-hubs
documentationcenter: nodejs
author: ysxu
manager: dwrede
editor: 
ms.assetid: ded4749c-6c39-4ff8-b2cf-1927b3e92f93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/25/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40b1c0870bca02fd6d948dfc1f67ba5c76c698aa


---
# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Envío de notificaciones push seguras con los Centros de notificaciones de Azure y Node.js
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

## <a name="overview"></a>Información general
> [!IMPORTANT]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).
> 
> 

Esta guía le mostrará cómo enviar notificaciones push con la ayuda de los Centros de notificaciones de Azure directamente desde una aplicación Node.js. 

Entre los escenarios descritos se incluye el envío de notificaciones push a aplicaciones en las siguientes plataformas:

* Android
* iOS
* Windows Phone
* Plataforma universal de Windows 

Para obtener más información acerca de los centros de notificaciones, consulte la sección [Pasos siguientes](#next) .

## <a name="what-are-notification-hubs"></a>¿Qué son los Centros de notificaciones?
Los Centros de notificaciones de Azure proporcionan una infraestructura multiplataforma escalable de fácil uso para enviar notificaciones push a los dispositivos móviles. Para más información sobre la infraestructura del servicio, consulte la página [Introducción a los centros de notificaciones](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

## <a name="create-a-nodejs-application"></a>Creación de una aplicación Node.js
El primer paso en este tutorial es crear una nueva aplicación Node.js vacía. Para obtener instrucciones sobre cómo crear una aplicación Node.js, vea [Creación e implementación de una aplicación Node.js en un sitio web de Azure][nodejswebsite], [Servicio en la nube de Node.js][Servicio en la nube de Node.js] (mediante Windows PowerShell) o [Sitio web con WebMatrix].

## <a name="configure-your-application-to-use-notification-hubs"></a>Configuración de la aplicación para usar los Centros de notificaciones
Para usar los Centros de notificaciones de Azure tendrá que descargar y usar el [paquete de Azure](https://www.npmjs.com/package/azure)Node.js, que incluye un conjunto de bibliotecas auxiliares que se comunican con los servicios REST de notificación push.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Uso del Administrador de paquetes para Node (NPM) para obtener el paquete
1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) y navegue a la carpeta donde creó la aplicación vacía.
2. Escriba **npm install azure-sb** en la ventana de comandos.
3. Puede ejecutar manualmente el comando **ls** o **dir** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, encontrará el paquete de **Azure** , que contiene las bibliotecas necesarias para el acceso al Centro de notificaciones.

> [!NOTE]
> Para más información sobre la instalación de NPM, consulte el [blog de NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm)oficial. 
> 
> 

### <a name="import-the-module"></a>Importación del módulo
Con un editor de texto, agregue el código siguiente en la parte superior del archivo **server.js** de la aplicación:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Configuración de una conexión de Centro de notificaciones de Azure
El objeto **NotificationHubService** le permite trabajar con los Centros de notificaciones. El código siguiente crea un objeto **NotificationHubService** para el Centro de notificaciones denominado **hubname**. Agréguelo cerca de la parte superior del archivo **server.js** , tras la instrucción para importar el módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

El valor de conexión **connectionstring** se puede obtener en el [Portal de Azure] mediante los siguientes pasos:

1. En el panel de navegación izquierdo, haga clic en **Examinar**.
2. Seleccione **Centros de notificaciones**y, a continuación, elija el centro que desea usar para el ejemplo. Puede consultar el tutorial [Introducción a Centros de notificaciones para aplicaciones de la Tienda Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) si necesita ayuda para crear un nuevo Centro de notificaciones.
3. Seleccione **Configuración**.
4. Haga clic en **Directivas de acceso**. Verá las cadenas de conexión de acceso, tanto las compartidas como las de acceso completo.

![Portal de Azure: Centros de notificaciones](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [!NOTE]
> También puede recuperar la cadena de conexión mediante el cmdlet **Get-AzureSbNamespace** que proporciona [Azure PowerShell](../powershell-install-configure.md) o el comando **azure sb namespace show** con la [Interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-install.md).
> 
> 

## <a name="general-architecture"></a>Arquitectura general
El objeto **NotificationHubService** expone las siguientes instancias de objeto para enviar notificaciones push a dispositivos y aplicaciones específicos:

* **Android**: use el objeto **GcmService**, disponible en **notificationHubService.gcm**
* **iOS**: use el objeto **ApnsService**, al que se puede acceder en **notificationHubService.apns**
* **Windows Phone**: use el objeto **MpnsService**, disponible en **notificationHubService.mpns**
* **Plataforma universal de Windows**: use el objeto **WnsService**, disponible en **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Envío de notificaciones push a aplicaciones de Android
El objeto **GcmService** proporciona un método **send** que se puede usar para enviar notificaciones de inserción a las aplicaciones de Android. El método **send** acepta los siguientes parámetros:

* **Tags** : identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload** : el código JSON del mensaje o la carga útil de la cadena sin formato.
* **Callback** : función de devolución de llamada.

Para más información sobre el formato de carga útil, consulte la sección dedicada a la **carga útil** del documento sobre [implementación del servidor de GCM](http://developer.android.com/google/gcm/server.html#payload) .

El código siguiente usa la instancia **GcmService** expuesta por **NotificationHubService** para enviar una notificación de inserción a todos los clientes registrados.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Envío de notificaciones push a aplicaciones de iOS
Igual que en el caso de las aplicaciones de Android descrito anteriormente, el objeto **ApnsService** proporciona un método **send** que se puede usar para enviar notificaciones de inserción a las aplicaciones iOS. El método **send** acepta los siguientes parámetros:

* **Tags** : identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload** : el código JSON del mensaje o la carga útil de la cadena.
* **Callback** : función de devolución de llamada.

Para más información sobre el formato de carga útil, consulte la sección sobre la **carga útil de notificaciones** del documento [Local and Remote Notification Programming Guide](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) (Guía de programación de notificaciones locales y remotas).

El código siguiente usa la instancia **ApnsService** expuesta por **NotificationHubService** para enviar un mensaje de alerta a todos los clientes:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
         // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Envío de notificaciones push a aplicaciones de Windows Phone
El objeto **MpnsService** proporciona un método **send** que se puede usar para enviar notificaciones de inserción a las aplicaciones de Windows Phone. El método **send** acepta los siguientes parámetros:

* **Tags** : identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload** : carga útil XML del mensaje.
* **TargetName** - `toast` para notificaciones del sistema. `token` para notificaciones de icono.
* **NotificationClass** : prioridad de la notificación. Consulte la sección sobre **elementos de encabezados HTTP** del documento [Pushing Notifications from a Server (Windows Phone)](http://msdn.microsoft.com/library/hh221551.aspx) (Inserción de notificaciones desde un servidor) para obtener los valores válidos.
* **Options** : encabezados de solicitud opcionales.
* **Callback** : función de devolución de llamada.

Para obtener una lista de valores **TargetName**, **NotificationClass** y opciones de encabezado que sean válidos, consulte la página [Pushing Notifications from a Server (Windows Phone)](http://msdn.microsoft.com/library/hh221551.aspx) (Inserción de notificaciones desde un servidor para Windows Phone).

En el ejemplo siguiente se usa la instancia **MpnsService** expuesta por **NotificationHubService** para enviar una notificación de inserción del sistema:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Envío de notificaciones push a aplicaciones de la Plataforma universal de Windows (UWP)
El objeto **WnsService** proporciona un método **send** que se puede usar para enviar notificaciones de inserción a las aplicaciones de la Plataforma universal de Windows.  El método **send** acepta los siguientes parámetros:

* **Tags** : identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes registrados.
* **Payload** : carga útil del mensaje XML.
* **Type** : tipo de notificación.
* **Options** : encabezados de solicitud opcionales.
* **Callback** : función de devolución de llamada.

Para obtener una lista de tipos y encabezados de solicitud válidos, consulte [Encabezados de respuesta y solicitud del servicio de notificaciones de inserción (aplicaciones de Windows en tiempo de ejecución)](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

El código siguiente usa la instancia **WnsService** expuesta por **NotificationHubService** para enviar una notificación de inserción del sistema a una aplicación para UWP:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
         // notification sent
      }
    });

## <a name="next-steps"></a>Pasos siguientes
Los fragmentos de código de los ejemplos anteriores le permiten crear fácilmente la infraestructura del servicio para entregar notificaciones push a una amplia variedad de dispositivos. Ahora que conoce los fundamentos del uso de los Centros de notificaciones con node.js, siga estos vínculos para más información acerca de cómo puede ampliar estas capacidades adicionales.

* Consulte la referencia MSDN [Introducción a los centros de notificaciones](https://msdn.microsoft.com/library/azure/jj927170.aspx).
* Visite el repositorio [SDK de Azure para Node] en GitHub para ver más ejemplos y detalles de implementación.

[SDK de Azure para Node]: https://github.com/WindowsAzure/azure-sdk-for-node
[Next Steps]: #nextsteps
[Qué son los temas y las suscripciones de Service Bus?]: #what-are-service-bus-topics
[Creación de un espacio de nombres de servicio]: #create-a-service-namespace
[Obtención de credenciales de administración predeterminadas para el espacio de nombres]: #obtain-default-credentials
[Creación de una aplicación Node.js]: #Create_a_Nodejs_Application
[Configuración de la aplicación para usar Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
[Creación de un tema]: #How_to_Create_a_Topic
[Creación de suscripciones]: #How_to_Create_Subscriptions
[Envío de mensajes a un tema]: #How_to_Send_Messages_to_a_Topic
[Recepción de mensajes de una suscripción]: #How_to_Receive_Messages_from_a_Subscription
[Actuación ante errores de la aplicación y mensajes que no se pueden leer]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
[Eliminación de temas y suscripciones]: #How_to_Delete_Topics_and_Subscriptions
[1]: #Next_Steps
[Conceptos de tema]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
[Portal de Azure clásico]: http://manage.windowsazure.com
[imagen]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
[2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
[3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
[4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
[5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
[SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
[SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
[Sitio web con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Servicio en la nube de Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Portal de administración anterior]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
[nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Servicio en la nube de Node.js con Storage]: /develop/nodejs/tutorials/web-app-with-storage/
[Aplicación web de Node.js con Storage]: /develop/nodejs/tutorials/web-site-with-storage/
[Portal de Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


