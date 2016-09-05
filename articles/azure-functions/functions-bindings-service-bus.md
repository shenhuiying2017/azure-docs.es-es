<properties
	pageTitle="Enlaces y desencadenadores de bus de servicio de funciones de Azure | Microsoft Azure"
	description="Descubra cómo utilizar desencadenadores y enlaces de bus de servicio de Azure en funciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
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
	ms.date="08/22/2016"
	ms.author="chrande; glenga"/>

# Desencadenadores y enlaces de bus de servicio de funciones de Azure para colas y temas

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artículo explica cómo configurar y codificar desencadenadores y enlaces de bus de servicio en funciones de Azure.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a id="sbtrigger"></a> Desencadenador de tema o cola del bus de servicio

#### function.json

El archivo *function.json* especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o el tema, o bien del mensaje de la cola o el tema.
- `queueName`: solo para el desencadenador de cola, el nombre de la cola que se sondea.
- `topicName`: solo para el desencadenador de tema, el nombre del tema que se sondea.
- `subscriptionName`: solo para el desencadenador de tema, el nombre de la suscripción.
- `connection`: nombre de una configuración de aplicación que contiene una cadena de conexión del bus de servicio. La cadena de conexión debe ser para un espacio de nombres del bus de servicio y no estar limitada a una cola o un tema concretos. Si en la cadena de conexión no hay derechos de administración, establezca la propiedad `accessRights`. Si `connection` se queda vacía, el desencadenador o el enlace funcionarán con la cadena de conexión del bus de servicio predeterminada para la aplicación de función, que se especifica mediante la configuración de la aplicación AzureWebJobsServiceBus.
- `accessRights`: especifica los derechos de acceso disponibles para la cadena de conexión. El valor predeterminado es `manage`. Establézcalo en `listen` si utiliza una cadena de conexión que no otorga permisos de administración. De lo contrario, el sistema en tiempo de ejecución de Funciones puede intentar realizar operaciones que requieran derechos de administración y no conseguirlo.
- `type`: debe establecerse en *serviceBusTrigger*.
- `direction`: debe establecerse en *in*.

Ejemplo de *function.json* con un desencadenador de cola del bus de servicio:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### Ejemplo de código de C# que procesa un mensaje de cola del Bus de servicio

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### Ejemplo de código de Node.js que procesa un mensaje de cola del Bus de servicio

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### Tipos admitidos

El mensaje de la cola del Bus de servicio se puede deserializar en cualquiera de los siguientes tipos:

* Object (de JSON)
* string
* byte array
* `BrokeredMessage` (C#)

#### <a id="sbpeeklock"></a> Comportamiento de PeekLock

El sistema en tiempo de ejecución de Funciones recibe un mensaje en el modo `PeekLock` y llama a `Complete` en el mensaje si la función finaliza correctamente, o bien llama a `Abandon` si se produce un error en la función. Si la ejecución de la función dura más que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

#### <a id="sbpoison"></a> Gestión de mensajes dudosos

El Bus de servicio realiza su propio tratamiento de mensajes dudosos, que no se puede controlar ni configurar en el código ni en la configuración de Funciones de Azure.

#### <a id="sbsinglethread"></a> Subprocesamiento único

De forma predeterminada, el sistema en tiempo de ejecución de Funciones procesa simultáneamente varios mensajes en cola. Para indicar al sistema en tiempo de ejecución que procese los mensajes de la cola o del tema de uno en uno, establezca `serviceBus.maxConcurrrentCalls` en 1 en el archivo *host.json*. Para obtener información sobre el archivo *host.json*, consulte [Estructura de carpetas](functions-reference.md#folder-structure) en el artículo de referencia para desarrolladores y busque [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en el repositorio de WebJobs.Script.

## <a id="sboutput"></a> Enlace de salida de tema o cola del bus de servicio

#### function.json

El archivo *function.json* especifica las siguientes propiedades:

- `name`: nombre de la variable utilizado en el código de la función de la cola o el mensaje de la cola.
- `queueName`: solo para el desencadenador de cola, el nombre de la cola que se sondea.
- `topicName`: solo para el desencadenador de tema, el nombre del tema que se sondea.
- `subscriptionName`: solo para el desencadenador de tema, el nombre de la suscripción.
- `connection`: igual que para el desencadenador del bus de servicio.
- `accessRights`: especifica los derechos de acceso disponibles para la cadena de conexión. El valor predeterminado es `manage`. Establézcalo en `send` si utiliza una cadena de conexión que no otorga permisos de administración. De lo contrario, el sistema en tiempo de ejecución de Funciones puede intentar realizar operaciones que requieran derechos de administración, como la creación de colas, y no conseguirlo.
- `type`: debe establecerse en *serviceBus*.
- `direction`: debe establecerse en *out*.

Ejemplo de *function.json* con un desencadenador de temporizador para escribir mensajes de cola del bus de servicio:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Tipos admitidos

Funciones de Azure puede crear un mensaje de cola del Bus de servicio desde cualquiera de los siguientes tipos:

* Object (siempre crea un mensaje JSON, crea el mensaje con un objeto null si el valor es null cuando finaliza la función)
* string (crea un mensaje si el valor no es null cuando termina la función)
* byte array (funciona como una cadena)
* `BrokeredMessage` (C#; funciona como una cadena)

Para crear varios mensajes en una función de C#, puede utilizar `ICollector<T>` o `IAsyncCollector<T>`. Se crea un mensaje al llamar al método `Add`.

#### Ejemplos de código de C# que crean mensajes en cola del Bus de servicio

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
	string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### Ejemplo de código de Node.js que crea un mensajes en cola del Bus de servicio

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## Pasos siguientes

[AZURE.INCLUDE [pasos siguientes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->