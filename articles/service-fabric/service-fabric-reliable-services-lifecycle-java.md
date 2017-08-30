---
title: "Información general del ciclo de vida de Reliable Services de Azure Service Fabric | Microsoft Docs"
description: "Obtenga más información sobre los distintos eventos de ciclo de vida en los servicios de Reliable Services de Service Fabric"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---

# <a name="reliable-services-lifecycle-overview"></a>Información general del ciclo de vida de Reliable Services
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java en Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Al analizar los ciclos de vida de Reliable Services, los conceptos básicos del ciclo de vida son lo más importante. En general:

* Durante el inicio:
  * se construyen los servicios,
  * tienen la oportunidad de construir y devolver cero o más agentes de escucha,
  * se abre cualquier agente de escucha devuelto, lo cual permite la comunicación con el servicio y
  * se llama al método runAsync del servicio, lo cual permite a este último realizar trabajos de ejecución prolongada o en segundo plano.
* Durante el cierre:
  * se cancela el token de cancelación pasado a runAsync y se cierran los agentes de escucha y
  * una vez completado, se destruye el propio objeto de servicio.

Hay información sobre la ordenación exacta de estos eventos. En concreto, el orden de eventos puede cambiar ligeramente dependiendo de si Reliable Service es sin estado o con estado. Además, para los servicios con estado, tenemos que tratar con el escenario de intercambio principal. Durante esta secuencia, el rol Principal se transfiere a otra réplica (o se devuelve) sin que el servicio tenga que cerrarse. Por último, hay que pensar en condiciones de error.

## <a name="stateless-service-startup"></a>Inicio de un servicio sin estado
El ciclo de vida de un servicio sin estado es bastante sencillo. Este es el orden de los eventos:

1. Se construye el servicio.
2. A continuación, suceden dos cosas simultáneamente:
    - Se invoca `StatelessService.createServiceInstanceListeners()` y se abren los agentes de escucha devueltos (se llama a `CommunicationListener.openAsync()` en cada agente de escucha).
    - Se llama al método runAsync (`StatelessService.runAsync()`) del servicio.
3. Si está presente, se llama al propio método onOpenAsync del servicio (en concreto, se llama a `StatelessService.onOpenAsync()`. Se trata de una invalidación poco habitual, pero está disponible).

Es importante tener en cuenta que no hay ninguna ordenación entre las llamadas para crear y abrir los agentes de escucha y runAsync. Los agentes de escucha se pueden abrir antes de iniciar runAsync. De forma similar, runAsync puede terminar invocándose antes de que se abran o incluso de que se hayan construido los agentes de escucha de comunicación. Si se necesita cualquier sincronización, se deja como un ejercicio para el implementador. Soluciones comunes:

* En ocasiones, los agentes de escucha no pueden funcionar hasta que se crea otra información o se realiza un trabajo. Para los servicios sin estado, ese trabajo normalmente se puede realizar en el constructor del servicio, durante la llamada a `createServiceInstanceListeners()`, o como parte de la construcción del propio agente de escucha.
* En ocasiones, el código de runAsync no se inicia hasta que estén abiertos los agentes de escucha. En este caso, hace falta coordinación adicional. Una solución habitual es incluir alguna marca dentro de los agentes de escucha que indique cuándo se han completado, lo cual se comprueba en runAsync antes de continuar con el trabajo real.

## <a name="stateless-service-shutdown"></a>Cierre de un servicio sin estado
Al cerrar un servicio sin estado, se sigue el mismo patrón, solo que en orden inverso:

1. En paralelo
    - Se cierran todos los agentes de escucha abiertos (se llama a `CommunicationListener.closeAsync()` en cada agente de escucha)
    - Se cancela el token de cancelación que se pasó a `runAsync()` (la comprobación de la propiedad `isCancelled` del token de cancelación devuelve el valor True y, si se llama al método `throwIfCancellationRequested` del token, se inicia una excepción `CancellationException`).
2. Cuando `closeAsync()` se ha completado en cada agente de escucha y también finaliza `runAsync()`, se llama al método `StatelessService.onCloseAsync()` del servicio, si está presente (de nuevo, se trata de una invalidación poco habitual).
3. Una vez completado `StatelessService.onCloseAsync()`, se destruye el objeto de servicio.

## <a name="notes-on-service-lifecycle"></a>Notas sobre el ciclo de vida del servicio
* Tanto el método `runAsync()` como las llamadas a `createServiceInstanceListeners` son opcionales. Un servicio puede tener uno, ambos o ninguno. Por ejemplo, si el servicio realiza todo su trabajo en respuesta a las llamadas del usuario, no es necesario que implemente `runAsync()`. Solo son necesarios los agentes de escucha de comunicación y el código asociado. Del mismo modo, la creación y devolución de los agentes de escucha de comunicación es opcional, ya que el servicio tal vez solo tiene que realizar trabajo en segundo plano, por lo que solo necesita implementar `runAsync()`.
* Un servicio puede completar `runAsync()` correctamente y volver desde este. Esto no se considera una condición de error y representaría el trabajo en segundo plano de la finalización del servicio. Para los servicios de Reliable Services con estado, se llamaría a `runAsync()` de nuevo si el servicio pasara de principal a secundario y de nuevo a principal.
* Si un servicio sale de `runAsync()` iniciando una excepción inesperada, se trata de un error, de modo que se cierra el objeto de servicio y se notifica un error de mantenimiento.
* Aunque no hay un límite de tiempo para que se devuelvan resultados de estos métodos, se pierde inmediatamente la posibilidad de escribir y, por lo tanto, no se puede realizar ningún trabajo real. Se recomienda que devuelva resultados lo antes posible tras recibir la solicitud de cancelación. Si su servicio no responde a estas llamadas a la API en un intervalo de tiempo razonable, Service Fabric puede forzar la finalización del servicio. Esto solo suele pasar durante las actualizaciones de aplicación o cuando se elimina un servicio. Este tiempo de espera es de 15 minutos de manera predeterminada.
* Los errores en la ruta `onCloseAsync()` dan como resultado una llamada a `onAbort()`, que es una oportunidad como último recurso para hacer todo lo posible para que el servicio limpie y libere cualquier recurso que haya reclamado.

> [!NOTE]
> Las instancias con estado de Reliable Services aún no se admiten en Java.
>
>

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
* [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)

