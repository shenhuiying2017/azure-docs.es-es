<properties
	pageTitle="Envío de notificaciones push seguras con los Centros de notificaciones de Azure y Node.js"
	description="Aprenda a usar los Centros de notificaciones para enviar notificaciones de inserción desde una aplicación Node.js."
    keywords="notificación push,notificaciones push,inserción de node.js,inserción de ios"
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="wesmc"/>

# Envío de notificaciones push seguras con los Centros de notificaciones de Azure y Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##Información general

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Esta guía le mostrará cómo enviar notificaciones push con la ayuda de los Centros de notificaciones de Azure directamente desde una aplicación Node.js.

Entre los escenarios descritos se incluye el envío de notificaciones push a aplicaciones en las siguientes plataformas:

* Android
* iOS
* Windows Phone
* Plataforma universal de Windows 

Para obtener más información acerca de los centros de notificaciones, consulte la sección [Pasos siguientes](#next).

##¿Qué son los Centros de notificaciones?

Los Centros de notificaciones de Azure proporcionan una infraestructura multiplataforma escalable de fácil uso para enviar notificaciones push a los dispositivos móviles. Para más información sobre la infraestructura del servicio, consulte la página [Introducción a los centros de notificaciones](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Creación de una aplicación Node.js

El primer paso en este tutorial es crear una nueva aplicación Node.js vacía. Si desea instrucciones sobre cómo crear una aplicación Node.js, consulte [Creación de una aplicación web de Node.js en el Servicio de aplicaciones de Azure][nodejswebsite], [Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure][Node.js Cloud Service] (con Windows PowerShell) o [Creación e implementación de una aplicación web Node.js en Azure con WebMatrix].

##Configuración de la aplicación para usar los Centros de notificaciones

Para usar los Centros de notificaciones de Azure tendrá que descargar y usar el [paquete de Azure](https://www.npmjs.com/package/azure) Node.js, que incluye un conjunto de bibliotecas auxiliares que se comunican con los servicios REST de notificación push.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Linux) y vaya a la carpeta donde creó la aplicación vacía.

2.  Escriba **npm install azure** en la ventana de comandos, con ello debe obtener una salida similar a la siguiente (los números de versión pueden variar):

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Puede ejecutar manualmente el comando **ls** o **dir** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, encontrará el paquete de **Azure**, que contiene las bibliotecas necesarias para el acceso al Centro de notificaciones.

>[AZURE.NOTE] Si desea más información acerca de la instalación de NPM consulte el sitio oficial [The npm Blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm).

### Importación del módulo

Con un editor de texto, agregue el código siguiente en la parte superior del archivo **server.js** de la aplicación:

    var azure = require('azure');

### Configuración de una conexión de Centro de notificaciones de Azure

El objeto **NotificationHubService** le permite trabajar con los Centros de notificaciones. El código siguiente crea un objeto **NotificationHubService** para el Centro de notificaciones denominado **hubname**. Agréguelo cerca de la parte superior del archivo **server.js**, tras la instrucción para importar el módulo azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

El valor de conexión **connectionstring** se puede obtener en el [Portal de Azure] mediante los siguientes pasos:

1. En el panel de navegación izquierdo, haga clic en **Examinar**.

2. Seleccione **Centros de notificaciones** y, a continuación, elija el centro que desea usar para el ejemplo. Puede acudir al tutorial [Introducción a Centros de notificaciones para aplicaciones de la Tienda Windows](notification-hubs-windows-store-dotnet-get-started.md) si necesita ayuda para crear un nuevo Centro de notificaciones.

3. Seleccione **Configuración**.

4. Haga clic en **Directivas de acceso**. Verá las cadenas de conexión de acceso, tanto las compartidas como las de acceso completo.

![Portal de Azure: Centros de notificaciones](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Para recuperar la cadena de conexión, también puede usar el cmdlet **Get-AzureSbNamespace** que proporciona **Azure PowerShell** o el comando [azure sb namespace show](../powershell-install-configure.md) con las [Interfaz de la línea de comandos de Azure (CLI de Azure)](../xplat-cli-install.md).

##Arquitectura general

El objeto **NotificationHubService** expone las siguientes instancias de objeto para enviar notificaciones push a dispositivos y aplicaciones específicos:

* **Android**: use el objeto **GcmService**, disponible en **notificationHubService.gcm**
* **iOS**: use el objeto **ApnsService**, al que se puede obtener acceso en **notificationHubService.apns**
* **Windows Phone**: use el objeto **MpnsService**, disponible en **notificationHubService.mpns**
* **Plataforma universal de Windows**: use el objeto **WnsService**, disponible en **notificationHubService.wns**

### Envío de notificaciones push a aplicaciones de Android

El objeto **GcmService** proporciona un método **send** que se puede usar para enviar notificaciones push a las aplicaciones de Android. El método **send** acepta los siguientes parámetros:

* **Tags**: identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload**: el JSON del mensaje o la carga de la cadena sin formato.
* **Callback**: función de devolución de llamada.

Para más información acerca del formato de carga, consulte la sección dedicada a la **carga (payload)** del documento sobre [implementación del servidor de GCM](http://developer.android.com/google/gcm/server.html#payload).

El código siguiente usa la instancia **GcmService** expuesta por **NotificationHubService** para enviar una notificación push a todos los clientes registrados.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envío de notificaciones push a aplicaciones de iOS

Igual que en el caso de las aplicaciones de Android descrito anteriormente, el objeto **ApnsService** proporciona un método **send** que se puede usar para enviar notificaciones push a las aplicaciones de iOS. El método **send** acepta los siguientes parámetros:

* **Tags**: identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload**: el JSON del mensaje o la carga de la cadena.
* **Callback**: función de devolución de llamada.

Para más información acerca del formato de carga, consulte la sección **Apple Push Notification Service** de el documento [Local and Remote Notification Programming Guide](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) (guía de programación de notificaciones locales y remotas).

El código siguiente usa la instancia **ApnsService** expuesta por **NotificationHubService** para enviar un mensaje de alerta a todos los clientes:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Envío de notificaciones push a aplicaciones de Windows Phone

El objeto **MpnsService** proporciona un método **send** que se puede usar para enviar notificaciones push a las aplicaciones de Windows Phone. El método **send** acepta los siguientes parámetros:

* **Tags**: identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes.
* **Payload**: carga XML del mensaje.
* **TargetName**: `toast` para notificaciones del sistema. `token` para notificaciones de icono.
* **NotificationClass**: prioridad de la notificación. Consulte la sección sobre **elementos de encabezados HTTP** del documento [Pushing Notifications from a Server (Windows Phone)](http://msdn.microsoft.com/library/hh221551.aspx) (Introducción de notificaciones push desde un servidor para Windows Phone) para obtener los valores válidos.
* **Options**: encabezados de solicitud opcionales.
* **Callback**: función de devolución de llamada.

Para obtener una lista de valores **TargetName**, **NotificationClass** y opciones de encabezado que sean válidos, consulte la página [Pushing Notifications from a Server (Windows Phone)](http://msdn.microsoft.com/library/hh221551.aspx) (Introducción de notificaciones push desde un servidor para Windows Phone).

El ejemplo siguiente usa la instancia **MpnsService** expuesta por **NotificationHubService** para enviar una notificación push del sistema:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Envío de notificaciones push a aplicaciones de la Plataforma universal de Windows (UWP)

El objeto **WnsService** proporciona un método **send** que se puede usar para enviar notificaciones push a las aplicaciones de la Plataforma universal de Windows. El método **send** acepta los siguientes parámetros:

* **Tags**: identificador de etiqueta. Si no se proporciona ninguna etiqueta, la notificación se enviará a todos los clientes registrados.
* **Payload**: carga del mensaje XML.
* **Type**: tipo de notificación.
* **Options**: encabezados de solicitud opcionales.
* **Callback**: función de devolución de llamada.

Para obtener una lista de tipos y encabezados de solicitud válidos, consulte [Encabezados de respuesta y solicitud del servicio de notificaciones de inserción (aplicaciones de Windows en tiempo de ejecución)](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

El código siguiente usa la instancia **WnsService** expuesta por **NotificationHubService** para enviar una notificación push del sistema:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Pasos siguientes

Los fragmentos de código de los ejemplos anteriores le permiten crear fácilmente la infraestructura del servicio para entregar notificaciones push a una amplia variedad de dispositivos. Ahora que conoce los fundamentos del uso de los Centros de notificaciones con node.js, siga estos vínculos para más información acerca de cómo puede ampliar estas capacidades adicionales.

-   Consulte la referencia MSDN [Introducción a los centros de notificaciones](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Visite el repositorio [SDK de Azure para Node] en GitHub para obtener más ejemplos y detalles de implementación.

  [SDK de Azure para Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Creación e implementación de una aplicación web Node.js en Azure con WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0330_2016-->