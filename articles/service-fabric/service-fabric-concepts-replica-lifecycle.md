---
title: "Réplicas e instancias en Azure Service Fabric | Microsoft Docs"
description: "Descripción de réplicas, instancias, su función y ciclo de vida"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 794cbed0f5072bcc1c18343b9896aad464861c45
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="replicas-and-instances"></a>Réplicas e instancias 
En este artículo se ofrece información general del ciclo de vida de réplicas de servicios con estado e instancias de servicios sin estado.

## <a name="instances-of-stateless-services"></a>Instancias de servicios sin estado
Una instancia de un servicio sin estado es una copia de la lógica de servicio que se ejecuta en uno de los nodos del clúster. Una instancia dentro de una partición solo se identifica mediante su InstanceId. El ciclo de vida de una instancia se puede modelar mediante el siguiente diagrama:

![Ciclo de vida de las instancias](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Una vez que el Resource Manager de clúster determina una ubicación para la instancia, especifica este estado del ciclo de vida. En este momento, la instancia se iniciará en el nodo (el host de aplicación iniciado, la instancia creada y, después, abierta). Una vez completo el inicio, la instancia realiza la transición al estado listo. 

Si el host o nodo de aplicación para esta instancia se bloquea, realiza la transición al estado descartado.

### <a name="ready-rd"></a>Listo (Ready, RD)
En el estado listo, la instancia está en funcionamiento en el nodo. Si este es un servicio de confianza, RunAsync se ha invocado. 

Si el host o nodo de aplicación para esta instancia se bloquea, realiza la transición al estado descartado.

### <a name="closing-cl"></a>Cierre (Closing, CL)
En el estado de cierre, Service Fabric se encuentra en proceso de apagar la instancia de este nodo. Esto podría deberse a muchas razones: por ejemplo, la actualización de la aplicación, el equilibrio de carga o el servicio que se elimina. Una vez completado el apagado, realiza la transición al estado descartado.

### <a name="dropped-dd"></a>Descartado (Dropped, DD)
En el estado descartado, la instancia ya no se ejecuta en el nodo. En este momento, Service Fabric mantiene metadatos sobre esta instancia (que se eliminará de forma diferida).

> [!NOTE]
> Es posible realizar la transición de cualquier estado al estado descartado mediante la opción ForceRemove en `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de servicios con estado
Una réplica de un servicio con estado es una copia de la lógica de servicio que se ejecuta en uno de los nodos del clúster. Además, la réplica mantiene una copia del estado de ese servicio. Hay dos conceptos relacionados que describen el ciclo de vida y comportamiento de las réplicas con estado:
- Ciclo de vida de las réplicas
- Rol de réplica

En la siguiente explicación se describen servicios con estado persistentes. Para los servicios con estado volátiles (o en memoria), los estados fuera de servicio o descartado son equivalentes.

![Ciclo de vida de las réplicas](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Una réplica InBuild es una réplica que se crea o prepara para unirse al conjunto de réplicas. Dependiendo del rol de réplica, IB tiene una semántica diferente. 

Si el host de aplicación o el nodo para una réplica InBuild se bloquea, realiza la transición al estado fuera de servicio.

####<a name="primary-inbuild-replicas"></a>Réplicas InBuild principales 
InBuild principales son las primeras réplicas para una partición. Esto suele ocurrir al crearse la partición. Las réplicas InBuild principales también surgen al reiniciarse todas las réplicas de una partición o descartarse.

####<a name="idlesecondary-inbuild-replicas"></a>Réplicas InBuild secundarias inactivas
Estas son nuevas réplicas creadas por el Resource Manager de clúster. También pueden ser réplicas existentes que dejaron de funcionar y deben agregarse de nuevo en el conjunto. Estas réplicas se inicializan o crean mediante la principal antes de poder unirse al conjunto de réplicas como secundarias activas y participar en el reconocimiento cuórum de las operaciones.

####<a name="activesecondary-inbuild-replicas"></a>Réplicas InBuild secundarias activas
Este estado puede observarse en algunas consultas. Es una optimización donde el conjunto de réplicas no cambia, pero debe crearse una réplica. La propia réplica sigue las transacciones de máquina de estados normales (como se describe en la sección de roles de réplica).

### <a name="ready-rd"></a>Listo (Ready, RD)
Una réplica lista es una réplica que participa en la replicación y reconocimiento cuórum de las operaciones. El estado listo se puede aplicar a las réplicas principales y secundarias activas.

Si el host de aplicación o el nodo para una réplica lista se bloquea, realiza la transición al estado fuera de servicio.

### <a name="closing-cl"></a>Cierre (Closing, CL)
Una réplica especifica el estado de cierre en los siguientes escenarios:

- **Apagado del código para la réplica**: es posible que Service Fabric tenga que apagar el código en ejecución para una réplica. Esto podría deberse a muchas razones, por ejemplo, la aplicación, Fabric, la actualización de la infraestructura o un error notificado por la réplica, etc. Cuando se completa el cierre de la réplica, esta realiza la transición al estado fuera de servicio. El estado persistente asociado a esta réplica que se almacena en disco no se limpia.

- **Eliminación de la réplica del clúster**: es posible que Service Fabric tenga que quitar el estado persistente y apagar el código en ejecución para una réplica. Esto podría deberse a muchas razones, por ejemplo, el equilibrio de carga

### <a name="dropped-dd"></a>Descartado (Dropped, DD)
En el estado descartado, la instancia ya no se ejecuta en el nodo. Tampoco se ha dejado ningún estado en el nodo. En este momento, Service Fabric mantiene metadatos sobre esta instancia (que finalmente se eliminará también).

### <a name="down-d"></a>Fuera de servicio (Down, D)
En el estado fuera de servicio, el código de réplica no se ejecuta, pero el estado persistente para esa réplica existe en ese nodo. Una réplica puede estar fuera de servicio por muchas razones, por ejemplo, el nodo que está fuera de servicio, los bloqueos en el código de réplica, la actualización de la aplicación, errores en la réplica, etc.

Una réplica fuera de servicio se abre mediante Service Fabric según se necesite. Por ejemplo, cuando la actualización esté completa en el nodo, etc.

El rol de réplica no es pertinente en el estado fuera de servicio.

### <a name="opening-op"></a>Apertura (Opening, OP)
Una réplica fuera de servicio especifica el estado de apertura si es necesario que Service Fabric traiga de nuevo la copia de seguridad de la réplica. Por ejemplo, esto podría ocurrir tras completarse en un nodo la actualización de un código para la aplicación, etc. 

Si el host de aplicación o el nodo para una réplica de apertura se bloquea, realiza la transición al estado fuera de servicio.

El rol de réplica no es pertinente en el estado de apertura.

### <a name="standby-sb"></a>Espera (StandBy, SB)
Una réplica de espera es una réplica de un servicio persistente que dejó de funcionar y se abrió a continuación. Esta réplica podría usarse mediante Service Fabric si debe agregar otra réplica al conjunto de réplicas (puesto que ya tiene parte del estado y el proceso de creación es más rápido). Tras expirar StandByReplicaKeepDuration, se descarta la réplica de espera.

Si el host de aplicación o el nodo para una réplica de espera se bloquea, realiza la transición al estado fuera de servicio.

El rol de réplica no es pertinente en el estado de espera.

> [!NOTE]
> Se considera que aquellas réplicas que no están fuera de servicio ni se descartan se encuentran *operativas*.
>

> [!NOTE]
> Es posible realizar la transición de cualquier estado al estado descartado mediante la opción ForceRemove en `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Rol de réplica 
El rol de la réplica determina su función en el conjunto de réplicas.

- **Principales (Primary, P)**: hay una principal en el conjunto de réplicas responsable de realizar las operaciones de lectura y escritura. 
- **Secundarias activas (ActiveSecondary, S)**: son réplicas que reciben actualizaciones de estado de la principal, las aplican y devuelven confirmaciones. Hay varios secundarios activos en el conjunto de réplicas y el número de estos determina el número de errores que el servicio puede administrar.
- **Secundarias inactivas (IdleSecondary, I)**: estas réplicas se crean mediante la principal, es decir, reciben estado de la principal antes de poder promoverse a la secundaria activa. 
- **Ningunas (None, N)**: estas réplicas no tienen ninguna responsabilidad en el conjunto de réplicas.
- **Desconocidas (Unknown, U)**: este es el rol inicial de una réplica antes de recibir una llamada API ChangeRole de Service Fabric.

En la siguiente sección se describen las transiciones de rol de réplica y algunos escenarios de ejemplo en los que pueden producirse:

![Rol de réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: creación de una nueva réplica principal
- U -> I: creación de una nueva réplica inactiva
- U -> N: eliminación de una réplica de espera
- I -> S: promoción de secundaria inactiva a secundaria activa de forma que sus confirmaciones contribuyan hacia el cuórum.
- I -> P: promoción de secundaria inactiva a principal. Esto puede ocurrir bajo reconfiguraciones especiales cuando la secundaria inactiva es la candidata correcta para ser principal.
- I -> N: eliminación de réplica secundaria inactiva.
- S -> P: promoción de secundaria activa a principal. Esto puede deberse a la conmutación por error de la principal o un movimiento principal iniciado por el Resource Manager de clúster en respuesta a la actualización de la aplicación o el equilibrio de carga, etc.
- S -> N: eliminación de réplica secundaria activa.
- P -> S: degradación de réplica principal. Esto puede deberse a un movimiento principal iniciado por el Resource Manager de clúster en respuesta a la actualización de la aplicación o el equilibrio de carga, etc.
- P -> N: eliminación de réplica principal

> [!NOTE]
> Los modelos de programación de nivel superior, como [Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md) ocultan el concepto de rol de réplica del desarrollador. En Actors, la noción de rol es innecesaria, mientras que en Services se simplifica enormemente en la mayoría de los escenarios.
>

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

- [Ciclo de vida de Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)

