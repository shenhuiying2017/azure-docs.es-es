---
title: Enlaces de Notification Hubs para Azure Functions
description: "Descubra cómo utilizar los enlaces de Azure Notification Hub en Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: cbc22721bbe06fdc5a8a9026b113071e1616bcb8
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="notification-hubs-output-binding-for-azure-functions"></a>Enlace de salida de Notification Hubs para Azure Functions

En este artículo se explica cómo enviar notificaciones Configurar las notificaciones de inserción mediante el uso de enlaces de [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) en Azure Functions. Azure Functions admite los enlaces de salida para Notification Hubs.

Azure Notification Hubs debe estar configurado para el servicio de notificaciones de plataforma (PNS) que desea usar. Para información sobre cómo obtener notificaciones de inserción en la aplicación cliente desde Notification Hubs, consulte [Introducción a Notification Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) y seleccione la plataforma cliente de destino en la lista desplegable que aparece cerca de la parte superior de la página.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example---template"></a>Ejemplo: plantilla

Las notificaciones que envía pueden ser notificaciones nativas o [de plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md). Las notificaciones nativas se envían a una plataforma cliente específica, tal como está configurado en la propiedad `platform` del enlace de salida. Las notificaciones de plantillas pueden enviarse varias plataformas.   

Vea el ejemplo específico del lenguaje:

