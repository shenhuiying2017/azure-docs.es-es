---
title: Aspectos internos de Reliable State Manager y Reliable Collection de Azure Service Fabric | Microsoft Docs
description: "Profundización en los conceptos y el diseño de Reliable Collection en Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Aspectos internos de Reliable State Manager y Reliable Collection de Azure Service Fabric
Este documento analiza en profundidad Reliable State Manager y Reliable Collections para ver qué se esconde detrás del funcionamiento de los componentes principales.

> [!NOTE]
> Este documento está en proceso de elaboración. Agregue comentarios a este artículo para decirnos sobre qué tema le gustaría obtener más información.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Modelo de persistencia local: registro y punto de comprobación
Reliable State Manager y Reliable Collections siguen un modelo de persistencia que se denomina Registro y punto de control.
En este modelo, cada cambio de estado se registra primero en el disco y, a continuación, se aplica en memoria.
El mismo estado completo se guarda en ocasiones (también conocido como Punto de comprobación).
La ventaja que proporciona es: que los deltas se convierten en escrituras de solo anexación secuenciales en disco para mejorar el rendimiento.

Para comprender mejor el modelo de registro y de punto de control, primero analicemos el escenario de discos infinitos.
Reliable State Manager registra cada operación antes de replicarse.
El registro permite que Reliable Collections aplique la operación solo en memoria.
Dado que los registros se conservan, incluso cuando la réplica produce error y debe reiniciarse, Reliable State Manager tiene suficiente información en sus registros para reproducir todas las operaciones que ha perdido la réplica.
Como el disco es infinito, las entradas de registro nunca deberán quitarse y Reliable Collection solo tiene que administrar el estado en memoria.

Ahora veamos el escenario de disco finito.
En un determinado momento, Reliable State Manager se quedará sin espacio en disco.
Antes de que suceda, Reliable State Manager debe truncar su registro para liberar espacio para los registros más recientes.
Reliable State Manager solicita las colecciones confiables para aplicar un punto de comprobación a su estado en memoria en el disco.
En este momento, las colecciones confiables conservarían su estado en memoria.
Una vez que las colecciones fiables completen sus puntos de control, el administrador de estado fiable puede truncar el registro para liberar espacio en disco.
Cuando la réplica debe reiniciarse, Reliable Collections recuperará su estado de punto de comprobación y Reliable State Manager recupera y reproduce todos los cambios de estado que se han producido desde el último punto de control.

Otro valor de agregar puntos de comprobación es que mejora los tiempos de recuperación en escenarios comunes. El registro contiene todas las operaciones que se han producido desde el último punto de comprobación.
Así que puede incluir varias versiones de un elemento, como varios valores para una fila determinada en Reliable Dictionary.
En cambio, Reliable Collections solo aplica puntos de comprobación a la última versión de cada valor de una clave.

## <a name="next-steps"></a>Pasos siguientes
* [Transacciones y bloqueos](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

