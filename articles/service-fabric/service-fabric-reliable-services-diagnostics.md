<properties
   pageTitle="Diagnóstico de servicios fiables con estado"
   description="Funcionalidad de diagnóstico para servicios fiables con estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="alanwar"/>

# Funcionalidad de diagnóstico para servicios fiables con estado
La clase StatefulServiceBase de servicios fiables con estado emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que pueden utilizarse para depurar el servicio, proporcionar información acerca de cómo funciona el tiempo de ejecución y ayudar a solucionar problemas.

## Eventos EventSource
El nombre EventSource de la clase StatefulServiceBase de servicio fiable con estado es "Microsoft-ServiceFabric-Services". Los eventos de este origen de eventos aparecen en la ventana [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando se está [depurando el servicio en Visual Studio](service-fabric-debugging-your-application.md).

Service Fabric también ofrece la opción de dirigir estos eventos a [Application Insights](http://azure.microsoft.com/services/application-insights/). Para obtener más información, consulte el artículo de [Configuración de Application Insights para Service Fabric](service-fabric-diagnostics-application-insights-setup.md).

Otros ejemplos de herramientas y tecnologías que ayudan a recopilar o ver eventos EventSource son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos de Microsoft Azure](../cloud-services-dotnet-diagnostics.md) y [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## Eventos

|Nombre del evento|Id de evento|Level|Descripción del evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativo|Emitido cuando se inicia la tarea RunAsync del servicio|
|StatefulRunAsyncCancellation|2|Informativo|Emitido cuando se cancela la tarea RunAsync del servicio|
|StatefulRunAsyncCompletion|3|Informativo|Emitido cuando se completa la tarea RunAsync del servicio|
|StatefulRunAsyncSlowCancellation|4|Warning (Advertencia)|Emitido cuando la tarea RunAsync del servicio tarda demasiado tiempo en completar la cancelación|
|StatefulRunAsyncFailure|5|Error|Emitido cuando la tarea RunAsync del servicio genera una excepción|

## Interpretación de eventos

Los eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion y StatefulRunAsyncCancellation son útiles para que el escritor del servicio comprenda el ciclo de vida de un servicio, así como el momento en que se inicia, se cancela y completa un servicio. Esto puede ser útil al depurar problemas de servicio o al comprender el ciclo de vida de servicio.

Los escritores del servicio deben prestar especial atención a los eventos StatefulRunAsyncSlowCancellation y StatefulRunAsyncFailure, ya que indican problemas con el servicio.

StatefulRunAsyncFailure se genera cada vez que la tarea RunAsync() del servicio produce una excepción. Normalmente, una excepción generada indica normalmente un error en el servicio. Además, la excepción hace que el servicio falle y se mueva a otro nodo, lo cual puede ser una operación costosa y retrasar las solicitudes entrantes mientras se mueve el servicio. Los escritores del servicio deben determinar la causa de la excepción y si es posible mitigarla.

StatefulRunAsyncSlowCancellation se genera cada vez que una solicitud de cancelación de la tarea RunAsync tarda más de 4 segundos. Cuando un servicio tarda demasiado en completar la cancelación, afecta a la capacidad del servicio de reiniciarse rápidamente en otro nodo y puede afectar a la disponibilidad general del servicio.

<!---HONumber=July15_HO2-->