* [Script de C#: parámetro de salida](#c-script-template-example---out-parameter)
* [Script de C#: asincrónico](#c-script-template-example---asynchronous)
* [Script de C#: JSON](#c-script-template-example---json)
* [Script de C#: tipos de biblioteca](#c-script-template-example---library-types)
* [F#](#f-template-example)
* [JavaScript](#javascript-template-example)

### <a name="c-script-template-example---out-parameter"></a>Ejemplo de plantilla de script de C#: parámetro de salida

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

### <a name="c-script-template-example---asynchronous"></a>Ejemplo de plantilla de script de C#: asincrónico

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

### <a name="c-script-template-example---json"></a>Ejemplo de plantilla de script de C#: JSON

En este ejemplo, se envía una notificación a un [registro de plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene un marcador de posición `message` en la plantilla mediante una cadena JSON válida.

```cs
using System;

public static void Run(string myQueueItem,  out string notification, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
}
```

### <a name="c-script-template-example---library-types"></a>Ejemplo de plantilla de script de C#: tipos de biblioteca

En este ejemplo, se muestra cómo utilizar los tipos definidos en la [biblioteca de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 

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

### <a name="f-template-example"></a>Ejemplo de plantilla de F#

En este ejemplo, se envía una notificación a un [registro de plantillas`message` que contiene ](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) y `location`.

```fsharp
let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
    notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]
```

### <a name="javascript-template-example"></a>Ejemplo de plantilla de JavaScript

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

## <a name="example---apns-native"></a>Ejemplo: nativo de APNS

En este ejemplo de script de C# se muestra cómo enviar una notificación APNS nativa. 

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

## <a name="example---gcm-native"></a>Ejemplo: nativo de GCM

En este ejemplo de script de C# se muestra cómo enviar una notificación GCM nativa. 

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

## <a name="example---wns-native"></a>Ejemplo: nativo de WNS

En este ejemplo de script de C# se muestra cómo usar los tipos definidos en la [biblioteca de Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar una notificación de WNS nativa. 

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

## <a name="attributes"></a>Atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [NotificationHub](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs), que se define en el paquete NuGet [Microsoft.Azure.WebJobs.Extensions.NotificationHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs).

Los parámetros y las propiedades del constructor del atributo se describen en la sección de [configuración](#configuration).

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json* y el atributo `NotificationHub`:

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** |N/D| Debe establecerse en "notificationHub". |
|**dirección** |N/D| Debe establecerse en "out". | 
|**name** |N/D| Nombre de variable usado en el código de función para el mensaje del Centro de notificaciones. |
|**tagExpression** |**TagExpression** | Las expresiones de etiqueta permiten especificar las notificaciones que se entregarán a un conjunto de dispositivos que se registraron para recibir las notificaciones que coincidan con estas expresiones.  Para obtener más información, consulte [Expresiones de etiqueta y enrutamiento](../notification-hubs/notification-hubs-tags-segment-push-message.md). |
|**hubName** | **HubName** | Nombre del recurso del Centro de notificaciones en Azure Portal. |
|**conexión** | **ConnectionStringSetting** | El nombre de una configuración de aplicación que contiene una cadena de conexión de Notification Hubs.  La cadena de conexión se debe establecer en el valor *DefaultFullSharedAccessSignature* de la instancia de Notification Hub. Consulte [Configuración de la cadena de conexión](#connection-string-setup) más adelante en este artículo.|
|**platform** | **Plataforma** | La propiedad de plataforma indica la plataforma cliente a donde se enviará la notificación. De forma predeterminada, si se omite la propiedad de plataforma desde el enlace de salida, las notificaciones de plantilla se pueden usar para tener como destino cualquier plataforma configurada en el centro de notificaciones de Azure. Para obtener más información sobre cómo utilizar plantillas en general para enviar notificaciones multiplataforma con un Centro de notificaciones de Azure, consulte [Templates](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) (Plantillas). Al establecerse, **platform** debe ser uno de los siguientes valores: <ul><li><code>apns</code>: Apple Push Notification Service. Para más información sobre la configuración del centro de notificaciones para APNS y la recepción de la notificación en una aplicación cliente, vea [Envío de notificaciones push a iOS con Azure Notification Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md).</li><li><code>adm</code>: [Amazon Device Messaging](https://developer.amazon.com/device-messaging). Para más información sobre la configuración del centro de notificaciones para ADM y la recepción de la notificación en una aplicación Kindle, vea [Introducción a Notification Hubs para aplicaciones Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md).</li><li><code>gcm</code>: [Google Cloud Messaging](https://developers.google.com/cloud-messaging/). También se admite Firebase Cloud Messaging, que es la nueva versión de GCM. Para más información, vea [Envío de notificaciones push a Android con Azure Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).</li><li><code>wns</code>: [Servicios de notificaciones push de Windows](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) que tienen como destino plataformas Windows. WNS también admite Windows Phone 8.1 y versiones posteriores. Para más información, vea [Introducción a Notification Hubs para aplicaciones de la plataforma universal de Windows](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).</li><li><code>mpns</code>: [Servicio de notificaciones push de Microsoft](https://msdn.microsoft.com/library/windows/apps/ff402558.aspx). Esta plataforma es compatible con Windows Phone 8 y versiones anteriores. Para más información, vea [Envío de notificaciones push con Azure Notification Hubs en Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md).</li></ul> |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="functionjson-file-example"></a>Ejemplo de archivo function.json

A continuación, se muestra un ejemplo de un enlace de Notification Hubs en un archivo *function.json*.

```json
{
  "bindings": [
    {
      "type": "notificationHub",
      "direction": "out",
      "name": "notification",
      "tagExpression": "",
      "hubName": "my-notification-hub",
      "connection": "MyHubConnectionString",
      "platform": "gcm"
    }
  ],
  "disabled": false
}
```

### <a name="connection-string-setup"></a>Configuración de la cadena de conexión

Para usar un enlace de salida de una instancia de Notification Hub, debe configurar la cadena de conexión de la instancia. Puede seleccionar una instancia de Notification Hubs existente o crear una nuevo desde la pestaña *Integrar* de Azure Portal. También puede configurar la cadena de conexión de forma manual. 

Para configurar la cadena de conexión a un Centro de notificaciones existente:

1. Vaya al Centro de notificaciones en [Azure Portal](https://portal.azure.com), elija **Directivas de acceso** y seleccione el botón Copiar situado junto a la directiva **DefaultFullSharedAccessSignature**. Con esto se copia la cadena de conexión de la directiva *DefaultFullSharedAccessSignature* en el Centro de notificaciones. Esta cadena de conexión permite que la función envíe mensajes de notificación a la instancia de Notification Hubs.
    ![Copia de la cadena de conexión del Centro de notificaciones](./media/functions-bindings-notification-hubs/get-notification-hub-connection.png)
1. Vaya a la aplicación de función en Azure Portal, elija **Configuración de la aplicación**, agregue una clave como **MyHubConnectionString,** pegue el elemento *DefaultFullSharedAccessSignature* copiado para la instancia de Notification Hubs como el valor y luego haga clic en **Guardar**.

El nombre de esta configuración de aplicación es lo que va en la configuración de conexión del enlace de salida en *function.json* o el atributo .NET. Consulte la [sección Configuración](#configuration) que apareció anteriormente en este artículo.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)

