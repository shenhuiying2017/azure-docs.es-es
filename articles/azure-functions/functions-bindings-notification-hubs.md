---
title: Enlace de centros de notificaciones de Azure Functions | Microsoft Docs
description: "Descubra cómo utilizar los enlaces de centros de notificaciones en funciones de Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
keywords: "funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: 0ff0c949-20bf-430b-8dd5-d72b7b6ee6f7
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2016
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fa3d37b963c1bb6b58127b9180cd657d7b1dabcc
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---
# <a name="azure-functions-notification-hub-output-binding"></a>Enlace de salida de centros de notificaciones de Azure de funciones de Azure
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de centros de notificaciones de Azure en funciones de Azure. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Las funciones pueden enviar notificaciones push mediante un Centro de notificaciones de Azure configurado con tan solo unas líneas de código. Sin embargo, el Centro de notificaciones de Azure debe configurarse para los servicios de notificaciones de plataforma (PNS) que desea utilizar. Para obtener más información sobre cómo configurar centros de notificaciones de Azure y desarrollar aplicaciones cliente que se registren para recibir notificaciones, consulte [Introducción a Centros de notificaciones](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) y haga clic en la plataforma de cliente de destino de la parte superior.

Las notificaciones que envía pueden ser nativas o de plantillas. Las notificaciones nativas se envían a una plataforma de notificación específica, tal y como está configurado en la propiedad `platform` del enlace de salida. Las notificaciones de plantillas pueden enviarse varias plataformas.   

## <a name="notification-hub-output-binding-properties"></a>Propiedades del enlace de salida del Centro de notificaciones
El archivo function.json ofrece las siguientes propiedades:

* `name` : nombre de variable utilizado en el código de función para el mensaje del centro de notificaciones.
* `type` : se debe establecer en *"notificationHub"*.
* `tagExpression` : las expresiones de etiqueta permiten especificar las notificaciones que se entregarán a un conjunto de dispositivos que se registraron para recibir las notificaciones que coincidan con estas expresiones.  Para obtener más información, consulte [Expresiones de etiqueta y enrutamiento](../notification-hubs/notification-hubs-tags-segment-push-message.md).
* `hubName` : nombre del recurso del centro de notificaciones en el Portal de Azure.
* `connection` : esta cadena de conexión debe ser una cadena de conexión de la **configuración de la aplicación** establecida en el valor *DefaultFullSharedAccessSignature* para el centro de notificaciones.
* `direction` : debe establecerse en *out*. 
* `platform`: la propiedad de plataforma indica la plataforma de notificación donde se enviará la notificación. Debe ser uno de los siguientes valores: 
  * De forma predeterminada, si se omite la propiedad de plataforma desde el enlace de salida, las notificaciones de plantilla se pueden usar para tener como destino cualquier plataforma configurada en el centro de notificaciones de Azure. Para obtener más información sobre cómo utilizar plantillas en general para enviar notificaciones multiplataforma con un Centro de notificaciones de Azure, consulte [Templates](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) (Plantillas).
  * `apns`: Apple Push Notification Service. Para obtener más información sobre cómo configurar el Centro de notificaciones para APNS y recibir la notificación en una aplicación cliente, consulte [Envío de notificaciones push a iOS con Notification Hubs de Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md). 
  * `adm`: [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para obtener más información sobre cómo configurar el Centro de notificaciones para ADM y recibir la notificación en una aplicación de Kindle, consulte [Introducción a Notification Hubs para aplicaciones Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md). 
  * `gcm`: [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). También se admite Firebase Cloud Messaging, que es la nueva versión de GCM. Para obtener más información sobre cómo configurar el Centro de notificaciones para GCM o FCM y recibir la notificación en una aplicación cliente, consulte [Envío de notificaciones push a Android con Notification Hubs de Azure](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).
  * `wns`: [servicios de notificaciones push de Windows](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) (WNS) que tienen como destino plataformas Windows. WNS también admite Windows Phone 8.1 y versiones posteriores. Para obtener más información sobre cómo configurar el Centro de notificaciones para WNS y recibir la notificación en una aplicación de la Plataforma universal de Windows (UWP), consulte [Introducción a Notification Hubs para aplicaciones de la plataforma universal de Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
  * `mpns`: [servicio de notificaciones push de Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx) (MPNS). Esta plataforma es compatible con Windows Phone 8 y versiones anteriores. Para obtener más información sobre cómo configurar el Centro de notificaciones para MPNS y recibir la notificación en una aplicación de Windows Phone, consulte [Envío de notificaciones push con Notification Hubs de Azure en Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).

Function.json de ejemplo:

```json
{
  "bindings": [
    {
      "name": "notification",
      "type": "notificationHub",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

## <a name="notification-hub-connection-string-setup"></a>Configuración de la cadena de conexión del Centro de notificaciones
Para usar un enlace de salida del Centro de notificaciones, debe configurar la cadena de conexión para él. Puede hacerlo en la pestaña *Integrar* seleccionando el Centro de notificaciones o creando uno. 

También puede agregar manualmente una cadena de conexión para un centro existente mediante la adición de una cadena de conexión para *DefaultFullSharedAccessSignature* al centro de notificaciones. Esta cadena de conexión le proporciona sus permisos de acceso de función para enviar mensajes de notificación. Al valor de la cadena de conexión *DefaultFullSharedAccessSignature* se puede acceder desde el botón **Claves** de la hoja principal del recurso del centro de notificaciones en el Portal de Azure. Para agregar manualmente una cadena de conexión para su centro, siga estos pasos: 

1. En la hoja **Function App** de Azure Portal, haga clic en **Configuración de Function App > Ir a Configuración de App Service**.
2. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.
3. Desplácese hacia abajo hasta la sección **Configuración de aplicación** y agregue una entrada con nombre para el valor *DefaultFullSharedAccessSignature* del Centro de notificaciones.
4. Haga referencia al nombre de la cadena de conexión de Configuración de aplicación en los enlaces de salida. De forma parecida al valor **MyHubConnectionString** utilizado en el ejemplo anterior.

## <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas de APNS con desencadenadores de colas de C#
En este ejemplo, se muestra cómo utilizar los tipos definidos en la [biblioteca de Notification Hubs de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de APNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native APNS notification is ...
    // { "aps": { "alert": "notification message" }}  

    log.Info($"Sending APNS notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{apnsNotificationPayload}");
    await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
}
```

## <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas de GCM con desencadenadores de colas de C#
En este ejemplo, se muestra cómo utilizar los tipos definidos en la [biblioteca de Notification Hubs de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de GCM nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The JSON format for a native GCM notification is ...
    // { "data": { "message": "notification message" }}  

    log.Info($"Sending GCM notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                        user.name + ")\" }}";
    log.Info($"{gcmNotificationPayload}");
    await notification.AddAsync(new GcmNotification(gcmNotificationPayload));        
}
```

## <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificaciones nativas de WNS con desencadenadores de colas de C#
En este ejemplo, se muestra cómo utilizar los tipos definidos en la [biblioteca de Notification Hubs de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de WNS nativa. 

```cs
#r "Microsoft.Azure.NotificationHubs"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.NotificationHubs;
using Newtonsoft.Json;

public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a new user to be processed in the form of a JSON string with 
    // a "name" value.
    //
    // The XML format for a native WNS toast notification is ...
    // <?xml version="1.0" encoding="utf-8"?>
    // <toast>
    //      <visual>
    //     <binding template="ToastText01">
    //       <text id="1">notification message</text>
    //     </binding>
    //   </visual>
    // </toast>

    log.Info($"Sending WNS toast notification of a new user");    
    dynamic user = JsonConvert.DeserializeObject(myQueueItem);    
    string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                    "<toast><visual><binding template=\"ToastText01\">" +
                                        "<text id=\"1\">" + 
                                            "A new user wants to be added (" + user.name + ")" + 
                                        "</text>" +
                                    "</binding></visual></toast>";

    log.Info($"{wnsNotificationPayload}");
    await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));        
}
```

## <a name="template-example-for-nodejs-timer-triggers"></a>Ejemplo de plantillas para desencadenadores de temporizador de Node.js
En este ejemplo, se envía una notificación a un [registro de plantillas`message` que contiene ](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) y `location`.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);  
    context.bindings.notification = {
        location: "Redmond",
        message: "Hello from Node!"
    };
    context.done();
};
```

