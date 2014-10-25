<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Cómo usar los centros de notificaciones

En esta guía se indica cómo usar los centros de notificaciones
desde aplicaciones Node.js. Los escenarios abordados incluyen el **envío de notificaciones a aplicaciones de Android, iOS, Windows Phone y la Tienda Windows**. Para obtener más información acerca de los centros de notificaciones, consulte la sección [Pasos siguientes][].

## Tabla de contenido

-   [¿Qué son los centros de notificaciones?][]
-   [Creación de una aplicación Node.js][]
-   [Configuración de la aplicación para usar el centro de notificaciones][]
-   [Direccionamiento del notificaciones][]
-   [Pasos siguientes][]

## <span id="hub"></span></a> ¿Qué son los Centros de notificaciones?

Los centros de notificaciones de Azure proporcionan una infraestructura multiplataforma escalable de fácil uso para enviar notificaciones de inserción a los dispositivos móviles. Para obtener más información, consulte [Centros de notificaciones de Azure][].

## <span id="create"></span></a> Creación de una aplicación Node.js

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure][], [Servicio en la nube Node.js][] (usando Windows PowerShell) o [Sitio web con WebMatrix][].

## <span id="config"></span></a>Configuración de su aplicación para usar el Centro de notificaciones

Para utilizar el centro de notificaciones de Azure, es necesario descargar y usar el
paquete azure para Node.js. Este paquete incluye un conjunto de prácticas bibliotecas que
se comunican con los servicios REST.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería
    devolver la salida siguiente:

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

3.  Puede ejecutar manualmente el comando **ls** o **dir** para comprobar si se ha creado la carpeta
    **node\_modules**. Dentro de dicha carpeta, busque el paquete
    **azure**, que contiene las bibliotecas necesarias para el acceso al
    Centro de notificaciones.

### Importación del módulo

Utilizando un editor de texto, agregue el código siguiente en la parte superior
del archivo **server.js** de la aplicación:

    var azure = require('azure');

### Configuración de una conexión de Centro de notificaciones de Azure

El objeto **NotificationHubService** le permite trabajar con los centros de notificaciones. El
código siguiente crea un objeto **NotificationHubService** para el centro de notificaciones denominado **hubname**. Agréguelo cerca de la
parte superior del archivo **server.js**, tras la instrucción para importar el módulo
azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

El valor de conexión **connectionstring** se puede obtener del Portal de administración de Azure; para ello, realice los siguientes pasos:

1.  En el Portal de administración de Azure, seleccione **Bus de servicio** y, a continuación, seleccione el espacio de nombres que contiene el centro de notificaciones.

2.  Seleccione **CENTROS DE NOTIFICACIONES** y, a continuación, elija el centro que desea usar.

3.  Seleccione **Ver cadena de conexión** en la sección **vista rápida** y copie el valor de la cadena de conexión.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Para recuperar la cadena de conexi&oacute;n, tambi&eacute;n puede usar el cmdlet <b>Get-AzureSbNamespace</b> que proporciona Azure PowerShell o el comando <b>azure sb namespace show</b> con las herramientas de l&iacute;nea de comandos de Azure.</p>

</div>

## <span id="send"></span></a> Envío de notificaciones

El objeto **NotificationHubService** expone las siguientes instancias de objeto para enviar notificaciones a dispositivos y aplicaciones específicos:

-   **Android**: use el objeto **GcmService**, disponible en **notificationHubService.gcm**
-   **iOS**: use el objeto **ApnsService**, al que se puede obtener acceso en **notificationHubService.apns**
-   **Windows Phone**: use el objeto **MpnsService**, disponible en **notificationHubService.mpns**
-   **Aplicaciones de la Tienda Windows**: use el objeto **WnsService**, disponible en **notificationHubService.wns**

### Envío de notificaciones a aplicaciones de Android

El objeto **GcmService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de Android. El método **send** acepta los siguientes parámetros:

-   Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
-   Payload: carga de cadena o JSON del mensaje.
-   Callback: función de devolución de llamada.

Para obtener más información acerca del formato de carga, consulte la sección Payload de [Implementing GCM Server][].

El código siguiente usa la instancia **GcmService** expuesta por **NotificationHubService** para enviar un mensaje a todos los clientes.

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

### Envío de notificaciones a aplicaciones de iOS

El objeto **ApnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de iOS. El método **send** acepta los siguientes parámetros:

-   Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
-   Payload: carga de cadena o JSON del mensaje.
-   Callback: función de devolución de llamada.

Para obtener más información acerca del formato de carga, consulte la sección The Notification Payload de la guía [Local and Push Notification Programming Guide][].

El código siguiente usa la instancia **ApnsService** expuesta por **NotificationHubService** para enviar un mensaje de alerta a todos los clientes.

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### Envío de notificaciones a aplicaciones de Windows Phone

El objeto **MpnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de Windows Phone. El método **send** acepta los siguientes parámetros:

-   Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
-   Payload: carga XML del mensaje.
-   TargetName: 'toast' para notificaciones del sistema. 'token' para notificaciones de icono.
-   NotificationClass: prioridad de la notificación. Consulte la sección HTTP Header Elements del tema [Pushing notifications from a server][] para obtener los valores válidos.
-   Options: encabezados de solicitud opcionales.
-   Callback: función de devolución de llamada.

Para obtener una lista de valores TargetName, NotificationClass y opciones de encabezado válidos, consulte [Pushing notifications from a server][].

El código siguiente usa la instancia **MpnsService** expuesta por **NotificationHubService** para enviar una alerta del sistema:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### Envío de notificaciones a aplicaciones de la Tienda Windows

El objeto **WnsService** proporciona un método **send** que se puede usar para enviar notificaciones a las aplicaciones de la Tienda Windows. El método **send** acepta los siguientes parámetros:

-   Tags: identificador de etiqueta. Si no se proporciona ninguna, la notificación se enviará a todos los clientes.
-   Payload: carga del mensaje XML.
-   Type: tipo de notificación.
-   Options: encabezados de solicitud opcionales.
-   Callback: función de devolución de llamada.

Para obtener una lista de valores Type y encabezados de solicitud válidos, consulte [Push notification service request and response headers][].

El código siguiente usa la instancia **WnsService** expuesta por **NotificationHubService** para enviar una alerta del sistema:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <span id="next"></span></a>Pasos siguientes

Ahora que conoce los fundamentos de uso de los Centros de notificaciones, consulte estos
vínculos para obtener más información.

-   Consulte la referencia de MSDN: [Centros de notificaciones de Azure][].
-   Visite el repositorio del [SDK de Azure para Node.js][] (en inglés) en GitHub.

  [Pasos siguientes]: #next
  [¿Qué son los centros de notificaciones?]: #hub
  [Creación de una aplicación Node.js]: #create
  [Configuración de la aplicación para usar el centro de notificaciones]: #config
  [Direccionamiento del notificaciones]: #send
  [Centros de notificaciones de Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/jj927170.aspx
  [Creación e implementación de una aplicación Node.js en un sitio web de Azure]: /es-es/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servicio en la nube Node.js]: /es-es/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Sitio web con WebMatrix]: /es-es/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Implementing GCM Server]: http://developer.android.com/google/gcm/server.html#payload
  [Local and Push Notification Programming Guide]: http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html
  [Pushing notifications from a server]: http://msdn.microsoft.com/es-es/library/hh221551.aspx
  [Push notification service request and response headers]: http://msdn.microsoft.com/es-es/library/windows/apps/hh465435.aspx
  [SDK de Azure para Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
