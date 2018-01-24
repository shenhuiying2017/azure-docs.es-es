---
title: "Información general sobre Durable Functions: Azure (versión preliminar)"
description: "Introducción a la extensión Durable Functions de Azure Functions."
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
ms.openlocfilehash: f1def2a43edee58bc8b5a33880e206130a1b4687
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="durable-functions-overview-preview"></a>Información general sobre Durable Functions (versión preliminar)

*Durable Functions* es una extensión de [Azure Functions](functions-overview.md) y [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) que le permite escribir funciones con estado en un entorno sin servidor. La extensión administra el estado, establece puntos de control y reinicia en su nombre.

La extensión le permite definir los flujos de trabajo con estado en un nuevo tipo de función que se llama una *función del orquestador*. Estas son algunas de las ventajas de las funciones del orquestador:

* Definen los flujos de trabajo en código. No se necesitan esquemas JSON ni diseñadores.
* Pueden llamar a otras funciones de forma sincrónica y asincrónica. La salida de las funciones llamadas puede guardarse en variables locales.
* Establecen automáticamente puntos de control en su progreso cuando la función espera. El estado local nunca se pierde si el proceso se recicla o se reinicia la máquina virtual.

> [!NOTE]
> Durable Functions se encuentra en versión preliminar y es una extensión avanzada de Azure Functions que no es adecuada para todas las aplicaciones. El resto de este artículo da por supuesto que está muy familiarizado con los conceptos de [Azure Functions](functions-overview.md) y los desafíos que implica el desarrollo de aplicaciones sin servidor.

El caso de uso principal para Durable Functions es simplificar los problemas de coordinación con estado complejos en las aplicaciones sin servidor. Las siguientes secciones describen algunos patrones de aplicación típicos que se pueden beneficiar de Durable Functions.

## <a name="pattern-1-function-chaining"></a>Patrón nº 1: Encadenamiento de funciones

*El encadenamiento de funciones* hace referencia al patrón de ejecución de una secuencia de funciones en un orden concreto. A menudo la salida de una función tiene que aplicarse a la entrada de otra función.

![Diagrama de encadenamiento de funciones](media/durable-functions-overview/function-chaining.png)

Durable Functions le permite implementar este patrón de forma concisa en código.

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

Los valores "F1", "F2" y "F3", "F4" son los nombres de otras funciones en la aplicación de la función. El flujo de control se implementa mediante construcciones de código imperativas normales. Es decir, el código se ejecuta de arriba a abajo y puede implicar semántica de flujo de control del lenguaje ya existente, como instrucciones condicionales y bucles.  La lógica de control de errores puede incluirse en bloques try/catch/finally.

El `ctx` parámetro ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) proporciona métodos para invocar otras funciones por nombre, paso de parámetros y devolución de salida de función. Cada vez que el código llama a `await`, el marco de Durable Functions *establece puntos de control* en el progreso de la instancia actual de la función. Si el proceso o la máquina virtual se recicla a mitad de la ejecución, la instancia de la función se reanuda desde la llamada `await` anterior. Más adelante veremos más sobre este comportamiento de reinicio.

## <a name="pattern-2-fan-outfan-in"></a>Patrón nº 2: Distribución ramificada de salida y de entrada

La *distribución ramificada de salida y entrada* hace referencia al patrón de ejecución en paralelo de varias funciones y a la espera hasta que todas finalicen.  A menudo se realiza algún trabajo de agregación en los resultados devueltos de las funciones.

![Diagrama de la distribución ramificada de salida y de entrada](media/durable-functions-overview/fan-out-fan-in.png)

Con las funciones normales, la distribución ramificada de salida se puede realizar haciendo que la función envíe varios mensajes a una cola. Sin embargo, la distribución ramificada de vuelta es mucho más compleja. Tendría que escribir código para realizar un seguimiento de cuándo finalizan las funciones desencadenadas por la cola y almacenar la salida de la función. La extensión de Durable Functions controla este patrón con código relativamente sencillo.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

El trabajo de distribución ramificada se distribuye en varias instancias de función `F2`, y se realiza un seguimiento del trabajo mediante el uso de una lista dinámica de las tareas. Se llama a la API de .NET `Task.WhenAll` para esperar a que todas las funciones llamadas finalicen. Entonces los resultados de la función `F2` se agregan desde la lista de tareas dinámica y se pasan a la función `F3`.

El establecimiento de puntos de control automáticos que se produce en la llamada `await` en `Task.WhenAll` garantiza que cualquier bloqueo o reinicio a mitad del proceso no requiere un reinicio de cualquiera de las tareas ya completadas.

