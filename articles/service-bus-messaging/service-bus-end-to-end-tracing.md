---
title: "Seguimiento y diagnóstico de un extremo a otro de Azure Service Bus | Microsoft Docs"
description: "Información general del diagnóstico y el seguimiento de un extremo a otro del cliente de Service Bus"
services: service-bus-messaging
documentationcenter: 
author: lmolkova
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Seguimiento y correlación distribuidos del servicio de mensajería de Service Bus

Uno de los problemas comunes en el desarrollo de microservicios es la capacidad de realizar un seguimiento de las operaciones de un cliente a través de todos los servicios que intervienen en el proceso. Es útil para la realizar depuraciones, análisis de rendimiento, pruebas A/B y otros escenarios de diagnóstico típicos.
Una parte de este problema es el seguimiento de elementos de trabajo lógicos. Asimismo, incluye los resultados y latencia del procesamiento de mensajes y las llamadas de dependencia externas. Otra parte del problema es la correlación de estos eventos de diagnóstico más allá de los límites del proceso.

Cuando un productor envía un mensaje a través de una cola, normalmente se produce en el ámbito de cualquier otra operación lógica que inició algún otro cliente o servicio. Igualmente, el consumidor continúa con la misma operación después de recibir un mensaje. Tanto el productor como el consumidor (y otros servicios que procesan la operación) emiten eventos de telemetría para hacer un seguimiento del flujo de la operación y su resultado. Para poder correlacionar estos eventos y realizar un seguimiento de la operación de un extremo a otro, cada servicio que notifica la telemetría debe marcar todos los eventos con un contexto de seguimiento.

