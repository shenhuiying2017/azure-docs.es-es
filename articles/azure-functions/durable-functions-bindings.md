---
title: 'Enlaces para Durable Functions: Azure'
description: "Aprenda a utilizar desencadenadores y enlaces en la extensión Durable Functions para Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 3be59e32de22e0939ee887fba1d20829f1ef22eb
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Enlaces para Durable Functions (Azure Functions)

La extensión [Durable Functions](durable-functions-overview.md) presenta dos nuevos enlaces de desencadenador que controlan la ejecución de las funciones del orquestador y de actividad. También presenta a un enlace de salida que actúa como un cliente para el tiempo de ejecución de Durable Functions.

## <a name="orchestration-triggers"></a>Desencadenadores de orquestación

El desencadenador de orquestación permite crear funciones de orquestador durables. Este desencadenador admite iniciar nuevas instancias de función del orquestador y reanudar las instancias existentes de función del orquestador que están en "espera" de una tarea.

Al utilizar las herramientas de Visual Studio para Azure Functions, el desencadenador de orquestación se configura usando el atributo .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Al escribir funciones de orquestador en lenguajes de scripting (por ejemplo, en Azure Portal), el desencadenador de orquestación se define mediante el objeto JSON siguiente en la matriz `bindings` del archivo *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`es el nombre de la orquestación. Este es el valor que los clientes tienen que usar cuando deseen iniciar nuevas instancias de esta función del orquestador. Esta propiedad es opcional. Si no se especifica, se utiliza el nombre de la función.
* `version` es una etiqueta de versión de la orquestación. Los clientes que inician una nueva instancia de una orquestación tienen que incluir la correspondiente etiqueta de versión. Esta propiedad es opcional. Si no se especifica, se utiliza la cadena vacía. Para más información sobre el control de versiones, consulte [Control de versiones en Durable Functions](durable-functions-versioning.md).

> [!NOTE]
> Los valores de configuración para las propiedades `orchestration` o `version` no se recomiendan de momento.

Internamente este enlace de desencadenador sondea una serie de colas de la cuenta de almacenamiento predeterminado para la aplicación de la función. Estas colas son detalles de implementación internos de la extensión, esta es la razón de que no se configuren explícitamente en las propiedades de enlace.

### <a name="trigger-behavior"></a>Comportamiento de un desencadenador

Estas son algunas notas acerca del desencadenador de orquestación:

* **Subprocesamiento único**: un único subproceso de distribución se usa para toda la ejecución de función del orquestador en una instancia de host. Por esta razón, es importante asegurarse de que el código de función del orquestador es eficaz y no realiza ninguna E/S. También es importante asegurarse de que este subproceso no realizar ningún trabajo asincrónico excepto cuando se espera en tipos de tareas específicos de Durable Functions.
* **Control de mensajes dudosos**: no hay compatibilidad con mensajes dudosos en los desencadenadores de orquestación.
* **Visibilidad de los mensajes**: los mensajes de desencadenador de orquestación se quitan de la cola y se mantienen invisibles durante un tiempo configurable. La visibilidad de estos mensajes se renueva automáticamente siempre que la aplicación de la función se esté ejecutando y se mantenga correcta.
* **Valores devueltos**: los valores devueltos se serializan en JSON y se conservan en la tabla de historial de orquestación en Azure Table Storage. Estos valores devueltos pueden ser consultados por el enlace de cliente de orquestación que se describe más adelante.

> [!WARNING]
> Las funciones del orquestador nunca deben usar ningún enlace de entrada o salida que no sea el enlace de desencadenador de orquestación. Si lo hacen, existe la posibilidad de que se produzcan problemas con la extensión Durable Task porque esos enlaces pueden no seguir las reglas de E/S y subprocesamiento único.

### <a name="trigger-usage"></a>Uso del desencadenador

El enlace de desencadenador de orquestación admite entradas y salidas. Estas son algunas cosas que hay que saber acerca del control de entradas y salidas:

* **entradas**: las funciones de orquestación admiten solamente [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) como tipo de parámetro. No se admite deserialización de entradas directamente en la firma de función. El código tiene que usar el método [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) para capturar entradas de la función del orquestador. Estas entradas tienen que ser tipos serializables con JSON.
* **salidas**: los desencadenadores de orquestación admiten valores de salida, así como de entrada. El valor devuelto de la función se utiliza para asignar el valor de salida y tiene que ser serializable con JSON. Si una función devuelve `Task` o `void`, un valor `null` se guardará como la salida.

> [!NOTE]
> En este momento solo se admiten los desencadenadores de orquestación en C#.

### <a name="trigger-sample"></a>Ejemplo de desencadenador

El siguiente es un ejemplo del aspecto que podría tener la función del orquestador C# más simple "Hola mundo":

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

La mayoría de las funciones del orquestador llaman a funciones de actividad, por lo que aquí tenemos un ejemplo de "Hola mundo" que muestra cómo llamar a una función de actividad:

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = await context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

## <a name="activity-triggers"></a>Desencadenadores de actividad

El desencadenador de actividad le permite crear funciones que las funciones del orquestador llaman.

Si está utilizando Visual Studio, el desencadenador de actividad se configura mediante el atributo. NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html). 

Si utiliza Azure Portal para el desarrollo, el desencadenador de actividad está definido por el objeto JSON siguiente en el `bindings` matriz de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`es el nombre de la actividad. Este es el valor que utilizan las funciones del orquestador para invocar esta función de actividad. Esta propiedad es opcional. Si no se especifica, se utiliza el nombre de la función.
* `version` es una etiqueta de versión de la actividad. Las funciones del orquestador que invocan una actividad tienen que incluir la correspondiente etiqueta de versión. Esta propiedad es opcional. Si no se especifica, se utiliza la cadena vacía. Para más información, consulte [Control de versiones](durable-functions-versioning.md).

