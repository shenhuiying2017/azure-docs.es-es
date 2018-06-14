---
title: Diagnóstico de Reliable Services con estado en Azure Service Fabric | Microsoft Docs
description: Funcionalidad de diagnóstico para Reliable Services con estado en Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 268ec61515f438fb7f98b6cef7a8ec60ba22e23f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212643"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funcionalidad de diagnóstico para Reliable Services con estado
La clase StatefulServiceBase de Reliable Services con estado de Azure Service Fabric emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que pueden usarse para depurar el servicio, ofrecer información acerca de cómo funciona el tiempo de ejecución y ayudar a solucionar problemas.

## <a name="eventsource-events"></a>Eventos EventSource
El nombre EventSource de la clase StatefulServiceBase de Reliable Services con estado es "Microsoft-ServiceFabric-Services". Los eventos de este origen de eventos aparecen en la ventana [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando se está [depurando el servicio en Visual Studio](service-fabric-debugging-your-application.md).

Otros ejemplos de herramientas y tecnologías que ayudan a recopilar o ver eventos EventSource son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) y [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Eventos
| Nombre del evento | Id. de evento | Nivel | Descripción del evento |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informativo |Emitido cuando se inicia la tarea RunAsync del servicio |
| StatefulRunAsyncCancellation |2 |Informativo |Emitido cuando se cancela la tarea RunAsync del servicio |
| StatefulRunAsyncCompletion |3 |Informativo |Emitido cuando finaliza la tarea RunAsync del servicio |
| StatefulRunAsyncSlowCancellation |4 |Warning (Advertencia) |Emitido cuando la tarea RunAsync del servicio tarda demasiado tiempo en completar la cancelación |
| StatefulRunAsyncFailure |5 |Error |Emitido cuando la tarea RunAsync del servicio genera una excepción |

## <a name="interpret-events"></a>Interpretar eventos
Los eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion y StatefulRunAsyncCancellation son útiles para que el escritor del servicio comprenda el ciclo de vida de un servicio, así como el momento en que se inicia, se cancela o finaliza un servicio. Esta información puede ser útil al depurar problemas de servicio o al comprender el ciclo de vida de servicio.

Los escritores del servicio deben prestar especial atención a los eventos StatefulRunAsyncSlowCancellation y StatefulRunAsyncFailure, ya que indican problemas con el servicio.

StatefulRunAsyncFailure se genera cada vez que la tarea RunAsync() del servicio produce una excepción. Normalmente, una excepción generada indica un error en el servicio. Además, la excepción hace que se produzca un error en el servicio, por lo que se mueve a otro nodo. Esta operación puede ser cara y puede retrasar las solicitudes entrantes mientras se mueve el servicio. Los escritores del servicio deben determinar la causa de la excepción y si es posible mitigarla.

StatefulRunAsyncSlowCancellation se genera cada vez que una solicitud de cancelación de la tarea RunAsync tarda más de cuatro segundos. Cuando un servicio tarda demasiado en completar la cancelación, afecta a la capacidad del servicio de reiniciarse rápidamente en otro nodo. Este escenario puede afectar a la disponibilidad general del servicio.

## <a name="performance-counters"></a>contadores de rendimiento
El tiempo de ejecución de Reliable Services define las siguientes categorías de contador de rendimiento:

| Categoría | DESCRIPCIÓN |
| --- | --- |
| Replicador transaccional de Service Fabric |Contadores específicos del replicador transaccional de Azure Service Fabric |

El replicador transaccional de Service Fabric se utiliza por [el administrador de estado confiable](service-fabric-reliable-services-reliable-collections-internals.md) para replicar las transacciones dentro de un conjunto determinado de [réplicas](service-fabric-concepts-replica-lifecycle.md). 

La aplicación del [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponible de forma predeterminada en el sistema operativo Windows puede usarse para recopilar y ver los datos del contador de rendimiento. [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) es otra opción para recopilar datos del contador de rendimiento y cargarlos en tablas de Azure.

### <a name="performance-counter-instance-names"></a>Nombres de instancias de contador de rendimiento
Un clúster que tenga un gran número de Reliable Services o particiones de Reliable Services tendrá una gran cantidad de instancias de contador de rendimiento del replicador de transacciones. Los nombres de las instancias del contador de rendimiento pueden ayudar a identificar la [partición](service-fabric-concepts-partitioning.md) y la réplica de servicio específicos con los que está asociada la instancia de contador de rendimiento.

#### <a name="service-fabric-transactional-replicator-category"></a>Categoría del replicador de transacciones de Service Fabric
En la categoría `Service Fabric Transactional Replicator`, los nombres de instancias de contadores tienen el formato siguiente:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* es la representación de cadena del identificador de partición de Service Fabric con el que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y su representación de cadena se genera mediante [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con el especificador de formato "D".

*ServiceFabricReplicaId* es el identificador asociado a una réplica específica de una instancia de Reliable Services. El identificador de la réplica se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otras instancias de contador de rendimiento generadas por la misma partición. Encontrará más detalles sobre las réplicas y su función en Reliable Services [aquí](service-fabric-concepts-replica-lifecycle.md).

El siguiente nombre de instancia de contador es típico de un contador de la categoría `Service Fabric Transactional Replicator`:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

En el ejemplo anterior, `00d0126d-3e36-4d68-98da-cc4f7195d85e` es la representación de cadena del identificador de partición Service Fabric, y `131652217797162571` es el identificador de réplica.

### <a name="transactional-replicator-performance-counters"></a>Contadores de rendimiento del replicador de transacciones

El tiempo de ejecución de Reliable Services emite los siguientes eventos en la categoría `Service Fabric Transactional Replicator`.

 Nombre del contador | DESCRIPCIÓN |
| --- | --- |
| Operaciones de inicio de transacción/s | El número de nuevas transacciones de escritura creadas por segundo.|
| Operaciones de transacción/s | El número de operaciones de adición/actualización/eliminación realizadas en colecciones confiables por segundo.|
| Prom. Latencia de vaciado (ms) | Número de bytes que el replicador de transacciones vacía en el disco por segundo |
| Operaciones limitadas/s | Número de operaciones que el replicador de transacciones rechaza cada segundo por causas de limitación. |
| Prom. ms de transacción/confirmación | Latencia de confirmación media por transacción en milisegundos |
| Prom. Latencia de vaciado (ms) | Duración media de las operaciones de vaciado de disco iniciadas por el replicador de transacciones en milisegundos |

## <a name="next-steps"></a>Pasos siguientes
[Proveedores de EventSource en PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