## <a name="pattern-3-async-http-apis"></a>Patrón nº 3: Las API de HTTP asincrónico

El tercer patrón se refiere en su totalidad al problema de coordinar el estado de las operaciones de larga duración con los clientes externos. Una manera común de implementar este patrón es hacer que una llamada HTTP desencadene la acción de ejecución prolongada y, a continuación, redirigir al cliente a un punto de conexión de estado que pueda sondear para obtener información sobre cuándo se completa la operación.

![Diagrama de la API de HTTP](media/durable-functions-overview/async-http-api.png)

Durable Functions proporciona API integradas que simplifican el código que tiene que escribir para interactuar con las ejecuciones de función de ejecución prolongada. Los [ejemplos](durable-functions-install.md) muestran un comando REST simple que puede usarse para iniciar nuevas instancias de la función del orquestador. Una vez que se inicia una instancia, la extensión expone las API de HTTP de webhook que consultan el estado de la función del orquestador. En el ejemplo siguiente se muestran los comandos REST para iniciar un orquestador y consultar su estado. Para evitar confusiones, se omiten algunos detalles en el ejemplo.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Dado que el estado está administrado por el tiempo de ejecución de Durable Functions, no tendrá que implementar su propio mecanismo de seguimiento de estado.

Aunque la extensión de Durable Functions tiene webhooks integrados para la administración de orquestaciones de larga ejecución, el usuario puede implementar este patrón utilizando sus propios desencadenadores de función (por ejemplo, HTTP, la cola o Event Hub) y el enlace `orchestrationClient`. Por ejemplo, podría utilizar un mensaje de cola para desencadenar la terminación.  O bien, puede usar un desencadenador HTTP protegido por una directiva de autenticación de Azure Active Directory en lugar de webhooks integradas que usen una clave generada para la autenticación. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