> [!NOTE]
> Los valores de configuración para las propiedades `activity` o `version` no se recomiendan de momento.

Internamente este enlace de desencadenador sondea una cola de la cuenta de almacenamiento predeterminado para la aplicación de la función. Esta cola es un detalle de implementación interno de la extensión, esta es la razón de que no se configure explícitamente en las propiedades de enlace.

### <a name="trigger-behavior"></a>Comportamiento de un desencadenador

Estas son algunas notas acerca del desencadenador de actividad:

* **Subprocesamiento**: a diferencia del desencadenador de orquestación, los desencadenadores de actividad no tienen ninguna restricción en relación a los subprocesos o E/S. Se puede tratar como funciones normales.
* **Control de mensajes dudosos**: no hay compatibilidad con mensajes dudosos en los desencadenadores de actividad.
* **Visibilidad de los mensajes**: los mensajes de desencadenador de actividad se quitan de la cola y se mantienen invisibles durante un tiempo configurable. La visibilidad de estos mensajes se renueva automáticamente siempre que la aplicación de la función se esté ejecutando y se mantenga correcta.
* **Valores devueltos**: los valores devueltos se serializan en JSON y se conservan en la tabla de historial de orquestación en Azure Table Storage.

> [!WARNING]
> El back-end de almacenamiento para las funciones de actividad es un detalle de implementación y el código de usuario no debe interactuar directamente con estas entidades de almacenamiento.

### <a name="trigger-usage"></a>Uso del desencadenador

El enlace de desencadenador de actividad admite entradas y salidas como el desencadenador de orquestación. Estas son algunas cosas que hay que saber acerca del control de entradas y salidas:

* **entradas**: las funciones de actividad usan de forma nativa [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) como tipo de parámetro. De forma alternativa, una función de actividad puede declararse con cualquier tipo de parámetro que se puede serializar con JSON. Cuando usa `DurableActivityContext`, puede llamar a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) para capturar y deserializar la entrada de la función de actividad.
* **salidas**: las funciones de actividad admiten valores de salida, así como de entrada. El valor devuelto de la función se utiliza para asignar el valor de salida y tiene que ser serializable con JSON. Si una función devuelve `Task` o `void`, un valor `null` se guardará como la salida.
* **metadatos**: las funciones de actividad pueden enlazar a un parámetro `string instanceId` para obtener el identificador de instancia de la orquestación primaria.

