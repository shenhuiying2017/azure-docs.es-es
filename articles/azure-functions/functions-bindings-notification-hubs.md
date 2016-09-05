<properties
	pageTitle="Enlace de centros de notificaciones de funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar los enlaces de centros de notificaciones en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="wesmc7777"
	manager="erikre"
	editor=""
	tags=""
	keywords="funciones de azure, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="08/19/2016"
	ms.author="wesmc"/>

# Enlace de salida de centros de notificaciones de Azure de funciones de Azure

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar enlaces de centros de notificaciones de Azure en funciones de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Las funciones pueden enviar notificaciones push mediante un Centro de notificaciones de Azure configurado mediante unas líneas de código tan solo. Sin embargo, el centro de notificaciones debe configurarse para los servicios de notificaciones de plataforma (PNS) que desea utilizar. Para obtener más información sobre cómo configurar centros de notificaciones de Azure y desarrollar aplicaciones cliente que se registren para recibir notificaciones, consulte [Introducción a Centros de notificaciones](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) y haga clic en la plataforma de cliente de destino de la parte superior.

## function.json para enlace de salida de centros de notificaciones de Azure

El archivo function.json ofrece las siguientes propiedades:

- `name`: nombre de variable utilizado en el código de función para el mensaje del centro de notificaciones.
- `type`: se debe establecer en *"notificationHub"*.
- `tagExpression`: las expresiones de etiqueta permiten especificar las notificaciones que se entregarán a un conjunto de dispositivos que se registraron para recibir las notificaciones que coincidan con estas expresiones. Para obtener más información, consulte [Expresiones de etiqueta y enrutamiento](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: nombre del recurso del centro de notificaciones en el Portal de Azure.
- `connection`: esta cadena de conexión debe ser una cadena de conexión de la **configuración de la aplicación** establecida en el valor *DefaultFullSharedAccessSignature* para el centro de notificaciones.
- `direction`: debe establecerse en *out*.
 
Function.json de ejemplo:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

## Configuración de la cadena de conexión del centro de notificaciones de Azure

Para usar un enlace de salida del centro de notificaciones debe configurar la cadena de conexión para el mismo. Esto se puede realizar en la pestaña *Integrar* simplemente seleccionando el centro de notificaciones o creando uno nuevo.

También puede agregar manualmente una cadena de conexión para un centro existente mediante la adición de una cadena de conexión para *DefaultFullSharedAccessSignature* al centro de notificaciones. Esta cadena de conexión le proporciona sus permisos de acceso de función para enviar mensajes de notificación. Al valor de la cadena de conexión *DefaultFullSharedAccessSignature* se puede acceder desde el botón **Claves** de la hoja principal del recurso del centro de notificaciones en el Portal de Azure. Para agregar manualmente una cadena de conexión para su centro, siga estos pasos:

1. En la hoja **Function App** del Portal de Azure, haga clic en **Function App Settings > Go to App Service settings** (Configuración de Function App > Ir a la configuración del Servicio de aplicaciones).

2. Vuelva a la hoja **Configuración** y haga clic en **Configuración de la aplicación**.

3. Desplácese hacia abajo hasta la sección **Cadenas de conexión** y agregue una entrada con nombre para el valor *DefaultFullSharedAccessSignature* para el centro de notificaciones. Cambie el tipo a **Personalizado**.
4. Haga referencia al nombre de la cadena de conexión en los enlaces de salida. De forma parecida al valor **MyHubConnectionString** utilizado en el ejemplo anterior.

## Ejemplo de código de centro de notificaciones de Azure para un desencadenador de temporizador de Node.js 

En este ejemplo, se envía una notificación a un [registro de plantillas](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene `location` y `message`.

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

## Ejemplo de código de centro de notificaciones de Azure para un desencadenador de cola de C#

En este ejemplo, se envía una notificación a un [registro de plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene `message`.


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

En este ejemplo, se envía una notificación a un [registro de plantilla](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contiene `message` mediante una cadena JSON válida.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Info($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

## Ejemplo de código de C# de desencadenador de cola del Centro de notificaciones de Azure que utiliza un tipo de notificación

En este ejemplo, se muestra cómo utilizar el tipo `Notification` definido en la [biblioteca de centros de notificaciones de Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Para utilizar este tipo y la biblioteca, debe cargar un archivo *project.json* para la aplicación de función. El archivo project.json es un archivo de texto JSON que tendrá un aspecto similar al siguiente:

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

Para obtener más información sobre cómo cargar el archivo project.json, consulte el artículo sobre la [carga de un archivo project.json](functions-reference.md#fileupdate).

Ejemplo de código:

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

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->