El parámetro `starter` [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) es un valor del enlace de salida `orchestrationClient` que forma parte de la extensión de Durable Functions. Proporciona métodos para iniciar, enviar eventos a, terminar y consultar instancias de función de orquestador nueva o existente. En el ejemplo anterior, una función desencadenada por HTTP toma un valor `functionName` de la dirección URL de entrada y pasa ese valor a [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Esta API de enlace, a continuación, devuelve una respuesta que contiene un `Location` encabezado e información adicional acerca de la instancia que se puede utilizar posteriormente para buscar una copia de seguridad del estado de la instancia iniciada o terminarlo.

## <a name="pattern-4-stateful-singletons"></a>Patrón nº 4: Singletons con estado

La mayoría de las funciones tienen un inicio y un final explícitos y no interactúan directamente con los orígenes de eventos externos. Sin embargo, las orquestaciones admiten un patrón de [singleton con estado](durable-functions-singletons.md) que les permite comportarse como [actores](https://en.wikipedia.org/wiki/Actor_model) de confianza en informática distribuida.

El siguiente diagrama muestra una función que se ejecuta en un bucle infinito mientras procesa eventos recibidos de orígenes externos.

![Diagrama de singleton con estado](media/durable-functions-overview/stateful-singleton.png)

Mientras que Durable Functions no es una implementación del modelo de actor, las funciones de orquestador tienen muchas de las mismas características de tiempo de ejecución. Por ejemplo, son de ejecución larga (posiblemente infinita), globales, confiables y su ubicación es transparente; además, tienen estado y un único subproceso. Esto hace que las funciones del orquestador sean útiles para escenarios de tipo "actor".

Las funciones normales son sin estado y, por tanto, no son adecuadas para implementar un patrón singleton con estado. Sin embargo, la extensión de Durable Functions facilita bastante la implementación del patrón singleton con estado. El código siguiente es una función de orquestador simple que implementa un contador.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int counterState = ctx.GetInput<int>();

    string operation = await ctx.WaitForExternalEvent<string>("operation");
    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }

    ctx.ContinueAsNew(counterState);
}
```

Este código es lo que se puede describir como una "orquestación infinita" &mdash; es decir, una orquestación que empieza y no termina nunca. Ejecuta los siguientes pasos:

* Comienza con un valor de entrada en `counterState`.
* Espera indefinidamente un mensaje denominado `operation`.
* Lleva a cabo alguna lógica para actualizar su estado local.
* "Se reinicia" mediante una llamada a `ctx.ContinueAsNew`.
* Vuelve a esperar indefinidamente a la siguiente operación.

## <a name="pattern-5-human-interaction"></a>Patrón nº 5: Interacción humana

Muchos procesos implican algún tipo de interacción humana. El aspecto más difícil de la intervención humana en un proceso automatizado es que las personas no tienen siempre la misma alta disponibilidad y capacidad de respuesta que los servicios en la nube. Los procesos automatizados tienen que tener esto en cuenta y a menudo lo hacen utilizando los tiempos de expiración y la lógica de compensación.

Un ejemplo de un proceso empresarial que implica la interacción humana es un proceso de aprobación. Por ejemplo, para un informe de gastos que supera un importe determinado puede ser necesaria la aprobación de un administrador. Si el administrador no aprueba el informe en un plazo de 72 horas (puede estar de vacaciones), se inicia un proceso de escalado para obtener la aprobación de otra persona (por ejemplo el jefe del administrador).

![Diagrama de interacción humana](media/durable-functions-overview/approval.png)

Este patrón puede implementarse usando una función del orquestador. El orquestador utilizará un [temporizador durable](durable-functions-timers.md) para solicitar la aprobación y escalar en el caso de que se sobrepase el tiempo de expiración. Esperará por un [evento externo](durable-functions-external-events.md), que sería la notificación generada por una interacción humana.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

El temporizador durable se crea mediante una llamada a `ctx.CreateTimer`. `ctx.WaitForExternalEvent` recibe la notificación. Y se llama a `Task.WhenAny` para decidir si se escala (se produce primero el tiempo de expiración) o se aprueba el proceso (se recibe la aprobación antes del tiempo de expiración).

## <a name="the-technology"></a>La tecnología

En segundo plano, las extensiones de Durable Functions se crean a partir de [Durable Task Framework](https://github.com/Azure/durabletask), una biblioteca de código abierto en GitHub para la creación de orquestaciones de tarea durables. Muy similar a como Azure Functions es la evolución sin servidor de Azure WebJobs, Durable Function es la evolución sin servidor de Durable Task Framework. Durable Task Framework se usa mucho dentro de Microsoft y fuera también para automatizar los procesos críticos. Es una opción natural para el entorno de Azure Functions sin servidor.

### <a name="event-sourcing-checkpointing-and-replay"></a>Abastecimiento de eventos, puntos de control y reproducción

La funciones del orquestador mantienen de forma confiable su estado de ejecución usando un patrón de diseño en la nube, conocido como [origen de eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). En lugar de almacenar directamente el estado *actual* de una orquestación, la extensión durable utiliza un almacén de solo anexar para registrar la *serie completa de acciones* realizada por la orquestación de función. Esto tiene muchas ventajas, como mejorar el rendimiento, la escalabilidad y la capacidad de respuesta en comparación con el "volcado" del estado de tiempo de ejecución completo. Otras ventajas incluyen proporcionar coherencia ocasional para los datos transaccionales y mantener el historial y los seguimientos de auditoría completa. Los mismos seguimientos de auditoría habilitan acciones de compensación confiables.

El uso de origen de eventos por esta extensión es transparente. En un segundo plano, el operador `await` en una función del orquestador devuelve el control del subproceso del orquestador al distribuidor de Durable Task Framework. El distribuidor, a continuación, confirma las nuevas acciones que la función de orquestador programó (como llamar a una o más funciones secundarias o programar un temporizador durable) al almacenamiento. Esta acción de confirmación transparente se anexa al *historial de ejecución* de la instancia de orquestación. El historial se guarda en una tabla de almacenamiento. La acción de confirmación, a continuación, agrega mensajes a una cola para programar el trabajo real. En este momento, la función del orquestador se puede descargar de la memoria. Su facturación se detiene si usa el Plan de consumo de Azure Functions.  Cuando hay más trabajo, la función se reinicia y se reconstruye su estado.

Una vez que una función de orquestación recibe más trabajo para realizar (por ejemplo, se recibe un mensaje de respuesta o expira un temporizador durable), el orquestador se reactiva y vuelve a ejecutar toda la función desde el principio con el fin de recompliar el estado local. Si durante esta reproducción el código intenta llamar a una función (o realizar cualquier otro trabajo asincrónico), Durable Task Framework consulta con el *historial de ejecución* de la orquestación actual. Si encuentra que la función de actividad ya se ha ejecutado y ofrece un resultado, reproduce el resultado de esa función y el código del orquestador continúa ejecutándose. Esto sigue ocurriendo hasta que el código de función llega a un punto en donde finaliza o tiene programado nuevo trabajo asincrónico.

### <a name="orchestrator-code-constraints"></a>Restricciones de código del orquestador

El comportamiento de reproducción crea restricciones sobre el tipo de código que se puede escribir en una función del orquestador. Por ejemplo, el código del orquestador tiene que ser determinista, ya que se reproducirá varias veces y tiene que producir siempre el mismo resultado. Encontrará una lista completa de las restricciones en la sección de [restricciones de código del orquestador](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) en el artículo sobre **Puntos de comprobación y reinicio**.

## <a name="language-support"></a>Compatibilidad con idiomas

Actualmente C# es el único lenguaje admitido para Durable Functions. Esto incluye las funciones del orquestador y la funciones de actividad. En el futuro, se agregará compatibilidad para todos los lenguajes que Azure Functions admite. Vea la [lista de problemas en el repositorio de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) de Azure Functions para ver el estado más reciente del trabajo sobre compatibilidad con lenguajes adicionales.

## <a name="monitoring-and-diagnostics"></a>Supervisión y diagnóstico

La extensión de Durable Functions emite automáticamente datos de seguimiento estructurado a [Application Insights](functions-monitoring.md) cuando se configura la aplicación de función con una clave de instrumentación de Application Insights. Estos datos de seguimiento se pueden utilizar para supervisar el comportamiento y el progreso de las orquestaciones.

Este es un ejemplo del aspecto de los eventos de seguimiento de Durable Functions en el portal de Application Insights con [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Resultados de la consulta de Application Insights](media/durable-functions-overview/app-insights-1.png)

Hay muchos datos estructurados útiles empaquetados en el campo `customDimensions` en cada entrada de registro. Este es un ejemplo de una entrada de este tipo expandida por completo.

![El campo customDimensions en la consulta de Application Insights](media/durable-functions-overview/app-insights-2.png)

Debido al comportamiento de la reproducción del distribuidor de Durable Task Framework, podrá ver entradas del registro redundantes para acciones reproducidas. Esto puede ser útil para comprender el comportamiento de la reproducción del motor principal. El artículo sobre [diagnósticos](durable-functions-diagnostics.md) muestra consultas de ejemplo que filtran los registros de reproducción para que pueda ver los registros de "en tiempo real".

## <a name="storage-and-scalability"></a>Almacenamiento y la escalabilidad

La extensión de Durable Functions usa blobs, tablas y colas de Azure Storage para conservar el estado del historial de ejecución y desencadenar la ejecución de la función. Se puede utilizar la cuenta de almacenamiento predeterminada de la aplicación de la función, o puede configurar una cuenta de almacenamiento independiente. Puede ser conveniente usar una cuenta independiente teniendo en cuenta los límites de rendimiento de almacenamiento. El código de orquestador que escribe no necesita (y no debería) interactuar con las entidades de estas cuentas de almacenamiento. Durable Task Framework administra directamente las entidades como un detalle de implementación.

Las funciones del orquestador programan funciones de actividad y reciben sus respuestas a través de mensajes de la cola interna. Cuando una aplicación de la función se ejecuta en el Plan de consumo de Azure Functions, estas colas se supervisan mediante el [controlador de escala de Azure Functions](functions-scale.md#how-the-consumption-plan-works) y se agregan nuevas instancias de proceso según sea necesario. Al escalar horizontalmente a varias máquinas virtuales, una función de orquestador se puede ejecutar en una máquina virtual, mientras que las funciones de actividad a las que llama se ejecutan en varias máquinas virtuales diferentes. Puede encontrar más información sobre el comportamiento de la escala de Durable Functions en [Rendimiento y escala](durable-functions-perf-and-scale.md).

Table Storage se utiliza para almacenar el historial de ejecución para las cuentas del orquestador. Cada vez que una instancia se rehidrata en una máquina virtual concreta, captura su historial de ejecución de Table Storage para poder volver a recompilar su estado local. Una de las cosas prácticas que supone tener el historial disponible en Table Storage es que se puede echar un vistazo y ver el historial de las orquestaciones usando herramientas como el [Explorador de Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Captura de pantalla del Explorador de Azure Storage](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Aunque es sencillo y práctico ver el historial de ejecución en Table Storage, no se fie de esta tabla. Puede cambiar con el progreso de la extensión Durable Functions.

## <a name="known-issues-and-faq"></a>Problemas conocidos y preguntas más frecuentes

En general, todos los problemas conocidos deben encontrarse en la lista de [problemas en GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Si le surge algún problema y no lo encuentra en GitHub, abra un nuevo problema e incluya una descripción detallada del mismo. Incluso si simplemente desea formular una pregunta, no dude en abrir un problema de GitHub y etiquetarlo como una pregunta.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Seguir leyendo la documentación sobre Durable Functions](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Instalación de la extensión Durable Functions y ejemplos](durable-functions-install.md)