> [!NOTE]
> Actualmente no se admiten los desencadenadores de actividad en las funciones de Node.js.

### <a name="trigger-sample"></a>Ejemplo de desencadenador

El siguiente es un ejemplo del aspecto que podría tener una función de actividad C# simple "Hola mundo":

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

El tipo de parámetro predeterminado para el enlace `ActivityTriggerAttribute` es `DurableActivityContext`. Sin embargo, los desencadenadores de actividad también admiten enlazar directamente con tipos serializables con JSON (incluidos los tipos primitivos), por lo que la misma función podría simplificarse como sigue:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

## <a name="orchestration-client"></a>Cliente de orquestación

El enlace del cliente de orquestación le permite escribir funciones que interactúan con las funciones del orquestador. Por ejemplo, puede actuar en las instancias de orquestación de las siguientes formas:
* Iniciarlas.
* Consultar su estado.
* Finalizarlas.
* Enviarles eventos mientras se están ejecutando.

Si está utilizando Visual Studio, puede enlazar al cliente de orquestación mediante el atributo. NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html).

Si utiliza lenguajes de scripting (por ejemplo, archivos *.csx*) para desarrollar, el desencadenador de orquestación está definido por el objeto JSON siguiente en la matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub`: se usa en escenarios donde varias aplicaciones de función comparten la misma cuenta de almacenamiento, pero tienen que estar aisladas entre sí. Si no se especifica, se usa el valor predeterminado `host.json`. Este valor tiene que coincidir con el valor usado por las funciones de orquestador de destino.
* `connectionName`: nombre de una configuración de aplicación que contiene una cadena de conexión de cuenta de almacenamiento. La cuenta de almacenamiento representada por esta cadena de conexión tiene que ser la misma que utilizan las funciones del orquestador de destino. Si no se especifica, se utiliza la cadena de conexión de cuenta de almacenamiento predeterminada de la aplicación de función.

> [!NOTE]
> En la mayoría de los casos, se recomienda omitir estas propiedades y confiar en el comportamiento predeterminado.

### <a name="client-usage"></a>Uso del cliente

En funciones C#, habitualmente se enlaza a `DurableOrchestrationClient`, lo que proporciona acceso completo a todas las API de cliente compatibles con Durable Functions. Las API en el objeto de cliente incluyen:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Como alternativa, puede enlazar a `IAsyncCollector<T>` donde `T` es [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) o `JObject`.

Consulte la documentación de la API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) para más información sobre estas operaciones.

### <a name="client-sample-visual-studio-development"></a>Ejemplo de cliente (Desarrollo de Visual Studio)

Aquí tenemos un ejemplo de una función desencadenada por la cola que inicia una orquestación "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Ejemplo de cliente (no de Visual Studio)

Si no está usando Visual Studio para el desarrollo, puede crear el siguiente archivo *function.json*. En este ejemplo se muestra cómo configurar una función desencadenada por la cola que utiliza el enlace del cliente de orquestación durable:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

A continuación se encuentran ejemplos específicos del idioma que inician nuevas instancias de función de orquestador.

#### <a name="c-sample"></a>Ejemplo de C#

El ejemplo siguiente muestra cómo utilizar el enlace de cliente de orquestación para iniciar una nueva instancia de función desde una función de script de C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>Ejemplo de Node.js

El ejemplo siguiente muestra cómo utilizar el enlace de cliente de orquestación durable para iniciar una nueva instancia de función desde una función de Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Pueden encontrar más información acerca de cómo iniciar instancias en [Administración de instancias](durable-functions-instance-management.md).

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga información acerca de los comportamientos de los puntos de control y reproducción](durable-functions-checkpointing-and-replay.md)

