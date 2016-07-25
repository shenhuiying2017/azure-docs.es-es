<properties
	pageTitle="Enlaces de Centro de eventos de Funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar los enlaces del Centro de eventos de Azure en Funciones de Azure."
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
	ms.date="05/27/2016"
	ms.author="wesmc"/>

# Enlaces de Centro de eventos de funciones de Azure

En este artículo se explica cómo configurar y codificar enlaces de [Centro de eventos de Azure ](../event-hubs/event-hubs-overview.md) para Funciones de Azure. Funciones de Azure admite enlaces de desencadenador y salida para Centros de eventos de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## Enlace de desencadenador de Centro de eventos de Azure

Un desencadenador de Centro de eventos de Azure puede utilizarse para responder a un evento enviado a una secuencia de eventos del centro de eventos. Debe tener acceso de lectura al centro de eventos para configurar un enlace de desencadenador.

#### function.json para el enlace de desencadenador del Centro de eventos

El archivo *function.json* de un desencadenador del Centro de eventos de Azure especifica las siguientes propiedades:

- `type`: debe establecerse en *eventHubTrigger*.
- `name`: nombre de variable utilizado en el código de función para el mensaje del centro de eventos.
- `direction`: debe establecerse en *in*.
- `path`: el nombre del centro de eventos.
- `connection`: el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres en el que se encuentra el centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **información de conexión** del espacio de nombres, no del propio centro de eventos. Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.

		{
		  "bindings": [
		    {
		      "type": "eventHubTrigger",
		      "name": "myEventHubMessage",
		      "direction": "in",
		      "path": "MyEventHub",
		      "connection": "myEventHubReadConnectionString"
		    }
		  ],
		  "disabled": false
		}

#### Ejemplo de desencadenador C# de Centro de eventos de Azure
 
Con el archivo function.json de ejemplo anterior, el cuerpo del mensaje de evento se registrará con el código de función C# siguiente:
 
	using System;
	
	public static void Run(string myEventHubMessage, TraceWriter log)
	{
	    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
	}

#### Ejemplo de desencadenador Node.js de Centro de eventos de Azure
 
Con el archivo function.json de ejemplo anterior, el cuerpo del mensaje de evento se registrará con el código de función Node.js siguiente:
 
	module.exports = function (context, myEventHubMessage) {
	    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);	
	    context.done();
	};


## Enlace de salida del Centro de eventos de Azure

Un enlace de salida del Centro de eventos de Azure se usa para escribir eventos en una secuencia de eventos del centro de eventos. Debe tener permiso de envío a un centro de eventos para escribir eventos en él.

#### function.json para el enlace de salida del Centro de eventos

El archivo *function.json* del enlace de la salida del Centro de eventos de Azure especifica las siguientes propiedades:

- `type`: debe establecerse en *eventHub*.
- `name`: nombre de variable utilizado en el código de función para el mensaje del centro de eventos.
- `path`: el nombre del centro de eventos.
- `connection`: el nombre de una configuración de aplicación que contiene la cadena de conexión para el espacio de nombres en el que se encuentra el centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **información de conexión** del espacio de nombres, no del propio centro de eventos. Esta cadena de conexión debe tener permisos de envío para enviar el mensaje a la secuencia del Centro de eventos.
- `direction`: debe establecerse en *out*.

	    {
	      "type": "eventHub",
	      "name": "outputEventHubMessage",
	      "path": "myeventhub",
	      "connection": "MyEventHubSend",
	      "direction": "out"
	    }


#### Ejemplo de código de C# del Centro de eventos de Azure para el enlace de salida
 
El siguiente código de función de ejemplo de C# muestra cómo escribir un evento en una secuencia de eventos del Centro de eventos. Este ejemplo representa el enlace de salida del Centro de eventos mostrado anteriormente aplicado a un desencadenador de temporizador de C#.
 
	using System;
	
	public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
	{
	    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
	
	    log.Verbose(msg);   
	    
	    outputEventHubMessage = msg;
	}

#### Ejemplo de código de Node.js del Centro de eventos de Azure para el enlace de salida
 
El siguiente código de función de ejemplo de Node.js muestra cómo escribir un evento en una secuencia de eventos del Centro de eventos. Este ejemplo representa el enlace de salida del Centro de eventos mostrado anteriormente aplicado a un desencadenador de temporizador de Node.js.
 
	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	    
	    if(myTimer.isPastDue)
	    {
	        context.log('TimerTriggerNodeJS1 is running late!');
	    }

	    context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
	    
	    context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
	
	    context.done();
	};

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0713_2016-->