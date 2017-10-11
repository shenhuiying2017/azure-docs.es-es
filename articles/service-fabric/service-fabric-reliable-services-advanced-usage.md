---
title: Uso avanzado de Reliable Services | Microsoft Docs
description: "Obtenga información sobre el uso avanzado de Reliable Services de Service Fabric para obtener una mayor flexibilidad en los servicios."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: a87924faaf5c6c43716b06b6d70ab5100c61f097
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Uso avanzado del modelo de programación de servicios fiables
Azure Service Fabric simplifica la escritura y administración de los servicios con y sin estado confiables. En esta guía se explican los usos avanzados de Reliable Services para obtener más control y flexibilidad respecto a sus servicios. Antes de leer esta guía, familiarícese con [el modelo de programación de servicios fiables](service-fabric-reliable-services-introduction.md).

Los servicios con y sin estado tienen dos puntos de entrada principal para el código de usuario:

* `RunAsync(C#) / runAsync(Java)` es un punto de entrada de propósito general para el código del servicio.
* `CreateServiceReplicaListeners(C#)` y `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` se usan con el fin de abrir agentes de escucha de comunicación para las solicitudes de cliente.

En la mayoría de los servicios, estos puntos de dos entradas son suficientes. En raras ocasiones, cuando se precisa más control sobre el ciclo de vida de un servicio, se encuentran disponibles eventos de ciclo de vida adicionales.

## <a name="stateless-service-instance-lifecycle"></a>Ciclo de vida de instancias de servicios sin estado
El ciclo de vida de un servicio sin estado es muy sencillo. Los servicios sin estado solo se pueden abrir, cerrar o anular. El elemento `RunAsync` incluido en un servicio sin estado se ejecuta cuando se abre una instancia de este y se cancela cuando se cierra o anula una instancia de dicho servicio.

Aunque `RunAsync` debe ser suficiente en casi todos los casos, también se encuentran disponibles eventos de apertura, cierre o anulación en los servicios sin estado:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` Se llama a OnOpenAsync cuando está a punto de usarse la instancia de servicio sin estado. En este momento, se pueden iniciar las tareas de inicialización del servicio ampliado.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` Se llama a OnCloseAsync cuando la instancia de servicio sin estado se va a apagar correctamente. Esto puede ocurrir cuando se está actualizando el código de servicio, cuando se está moviendo la instancia de servicio debido al equilibrio de carga o se detecta un error transitorio. OnCloseAsync puede utilizarse para cerrar de manera segura todos los recursos, detener cualquier procesamiento en segundo plano, terminar de guardar el estado externo o cerrar conexiones existentes.
* `void OnAbort() - C# / void onAbort() - Java` Se llama a OnAbort cuando la instancia de servicio sin estado se apaga a la fuerza. Normalmente se llama cuando se detecta un error permanente en el nodo o cuando Service Fabric no puede administrar el ciclo de vida de la instancia de servicio debido a errores internos.

## <a name="stateful-service-replica-lifecycle"></a>Ciclo de vida de réplicas de servicio con estado

> [!NOTE]
> Las instancias con estado de Reliable Services aún no se admiten en Java.
>
>

El ciclo de vida de una réplica de servicio con estado es mucho más complejo que una instancia de servicio sin estado. Además de los eventos de apertura, cierre y anulación, las réplicas de servicio con estado sufren cambios de rol durante su ciclo de vida. Cuando una réplica de servicio con estado cambia de rol, se desencadena el evento `OnChangeRoleAsync` :

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` cuando la réplica del servicio con estado cambia de rol, por ejemplo, a principal o secundario. Las réplicas principales reciben el estado de escritura (se les permite crear y escribir en Reliable Collections). Las réplicas secundarias reciben el estado de lectura (solo pueden leer desde Reliable Collections existentes). La mayoría del trabajo en un servicio con estado se lleva a cabo en la réplica principal. Las réplicas secundarias pueden realizar validación de solo lectura, generación de informes, minería de datos u otros trabajos de solo lectura.

En un servicio con estado, solo la réplica principal tiene acceso de escritura al estado y, por tanto, normalmente cuando el servicio está realizando tareas reales. El método `RunAsync` de un servicio con estado se ejecuta solo cuando la réplica de servicio con estado es principal. El método `RunAsync` se cancela cuando el rol de una réplica principal cambia a otro distinto del principal, así como durante los eventos de cierre y anulación.

Mediante el evento `OnChangeRoleAsync` se pueden realizar las tareas según el rol de réplica, así como en respuesta al cambio de rol.

Un servicio con estado también proporciona los mismos cuatro eventos de ciclo de vida que uno sin estado, con la misma semántica y casos de uso:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>Pasos siguientes
Para consultar temas más avanzados relacionados con Service Fabric, consulte los siguientes artículos:

* [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md)
* [Introducción al estado de Service Fabric](service-fabric-health-introduction.md)
* [Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Configuring cluster resource manager settings for service fabric services (Configuración del Administrador de recursos de clúster para servicios de Service Fabric)](service-fabric-cluster-resource-manager-configure-services.md)
