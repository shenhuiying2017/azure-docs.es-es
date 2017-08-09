---
title: Seguimiento de las operaciones personalizadas con el SDK de .NET de Azure Application Insights | Microsoft Docs
description: Seguimiento de las operaciones personalizadas con el SDK de .NET de Azure Application Insights
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/31/2017
ms.author: sergkanz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 883750c939aa3bd605189f513e5ce74642f91709
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="tracking-custom-operations-with-application-insights-net-sdk"></a>Seguimiento de las operaciones personalizadas con el SDK de .NET de Application Insights

Los SDK de Application Insights realizan el seguimiento automático de las solicitudes HTTP de entrada y las llamadas a los servicios dependientes: solicitudes HTTP, consultas SQL, etc. Considere una aplicación que combina varios microservicios. El seguimiento y correlación de las solicitudes y las dependencias le ofrece visibilidad en la capacidad de respuesta de toda la aplicación y la confiabilidad en todos los microservicios. Vamos a expandir gradualmente la lista y a proporcionar a una recopilación automática de otras plataformas y marcos de trabajo conocidos. 

Hay una clase de patrones de aplicación que no se admiten de forma genérica. La supervisión correcta de estos patrones requiere la instrumentación manual de código. En este artículo se tratan algunos patrones que requieren instrumentación manual. Incluir una tarea de procesamiento de cola personalizada o ejecutar una tarea de ejecución prolongada de fondo.

En este documento se proporcionan instrucciones sobre cómo realizar el seguimiento de operaciones personalizadas con el SDK de Application Insights.

El documento es relevante para:
- Application Insights para .NET (también conocido como Base SDK) versión `2.4+`
- Application Insights para aplicaciones web (con ASP.NET) versión `2.4+`
- Application Insights para AspNetCore versión `2.1+`

## <a name="overview"></a>Información general
Por operación entendemos que es una parte lógica del trabajo que se ejecuta en una aplicación. Tiene un nombre, una hora de inicio y una duración, y un contexto de ejecución como el nombre de usuario, las propiedades y el resultado. Si operación `A` fue iniciada por la operación `B`, la operación `B` se establece como elemento primario para `A`.  La operación solo puede tener un elemento primario y muchas operaciones secundarias. Encontrará más información sobre las operaciones y la correlación de telemetría [aquí](application-insights-correlation.md).