## <a name="template-example-for-f-timer-triggers"></a>Ejemplo de plantillas para desencadenadores de temporizador de F#
En este ejemplo, se envía una notificación a un [registro de plantillas`message` que contiene ](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) y `location`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

## <a name="template-example-using-an-out-parameter"></a>Ejemplo de plantilla con un parámetro de salida
En este ejemplo, se envía una notificación a un [registro de plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene un marcador de posición `message` en la plantilla.

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = GetTemplateProperties(myQueueItem);
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return templateProperties;
}
```

## <a name="template-example-with-asynchronous-function"></a>Ejemplo de plantilla de función asincrónica
Si va a utilizar código asincrónico, no se admiten parámetros de salida. En este caso, utilice `IAsyncCollector` para devolver la notificación de plantillas. El código siguiente es un ejemplo asincrónico del anterior. 

```cs
using System;
using System.Threading.Tasks;
using System.Collections.Generic;

public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    log.Info($"Sending Template Notification to Notification Hub");
    await notification.AddAsync(GetTemplateProperties(myQueueItem));    
}

private static IDictionary<string, string> GetTemplateProperties(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["user"] = "A new user wants to be added : " + message;
    return templateProperties;
}
```

## <a name="template-example-using-json"></a>Ejemplo de plantilla con JSON
En este ejemplo, se envía una notificación a un [registro de plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene un marcador de posición `message` en la plantilla mediante una cadena JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

## <a name="template-example-using-notification-hubs-library-types"></a>Ejemplo de plantilla con tipos de biblioteca de Notification Hubs
En este ejemplo, se muestra cómo utilizar los tipos definidos en la [biblioteca de Notification Hubs de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

```cs
#r "Microsoft.Azure.NotificationHubs"

using System;
using System.Threading.Tasks;
using Microsoft.Azure.NotificationHubs;

public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
{
   log.Info($"C# Queue trigger function processed: {myQueueItem}");
   notification = GetTemplateNotification(myQueueItem);
}

private static TemplateNotification GetTemplateNotification(string message)
{
    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
    templateProperties["message"] = message;
    return new TemplateNotification(templateProperties);
}
```

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


