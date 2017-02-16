---
title: "Información general del ciclo de vida de Reliable Services de Azure Service Fabric | Microsoft Docs"
description: "Obtenga más información sobre los distintos eventos de ciclo de vida en los servicios de Reliable Services de Service Fabric"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: 1472bd1647b620d1f0489cdabbed58e72585aef7
ms.openlocfilehash: ad1f713f2b12af4f778b9b14edf33a568d2aa8b1

---

# <a name="reliable-services-lifecycle-overview"></a>Información general del ciclo de vida de Reliable Services
Al analizar los ciclos de vida de Reliable Services, los conceptos básicos del ciclo de vida son lo más importante. En general:

* Durante el inicio:
  * se construyen los servicios,
  * tienen la oportunidad de construir y devolver cero o más agentes de escucha,
  * se abre cualquier agente de escucha devuelto, lo cual permite la comunicación con el servicio y
  * se llama al método RunAsync del servicio, lo cual permite al servicio realizar trabajos de larga ejecución o en segundo plano.
* Durante el cierre:
  * se cancela el token de cancelación pasado a RunAsync y se cierran los agentes de escucha y
  * una vez completado, se destruye el propio objeto de servicio.

Hay información sobre la ordenación exacta de estos eventos. En concreto, el orden de eventos puede cambiar ligeramente dependiendo de si Reliable Service es sin estado o con estado. Además, para los servicios con estado, tenemos que tratar con el escenario de intercambio principal. Durante esta secuencia, el rol Principal se transfiere a otra réplica (o se devuelve) sin que el servicio tenga que cerrarse. Por último, hay que pensar en condiciones de error.

## <a name="stateless-service-startup"></a>Inicio de un servicio sin estado
El ciclo de vida de un servicio sin estado es bastante sencillo. Este es el orden de los eventos:

1. Se construye el servicio.
2. A continuación, suceden dos cosas simultáneamente:
    - Se invoca `StatelessService.CreateServiceInstanceListeners()` y se abren los agentes de escucha devueltos (se llama a `ICommunicationListener.OpenAsync()` en cada agente de escucha).
    - Se llama al método RunAsync (`StatelessService.RunAsync()`) del servicio.
3. Si está presente, se llama al método OnOpenAsync del propio servicio (en concreto, se llama a `StatelessService.OnOpenAsync()`. Se trata de una invalidación poco habitual, pero está disponible).

Es importante tener en cuenta que no hay ninguna ordenación entre las llamadas para crear y abrir los agentes de escucha y RunAsync. Los agentes de escucha se pueden abrir antes de iniciar RunAsync. De forma similar, RunAsync puede invocarse finalmente antes de que se abran o incluso de que se hayan construido los agentes de escucha de comunicación. Si se necesita cualquier sincronización, se deja como un ejercicio para el implementador. Soluciones comunes:

* En ocasiones, los agentes de escucha no pueden funcionar hasta que se crea otra información o se realiza un trabajo. Para los servicios sin estado, ese trabajo normalmente se puede realizar en el constructor del servicio, durante la llamada a `CreateServiceInstanceListeners()`, o como parte de la construcción del propio agente de escucha.
* En ocasiones, el código de RunAsync no quiere iniciarse hasta que estén abiertos los agentes de escucha. En este caso, hace falta coordinación adicional. Una solución habitual es alguna marca dentro de los agentes de escucha que indique cuándo se ha completado, lo cual se comprueba en RunAsync antes de continuar con el trabajo real.

## <a name="stateless-service-shutdown"></a>Cierre de un servicio sin estado
Al cerrar un servicio sin estado, se sigue el mismo patrón, solo que en orden inverso:

1. En paralelo
    - Se cierran todos los agentes de escucha abiertos (se llama a `ICommunicationListener.CloseAsync()` en cada agente de escucha)
    - Se cancela el token de cancelación pasado a `RunAsync()` (la comprobación de la propiedad `IsCancellationRequested` del token de cancelación devuelve el valor true, y si se llama al elemento `ThrowIfCancellationRequested` del token, el método devuelve una instancia de `OperationCanceledException`).
2. Cuando `CloseAsync()` se ha completado en cada agente de escucha y también finaliza `RunAsync()`, se llama al método `StatelessService.OnCloseAsync()` del servicio, si está presente (de nuevo, se trata de una invalidación poco habitual).
3. Una vez completado `StatelessService.OnCloseAsync()`, se destruye el objeto de servicio.