El servicio de mensajería de Microsoft Azure Service Bus ha definido las propiedades de carga que deben usar los productores y consumidores para poder pasar estos contextos de seguimiento.
El protocolo se basa en el [protocolo de correlación HTTP](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Nombre de propiedad        | DESCRIPCIÓN                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Identificador único de una llamada externa del productor a la cola. Vea [Request-Id in HTTP protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) (Request-Id en el protocolo HTTP) para obtener el razonamiento, las consideraciones y el formato. |
|  Correlation-Context | Contexto de la operación, que se propaga a través de todos los servicios que intervienen en el proceso de la operación. Para obtener más información, vea [Correlation-Context in HTTP protocol](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) (Correlation-Context en el protocolo HTTP). |

## <a name="service-bus-net-client-auto-tracing"></a>Seguimiento automático del cliente .NET de Service Bus

A partir de la versión 3.0.0, el [Cliente de Microsoft Azure ServiceBus para .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) proporciona varios puntos de instrumentación de seguimiento que se pueden enlazar mediante los sistemas de seguimiento o los fragmentos de código de cliente.
La instrumentación permite realizar un seguimiento de todas las llamadas al servicio de mensajería de Service Bus desde el lado del cliente. Si se realiza el procesamiento de mensajes con el [patrón del controlador de mensajes](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), el procesamiento de mensajes también se instrumentará.

### <a name="tracking-with-azure-application-insights"></a>Realizar un seguimiento con Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) proporciona funcionalidades de supervisión de alto rendimiento entre las que se incluyen las solicitudes automágicas o el seguimiento de dependencias.

Dependiendo del tipo de proyecto, instale el SDK de Application Insights:
- Versión 2.5-beta2 o superior de [ASP.NET](../application-insights/app-insights-asp-net.md).
- Versión 2.2.0-beta2 o superior de [ASP.NET Core](../application-insights/app-insights-asp-net-core.md).
Estos vínculos proporcionan detalles sobre la instalación del SDK, la creación de recursos y la configuración del SDK (si fuera necesario). Para aplicaciones que no sean de ASP.NET, consulte el artículo [Azure Application Insights for Console Applications](../application-insights/application-insights-console.md) (Azure Application Insights para las aplicaciones de consola).

Si usa un [patrón de controlador de mensajes](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) para procesar mensajes, ya ha terminado: se realizará el seguimiento automático de todas las llamadas de Service Bus que se hayan hecho mediante el servicio y se correlacionarán con otros elementos de telemetría. En caso contrario, consulte el siguiente ejemplo para saber cómo realizar el seguimiento manual del procesamiento de mensajes.

#### <a name="trace-message-processing"></a>Seguimiento del procesamiento de mensajes

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

En este ejemplo, `RequestTelemetry` se notifica en cada mensaje procesado e indica la marca de tiempo, la duración y el resultado (correcto). La telemetría también tiene un conjunto de propiedades de correlación.
Igualmente, los seguimientos anidados y las excepciones que se notifican durante el procesamiento de mensajes también se marcan con propiedades de correlación que se representan como "elementos secundarios" de `RequestTelemetry`.

Si llama a componentes externos compatibles durante el procesamiento de mensajes, también se seguirán y correlacionarán de forma automágica. Consulte [Seguimiento de las operaciones personalizadas con el SDK de .NET para Application Insights](../application-insights/application-insights-custom-operations-tracking.md), si quiere obtener más información sobre el seguimiento y la correlación manuales.

### <a name="tracking-without-tracing-system"></a>Realizar seguimientos sin sistema de seguimiento
En caso de que el sistema de seguimiento no admita la función de seguimiento automágico de llamadas de Service Bus, puede hacer que un sistema de seguimiento o la aplicación sean compatibles. En esta sección se describen los eventos de diagnóstico que envió el cliente .NET de Service Bus.  

El cliente .NET de Service Bus se instrumentó mediante las primitivas de seguimiento de .NET [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) y [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` actúa como un contexto de seguimiento mientras que `DiagnosticSource` es un mecanismo de notificación. 

Si no hay ningún agente de escucha para los eventos DiagnosticSource, la instrumentación estará desactivada y el costo de instrumentación será de cero. DiagnosticSource proporciona todo el control al agente de escucha:
- El agente de escucha se encarga de controlar qué orígenes y eventos hay que escuchar.
- El agente de escucha controla el muestreo y la velocidad de los eventos.
- Los eventos se envían con una carga que proporciona un contexto completo para que pueda obtener acceso y modificar el objeto de mensaje durante el evento.

Familiarícese con [DiagnosticSource User Guide](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) (Guía de usuario de DiagnosticSource) antes de continuar con la implementación.

Vamos a crear un agente de escucha de eventos de Service Bus en la aplicación de ASP.NET Core que escriba registros con Microsoft.Extension.Logger.
Este usa la biblioteca [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) para suscribirse a DiagnosticSource (aunque también es fácil suscribirse a DiagnosticSource sin este elemento).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

En este ejemplo, el agente de escucha registra la duración, el resultado, el identificador único y la hora de inicio de cada operación de Service Bus.

#### <a name="events"></a>Eventos

En cada operación se envían dos eventos: "Start" y "Stop". Lo más probablemente es que solo le interesen los eventos "Stop". Estos, igual que sucede con las propiedades de actividad, proporcionan el resultado de la operación, la hora de inicio y la duración.

La carga del evento proporciona un agente de escucha con el contexto de la operación y replica los parámetros de entrada de la API y el valor devuelto. Asimismo, la carga del evento "Stop" tiene todas las propiedades de carga del evento "Start", por lo que puede hacer caso omiso de este último.

Todos los eventos tienen propiedades "Entity" y "Endpoint", pero se han omitido en la tabla que se muestra a continuación.
  * `string Entity`: nombre de la entidad (cola, tema, etc.)
  * `Uri Endpoint`: dirección URL del punto de conexión de Service Bus

Cada evento "Stop" tiene la propiedad `Status` y la operación asincrónica `TaskStatus` con la que se completó, pero también se ha omitido en la tabla siguiente para simplificar el trabajo.

Esta es la lista completa de operaciones instrumentadas:

| Nombre de la operación | API de seguimiento | Propiedades de carga específicas|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | Mensajes IList<Message>: lista de mensajes que se van a enviar. |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Mensaje Message: mensaje que se va a procesar.<br/>DateTimeOffset ScheduleEnqueueTimeUtc: desplazamiento del mensaje programado.<br/>long SequenceNumber: número de secuencia del mensaje programado (carga del evento "Stop"). |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | long SequenceNumber: número de secuencia del mensaje que se va a cancelar. | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount: número máximo de mensajes que se pueden recibir.<br/>Mensajes IList<Message>: lista de mensajes recibidos (carga del evento "Stop"). |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber: punto de partida desde el que se va a examinar un lote de mensajes.<br/>int RequestedMessageCount: número de mensajes que desea recuperar.<br/>Mensajes IList<Message>: lista de mensajes recibidos (carga del evento "Stop"). |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers: lista que contiene los números de secuencia que se van a recibir.<br/>Mensajes IList<Message>: lista de mensajes recibidos (carga del evento "Stop"). |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens: lista que contiene los tokens de bloqueo de los mensajes que se van a completar.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | string LockToken: token de bloqueo del mensaje que se debe cancelar. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | string LockToken: token de bloqueo del mensaje que se debe aplazar. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | string LockToken: token de bloqueo del mensaje que se debe establecer como fallido. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | string LockToken: token de bloqueo del mensaje al que hay que renovar el bloqueo.<br/>DateTime LockedUntilUtc: nueva fecha y hora de expiración en formato UTC del token de bloqueo (carga del evento "Stop").|
| Microsoft.Azure.ServiceBus.Process | Función de lambda del controlador de mensajes incluida en [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Mensaje Message: mensaje que se va a procesar. |
| Microsoft.Azure.ServiceBus.ProcessSession | Función de lambda del controlador de la sesión de mensajes incluida en [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Mensaje Message: mensaje que se va a procesar.<br/>IMessageSession Session: sesión que se va a procesar. |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription Rule: descripción de regla que proporciona la regla que se va a agregar. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | string RuleName: nombre de la regla que se va a quitar. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> Rules: todas las reglas asociadas a la suscripción (solo carga "Stop"). |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | string SessionId: valor de sessionId presente en los mensajes. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | string SessionId: valor de sessionId presente en los mensajes.<br/>byte [] State []: estado de la sesión (carga del evento "Stop"). |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | string SessionId: valor de sessionId presente en los mensajes.<br/>byte [] State []: estado de la sesión. |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | string SessionId: valor de sessionId presente en los mensajes. |
| Microsoft.Azure.ServiceBus.Exception | cualquier API instrumentada| Exception Exception: instancia de excepción. |

En todos los eventos puede obtener acceso a `Activity.Current`, que contiene el contexto de la operación actual.

#### <a name="logging-additional-properties"></a>Registrar propiedades adicionales

`Activty.Current` proporciona un contexto detallado de la operación actual y sus elementos primarios. Para obtener más información, consulte la [documentación de las actividades](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
La instrumentación de Service Bus proporciona información adicional en el elemento `Activity.Current.Tags`, ya que tiene las opciones `MessageId` y `SessionId` siempre que estén disponibles.

Las actividades que realizan el seguimiento de los eventos "Receive", "Peek" y "ReceiveDeferred" también pueden tener la etiqueta `RelatedTo`. Esta contiene una lista distintiva de los elementos `Diagnostic-Id` de los mensajes que se han recibido como resultado.
Es posible que se reciban varios mensajes no relacionados debido a esta operación. Asimismo, el elemento `Diagnostic-Id` no se conoce cuando se inicia la operación, por lo que las operaciones de tipo "Receive" podrían estar correlacionadas con las operaciones de tipo "Process" si se usa solo esta etiqueta. No obstante, resulta útil para analizar los problemas de rendimiento y así poder comprobar cuánto tiempo se tardó en recibir el mensaje.

Una forma eficaz de registrar las etiquetas, es realizar una iteración con ellas para que, al agregarlas al ejemplo anterior, tengan el siguiente aspecto: 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrado y muestreo

En algunos casos, es recomendable registrar parte de los eventos para reducir el consumo del almacenamiento o la sobrecarga del rendimiento. Para ello, puede registrar solo los eventos "Stop" (tal como se muestra en el ejemplo anterior) o un porcentaje del muestreo de los eventos. 
`DiagnosticSource` proporciona la forma de lograrlo mediante el predicado `IsEnabled`. Para obtener más información, consulte [Context-Based Filtering in DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) (Filtrado basado en contexto en DiagnosticSource).

`IsEnabled` puede recibir varias llamadas de una sola operación para así minimizar el impacto en el rendimiento.

`IsEnabled` se llama en la siguiente secuencia:

1. `IsEnabled(<OperationName>, string entity, null)` por ejemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Tenga en cuenta que no hay ningún evento "Start" o "Stop" al final. Use esta opción para filtrar operaciones o colas determinadas. Si la devolución de llamada devuelve `false`, no se envían los eventos de la operación.

  * También recibirá una devolución de llamada `IsEnabled(<OperationName>, string entity, Activity activity)` para las operaciones "Process" y "ProcessSession". Úsela para filtrar eventos basados en `activity.Id` o en propiedades de etiquetas.
  
2. `IsEnabled(<OperationName>.Start)` por ejemplo, `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Comprueba si se deben activar eventos "Start". El resultado solo afecta a los eventos "Start", pero la instrumentación adicional no depende de él.

No hay ningún elemento `IsEnabled` para el evento "Stop".

Si algún resultado de la operación es una excepción, se llama a `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`. Solo puede suscribirse a eventos "Exception" y evitar el resto de la instrumentación. En este caso, tendrá que administrar dichas excepciones. Como el resto de la instrumentación está deshabilitada, no espere que el contexto del seguimiento fluya con los mensajes de consumidor a productor.

Puede usar `IsEnabled` y también implementar estrategias de muestreo. Gracias al muestreo basado en `Activity.Id` o `Activity.RootId`, puede obtener resultados de muestreo coherentes en todos los elementos (siempre y cuando se propague mediante el seguimiento del sistema o nuestro propio código).

Cuando hay varios agentes de escucha `DiagnosticSource` en el mismo origen, un solo agente de escucha no es suficiente para aceptar el evento, por lo que no se puede garantizar que llame a `IsEnabled`.

## <a name="next-steps"></a>pasos siguientes

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Correlación de Application Insights](../application-insights/application-insights-correlation.md)
* [Dependencias de supervisión de Application Insights](../application-insights/app-insights-asp-net-dependencies.md) para ver si REST, SQL u otros recursos externos le están ralentizando.
* [Realizar el seguimiento de operaciones personalizadas con el SDK de .NET para Application Insights](../application-insights/application-insights-custom-operations-tracking.md)