En el SDK de .NET de Application Insights se describe la operación mediante la clase abstracta [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/Extensibility/Implementation/OperationTelemetry.cs) y sus descendientes [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/RequestTelemetry.cs) y [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Core/Managed/Shared/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Seguimiento de operaciones de entrada 
El SDK web de Application Insights recopila automáticamente las solicitudes HTTP para las aplicaciones ASP.NET que se ejecutan en la canalización IIS y todas las aplicaciones ASP.NET Core. Hay soluciones admitidas por la comunidad para otras plataformas y marcos de trabajo. Pero si la aplicación no es compatible con ninguna solución admitida por los estándares o la comunidad, puede instrumentarla de forma manual.

Otro ejemplo que requiere seguimiento personalizado es el proceso de trabajo que recibe los elementos de la cola. Para algunas colas, se realiza un seguimiento como dependencia de la llamada para agregar un mensaje a esta cola. Pero la operación general que describe el procesamiento de mensajes no se recopila automáticamente.

Veamos cómo se puede realizar el seguimiento de estas operaciones.

En el nivel superior, la tarea consiste en crear `RequestTelemetry` y establecer propiedades conocidas; una vez completada la operación, se realiza el seguimiento de la telemetría. Se muestra en el siguiente ejemplo.

### <a name="http-request-in-owin-self-hosted-app"></a>Solicitud HTTP en una aplicación autohospedada de Owin
Se sigue el [protocolo HTTP para la correlación](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) y debe esperar recibir los encabezados que describe.

``` C#
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // if there is Request-Id recevied from the upstream service, set telemetry context accordingly
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows to correlate 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry item
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // process request
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // update status code and success as appropriate
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // now it's time to stop operation (and track telemetry)
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // returns root Id from the '|' to first '.' if any
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

El protocolo de correlación de HTTP también declara el encabezado `Correlation-Context`, pero se omite para simplificar el trabajo.

## <a name="queue-instrumentation"></a>Instrumentación de colas
Para la comunicación HTTP, hemos creado un protocolo para pasar los detalles de correlación. Algunos protocolos de colas permiten pasar metadatos adicionales junto con el mensaje y otros no.

### <a name="service-bus-queue"></a>Cola de ServiceBus
La [cola de ServiceBus](../service-bus-messaging/index.md) permite pasar el contenedor de propiedades junto con el mensaje y se usa para pasar el Id. de correlación.

La cola de ServiceBus usa protocolos basados en TCP. Application Insights no realiza un seguimiento automático de las operaciones de cola, por lo que se realiza manualmente. La operación de eliminación de cola es una API de estilo de inserción y no es posible realizar su seguimiento en absoluto.

#### <a name="enqueue"></a>Poner en cola

```C#
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes telemetry item
    // and allows to correlate this operation with its parent and children
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // ServiceBus Queue allows to pass property bag along with the message
    // we will use them to pass our correlation identifiers (and other context)
    // to the consumer
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // set operation.Telemetry Success and ResponseCode here
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proceso
```C#
public async Task Process(BrokeredMessage message)
{
    // once the message is taken from the queue, create ReqeustTelemetry to track its processing
    // it may also make sense to get name from the message
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // get the operation id from the Request-Id (if you follow the 'Http Protocol for Correlation')
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Cola de Azure Storage
En el ejemplo siguiente se muestra cómo realizar el seguimiento de operaciones de [cola de Azure Storage](../storage/storage-dotnet-how-to-use-queues.md) y poner en correlación la telemetría entre el productor, el consumidor y Azure Storage. 

La cola de Azure Storage tiene API HTTP y se realiza el seguimiento de todas las llamadas a la cola por ApplicationInsights DependencyCollector para las solicitudes HTTP.
Asegúrese de que tiene `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` en `applicationInsights.config` o agréguelo mediante programación tal como se describe [aquí](app-insights-api-filtering-sampling.md).

Si configura Application Insights manualmente, asegúrese de crear e inicializar `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` de esta forma:
 
``` C#
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// you can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add Azure Storage endpoint, otherwise you may experience request signature validation issues on the Storage service side
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// do not forget to dispose module during application shutdown
```

También puede poner en correlación el Id. de operación de Application Insights con el RequestID de Azure Storage. Consulte [este artículo](../storage/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing) sobre cómo establecer y obtener el cliente de solicitudes de Storage y el Id. de solicitud de servidor.

#### <a name="enqueue"></a>Poner en cola
Como las colas de Azure Storage admiten API de HTTP, Application Insights realiza el seguimiento automático de todas las operaciones en la cola. En muchos casos, esta instrumentación debería ser suficiente.
Pero para poner en correlación seguimientos en el lado del consumidor con seguimientos del productor, debe pasar un contexto de correlación de forma similar a como se hace en "Protocolo HTTP para la correlación". 

En el ejemplo siguiente, se realiza el seguimiento de la operación `Enqueue` opcional. Permite
 - Poner en correlación los reintentos (si existen): Todos tienen un elemento primario común que es la operación `Enqueue`. En caso contrario, se realiza su seguimiento como elementos secundarios de la solicitud de entrada. Por tanto, si hay varias solicitudes lógicas a la cola, podría ser difícil buscar qué llamada generó los reintentos.
 - Poner en correlación los registros de Azure Storage (si es necesario y cuando sea necesario) con la telemetría de Application Insights.

La operación `Enqueue` es el elemento secundario de una operación "primaria" (por ejemplo, la solicitud HTTP de entrada) y la llamada de dependencia "Http" es el elemento secundario de la operación `Enqueue` y descendiente de la solicitud de entrada.

```C#
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload
    // e.g. if you choose to pass payload serialized to json, it may look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Azure Storage logs and ApplciationInsights telemetry
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}  
```

Si quiere reducir la cantidad de telemetría que notifica la aplicación o no quiere realizar el seguimiento de la operación `enqueue` por otras razones, puede usar directamente la API `Activity`:

- Cree (e inicie) una nueva `Activity` en lugar de iniciar la operación de Application Insights (NO es necesario asignarle propiedades excepto el nombre de la operación).
- Serialice `yourActivity.Id` en la carga del mensaje en lugar de `operation.Telemetry.Id`. También puede usar `Activity.Current.Id`


#### <a name="dequeue"></a>Quitar de la cola
Al igual que `Enqueue`, Application Insights realiza el seguimiento automático de la solicitud HTTP a la cola de Azure Storage. Pero supuestamente la operación `Enqueue` se produce en el contexto principal, por ejemplo un contexto de solicitud "de entrada". Los SDK de Application Insights correlacionan automáticamente esta operación (y su elemento HTTP) con la solicitud primaria y otra telemetría notificada en el mismo ámbito.

La operación `Dequeue` es complicada: el SDK de Application Insights realiza el seguimiento automático de las solicitudes HTTP, pero no conoce el contexto de correlación hasta que se analiza el mensaje. No es posible poner en correlación la solicitud HTTP para obtener el mensaje con el resto de la telemetría.

En muchos casos, puede resultar útil correlacionar la solicitud HTTP a la cola con otros seguimientos. En el ejemplo siguiente se muestra cómo lograrlo.

``` C#
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // if there is a message, we want to correlate Dequeue operation with processing
            // However we will only know what correlation Id to use AFTER we will get it from the message
            // So we will report telemetry once we know the Ids.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // delete message
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // update status code and success as appropriate
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Proceso

En el ejemplo siguiente, se realiza el seguimiento del mensaje "de entrada" de forma similar a cómo se realiza el seguimiento de la solicitud HTTP de entrada.

```C#
public async Task Process(MessagePayload message)
{
    // once the message is dequeued from the queue, create ReqeustTelemetry to track it's processing
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
    // it may also make sense to get name from the message
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // update status code and success as appropriate
        telemetryClient.StopOperation(operation);
    }
}
```