## <a name="stateful-service-startup"></a>Inicio de un servicio con estado
Los servicios con estado tienen un patrón similar al de los servicios sin estado, con unos pocos cambios. Al iniciarse un servicio con estado, el orden de los eventos es el siguiente:

1. Se construye el servicio.
2. Se llama a `StatefulServiceBase.OnOpenAsync()`. (Esto normalmente se invalida en el servicio).
3. Si la réplica del servicio en cuestión es la principal, los siguientes pasos se producen en paralelo; en caso contrario, el servicio avanza al paso 4
    - Se invoca `StatefulServiceBase.CreateServiceReplicaListeners()` y se abren los agentes de escucha devueltos (se llama a `ICommunicationListener.OpenAsync()` en cada agente de escucha).
    - Se llama al método RunAsync (`StatefulServiceBase.RunAsync()`) del servicio.
4. Cuando se han completado todas las llamadas `OpenAsync()` del agente de escucha de la réplica y se ha iniciado `RunAsync()` (o se omiten estos pasos porque esta réplica es secundaria actualmente), se llama a `StatefulServiceBase.OnChangeRoleAsync()`. (Esto se invalida con escasa frecuencia en el servicio).

De modo similar a los servicios sin estado, no hay coordinación entre el orden en el que se crean y abren los agentes de escucha y en el que se llama a RunAsync. Las soluciones son muy similares, con un caso adicional: imaginemos que las llamadas que llegan a los agentes de escucha de comunicación requieren que la información se mantenga dentro de alguna instancia de [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) para funcionar. Dado que es posible que los agentes de escucha de comunicación se abrieran antes de que se pudiera leer o escribir en las colecciones confiables, y antes de que se pudiera iniciar RunAsync, se necesita coordinación adicional. La solución más sencilla y habitual consiste en que los agentes de escucha de comunicación devuelvan algún código de error que permita informar al cliente para que intente la solicitud de nuevo.

## <a name="stateful-service-shutdown"></a>Cierre de un servicio con estado
De forma similar a los servicios sin estado, los eventos de ciclo de vida durante el cierre son los mismos que durante el inicio, pero a la inversa. Cuando un servicio con estado se está cerrando, se producen los siguientes eventos:

1. En paralelo
    - Se cierran todos los agentes de escucha abiertos (se llama a `ICommunicationListener.CloseAsync()` en cada agente de escucha)
    - Se cancela el token de cancelación pasado a `RunAsync()` (la comprobación de la propiedad `IsCancellationRequested` del token de cancelación devuelve el valor true, y si se llama al elemento `ThrowIfCancellationRequested` del token, el método devuelve una instancia de `OperationCanceledException`).
2. Cuando se han completado `CloseAsync()` y `RunAsync()` en cada agente de escucha (lo cual solo sería necesario si la réplica del servicio era principal), se llama al `StatefulServiceBase.OnChangeRoleAsync()` del servicio. (Esto se invalida con escasa frecuencia en el servicio).
3. Una vez finalizado el método `StatefulServiceBase.OnChangeRoleAsync()`, se llama al método `StatefulServiceBase.OnCloseAsync()` (de nuevo, se trata de una invalidación poco habitual, pero está disponible).
3. Una vez completado `StatefulServiceBase.OnCloseAsync()`, se destruye el objeto de servicio.

## <a name="stateful-service-primary-swaps"></a>Intercambios de servicios con estado principales
Durante la ejecución de un servicio con estado, solo se abren los agentes de escucha de comunicación y se llama al método RunAsync de los servicios con estado principales. Los secundarios se construyen, pero no reciben más llamadas. Pero durante la ejecución de un servicio con estado puede cambiar la réplica que es principal actualmente. ¿Cómo afecta esto a los eventos de ciclo de vida que puede ver una réplica? El comportamiento de la réplica con estado depende de si es la réplica que está subiendo o bajando de categoría.

### <a name="for-the-primary-being-demoted"></a>En el caso de la principal que pasa a secundaria
Service Fabric necesita que esta réplica deje de procesar mensajes y que cierre cualquier trabajo que esté realizando en segundo plano. Como resultado, este paso es similar al de un servicio que se está cerrando. Una diferencia es que el servicio no se destruye o se cierra, puesto que permanece como un elemento secundario. Se llama a las API siguientes:

1. En paralelo
    - Se cierran todos los agentes de escucha abiertos (se llama a `ICommunicationListener.CloseAsync()` en cada agente de escucha)
    - Se cancela el token de cancelación pasado a `RunAsync()` (la comprobación de la propiedad `IsCancellationRequested` del token de cancelación devuelve el valor true, y si se llama al elemento `ThrowIfCancellationRequested` del token, el método devuelve una instancia de `OperationCanceledException`).
2. Una vez completados `CloseAsync()` y `RunAsync()` en cada agente de escucha, se llama a `StatefulServiceBase.OnChangeRoleAsync()` del servicio. (Esto se invalida con escasa frecuencia en el servicio).

### <a name="for-the-secondary-being-promoted"></a>En el caso de la secundaria que pasa a principal
De forma similar, Service Fabric necesita que esta réplica empiece a escuchar mensajes en la conexión (si es su función) y a iniciar las tareas en segundo plano de las que se ocupa. Como resultado, este proceso es similar a cuando se crea el servicio, salvo que la propia réplica ya existe. Se llama a las API siguientes:

1. En paralelo
    - Se invoca `StatefulServiceBase.CreateServiceReplicaListeners()` y se abren los agentes de escucha devueltos (se llama a `ICommunicationListener.OpenAsync()` en cada agente de escucha).
    - Se llama al método RunAsync (`StatefulServiceBase.RunAsync()`) del servicio.
4. Cuando se han completado todas las llamadas a `OpenAsync()` del agente de escucha de la réplica y se ha iniciado `RunAsync()` (o se omitieron estos pasos porque esta réplica es secundaria), se llama a `StatefulServiceBase.OnChangeRoleAsync()`. (Esto se invalida con escasa frecuencia en el servicio).

## <a name="notes-on-service-lifecycle"></a>Notas sobre el ciclo de vida del servicio
* Tanto el método `RunAsync()` como las llamadas a `CreateServiceReplicaListeners/CreateServiceInstanceListeners` son opcionales. Un servicio puede tener uno, ambos o ninguno. Por ejemplo, si el servicio realiza todo su trabajo en respuesta a las llamadas del usuario, no es necesario que implemente `RunAsync()`. Solo son necesarios los agentes de escucha de comunicación y el código asociado. Del mismo modo, la creación y devolución de los agentes de escucha de comunicación es opcional, ya que el servicio tal vez solo tiene que realizar trabajo en segundo plano, por lo que solo necesita implementar `RunAsync()`.
* Un servicio puede completar `RunAsync()` correctamente y volver desde este. Esto no se considera una condición de error y representaría el trabajo en segundo plano de la finalización del servicio. Para los servicios de Reliable Services con estado, se llamaría a `RunAsync()` de nuevo si el servicio pasara de principal a secundario y de nuevo a principal.
* Si un servicio sale de `RunAsync()` iniciando una excepción inesperada, se trata de un error, de modo que se cierra el objeto de servicio y se notifica un error de mantenimiento.
* Aunque no hay un límite de tiempo para que se devuelvan resultados de estos métodos, se pierde inmediatamente la posibilidad de escribir en Reliable Collections y, por lo tanto, no se puede realizar ningún trabajo real. Se recomienda que devuelva resultados lo antes posible tras recibir la solicitud de cancelación. Si su servicio no responde a estas llamadas a la API en un intervalo de tiempo razonable, Service Fabric puede forzar la finalización del servicio. Esto solo suele pasar durante las actualizaciones de aplicación o cuando se elimina un servicio. Este tiempo de espera es de 15 minutos de manera predeterminada.
* Para los servicios con estado, hay una opción adicional en ServiceReplicaListeners que les permite iniciarse en las réplicas secundarias. Esto es poco habitual, pero el único cambio en los ciclos de vida es que se llama a `CreateServiceReplicaListeners()` (y los agentes de escucha resultantes abiertos) incluso si la réplica es un elemento secundario. De forma similar, si la réplica se convierte en principal más adelante, se cierran los agentes de escucha, se destruyen y se crean y abren unos nuevos y como parte del cambio a Principal.
* Los errores en la ruta `OnCloseAsync()` dan como resultado una llamada a `OnAbort()`, que es una oportunidad como último recurso para hacer todo lo posible para que el servicio limpie y libere cualquier recurso que haya reclamado.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Reliable Services](service-fabric-reliable-services-introduction.md)
* [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
* [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)



<!--HONumber=Jan17_HO1-->


