---
title: Alta disponibilidad y confiabilidad de Programador
description: Alta disponibilidad y confiabilidad de Programador
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="scheduler-high-availability-and-reliability"></a>Alta disponibilidad y confiabilidad de Programador
## <a name="azure-scheduler-high-availability"></a>Alta disponibilidad de Programador de Azure
Como servicio principal de la plataforma Azure, Programador de Azure tiene una alta disponibilidad y presenta implementación de servicio con redundancia geográfica y replicación geográfica regional de trabajos.

### <a name="geo-redundant-service-deployment"></a>Implementación de servicio con redundancia geográfica
Programador de Azure está disponible a través de la interfaz de usuario en prácticamente todas las regiones geográficas actuales de Azure. La lista de regiones en las que Programador de Azure está disponible se [muestra aquí](https://azure.microsoft.com/regions/#services). Si un centro de datos en una región hospedada no está disponible, las capacidades de conmutación por error de Programador de Azure son tales que el servicio está disponible en otro centro de datos.

### <a name="geo-regional-job-replication"></a>Replicación geográfica regional de trabajos
No solo el front-end de Programador de Azure está disponible para solicitudes de administración, sino que su propio trabajo también se replica geográficamente. Cuando hay una interrupción en una región, Programador de Azure conmuta por error y garantiza que el trabajo se ejecuta en otro centro de datos en la región geográfica emparejada.

Por ejemplo, si creó un trabajo en la zona Centro-Sur de EE. UU., Programador de Azure replica automáticamente ese trabajo en la zona Centro-Norte de EE. UU. Cuando hay un error en la zona Centro-Sur de EE. UU., Programador de Azure garantiza que el trabajo se ejecuta en la zona Centro-Norte de EE. UU. 

![][1]

Como resultado, Programador de Azure garantiza que, en caso de error de Azure, los datos se mantienen en la misma región geográfica más amplia. En consecuencia, no tiene duplicar el trabajo para agregar alta disponibilidad: Programador de Azure proporciona automáticamente capacidades de alta disponibilidad para sus trabajos.

## <a name="azure-scheduler-reliability"></a>Confiabilidad de Programador de Azure
Programador de Azure garantiza su propia alta disponibilidad y adopta un enfoque diferente para los trabajos creados por el usuario. Por ejemplo, puede que su trabajo invoque un extremo HTTP que no está disponible. No obstante, Programador de Azure intenta ejecutar el trabajo correctamente, ofreciéndole opciones alternativas para resolver el error. Programador de Azure hace esto de dos maneras:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Directiva de reintentos configurable a través de "retryPolicy"
Programador de Azure le permite configurar una directiva de reintentos. De manera predeterminada, si se produce un error en un trabajo, Programador intenta ejecutar el trabajo otras cuatro veces, a intervalos de 30 segundos. Puede volver a configurar esta directiva de reintentos para que sea más agresiva (por ejemplo, diez veces, a intervalos de 30 segundos) o más flexible (por ejemplo, dos veces, a intervalos diarios).

Como ejemplo de cuándo esto puede resultar de ayuda, puede crear un trabajo que se ejecute una vez por semana e invoca un extremo HTTP. Si el extremo HTTP está inactivo durante algunas horas cuando se ejecute el trabajo, es posible que no quiera esperar una semana más para que el trabajo se vuelva a ejecutar, puesto que incluso la directiva predeterminada de reintentos producirá un error. En estos casos, puede volver a configurar la directiva de reintentos estándar para que vuelva a intentar ejecutarlo cada tres horas (por ejemplo), en lugar de cada 30 segundos.

Para obtener información sobre cómo configurar una directiva de reintentos, vea [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Capacidad de configuración alternativa de extremos a través de "errorAction"
Si el extremo de destino para el trabajo de Programador de Azure permanece inaccesible, Programador de Azure recurre al extremo de control de errores alternativo tras aplicar la directiva de reintentos. Si se configura un extremo de control de errores alternativo, Programador de Azure lo invoca. Con un extremo alternativo, sus propios trabajos tendrán alta disponibilidad en caso de error.

Por ejemplo, en el diagrama siguiente, Programador de Azure sigue su directiva de reintentos para visitar un servicio web de Nueva York. Si los reintentos fallan, comprueba si hay una alternativa. Sigue adelante y comienza a realizar solicitudes a la alternativa con la misma directiva de reintentos.

![][2]

Tenga en cuenta que se aplica la misma directiva de reintentos a la acción original y la acción de error alternativa. También es posible que el tipo de acción de la acción de error alternativa sea distinto del tipo de acción de la acción principal. Por ejemplo, mientras que la acción principal puede ser invocar un punto de conexión HTTP, es posible que la acción de error sea una acción de la cola de almacenamiento, la cola del bus de servicio o el tema de bus de servicio que sí realiza el registro de errores.

Para obtener información sobre cómo configurar un extremo alternativo, consulte [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Otras referencias
 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Creación de programaciones complejas y periodicidad avanzada con Programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