Del mismo modo, se pueden instrumentar otras operaciones de cola. Una operación de lectura se debe instrumentalizar de una manera similar a la de quitar de la cola. La instrumentación de las operaciones de administración de cola no es necesaria. Application Insights realiza el seguimiento de estas operaciones como HTTP y en la mayoría de los casos es suficiente.

Al instrumentar la eliminación de mensajes, asegúrese de establecer los identificadores de la operación (correlación). Como alternativa, puede usar la API `Activity` (no es necesario establecer identificadores de operaciones en los elementos de telemetría, Application Insights lo hace automáticamente):

- Una vez que tiene un elemento de la cola, cree una nueva `Activity`.
- Use `Activity.SetParentId(message.ParentId)` para poner en correlación los registros de consumidor y productor.
- Inicie la `Activity`.
- Realice el seguimiento de las operaciones de eliminación de la cola, proceso y eliminación mediante las aplicaciones auxiliares `Start/StopOperation`. Hágalo desde el mismo flujo de control asincrónico (contexto de ejecución). De esta forma se correlacionan correctamente.
- Después, detenga la `Activity`.
- Use `Start/StopOperation` o llame a la telemetría de seguimiento manualmente 

### <a name="batch-processing"></a>Procesamiento por lotes
Algunas colas permiten que se eliminen varios mensajes de la cola con una solicitud, pero el procesamiento de esos mensajes es supuestamente independiente y pertenece a las distintas operaciones lógicas.
En este caso, no es posible poner en correlación la operación `Dequeue` con el procesamiento de mensajes determinados.

El procesamiento de cada mensaje debe realizarse en su propio flujo de control asincrónico. Puede encontrar más detalles sobre esto en la sección [Seguimiento de dependencias de salida](#outgoing-dependencies-tracking).

## <a name="long-running-background-tasks"></a>Tareas en segundo plano de ejecución prolongada
Algunas aplicaciones inician operaciones de ejecución prolongada que pueden deberse a solicitudes del usuario. Desde la perspectiva del seguimiento o la instrumentación, no es diferente de la instrumentación de solicitudes o dependencias. 

``` C#
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // process task
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // update status code and success as appropriate
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // update status code and success as appropriate
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

En este ejemplo, se usa `telemetryClient.StartOperation` para crear `RequestTelemetry` y rellenar el contexto de correlación. Supongamos que tiene una operación primaria, por ejemplo creada por las solicitudes de entrada que programaron la operación. Siempre que `BackgroundTask` se inicie en el mismo flujo de control asincrónico que una solicitud de entrada, se correlaciona con esa operación primaria. `BackgroundTask` y todos los elementos de telemetría anidados se correlacionarán automáticamente con la solicitud que la causó incluso después de que finalice la solicitud.

Cuando la tarea se inicia desde el subproceso en segundo plano que no tiene ninguna operación (Actividad) asociada a él, `BackgroundTask` no tiene ningún elemento primario. Pero puede tener operaciones anidadas y todos los elementos de telemetría notificados desde la tarea se ponen en correlación con la `RequestTelemetry` creada en `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Seguimiento de dependencias de salida
Es posible que quiera realizar el seguimiento de su propio tipo de "dependencia" o de una operación que no es compatible con Application Insights.

El método `Enqueue` de la cola de ServiceBus o la cola de Azure Storage puede servir como ejemplo de este seguimiento personalizado.

El enfoque general para el seguimiento de dependencias personalizadas es este:
- Llame al método `TelemetryClient.StartOperation` (extensión) que rellena las propiedades de `DependencyTelemetry` necesarias para la correlación y algunas otras (inicio de la marca de tiempo, duración).
- Establezca otras propiedades personalizadas en `DependencyTelemetry`: el nombre y cualquier otro contexto que se necesite.
- Llame a la dependencia y espere a que finalice.
- Detenga la operación con `StopOperation` cuando haya finalizado.
- Controle las excepciones.

`StopOperation` solo detiene la operación que se inició: si la operación en ejecución actual no coincide con la que quiere detener, StopOperation no hace nada. Esto podría suceder si se inician varias operaciones en paralelo en el mismo contexto de ejecución:

```C#
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// this will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Por tanto, debe asegurarse de que siempre llama a `StartOperation` y ejecuta la tarea en su propio contexto:
```C#
public async Task RunMyTaskAsync()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
    try 
    {
        var myTask = await StartMyTaskAsync();
        // update status code and success as appropriate
    }
    catch(...) 
    {
        // update status code and success as appropriate
    }
    finally 
    {
        telemetryClient.StopOperation(operation);
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga los conceptos básicos de la [correlación de telemetría](application-insights-correlation.md) en Application Insights.
- Consulte [modelo de datos](application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- Notifique [eventos y métricas](app-insights-api-custom-events-metrics.md) personalizados a Application Insights.
- Compruebe la [configuración](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) de la recopilación de propiedades estándar de contexto.
- Consulte el [manual del usuario de System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) para ver cómo se correlaciona la telemetría

