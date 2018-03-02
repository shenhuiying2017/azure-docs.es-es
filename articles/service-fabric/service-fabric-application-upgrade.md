---
title: "Tutorial de actualización de aplicaciones de Service Fabric | Microsoft Docs"
description: "Este artículo proporciona una introducción a la actualización de una aplicación de Service Fabric, incluida la elección de los modos de actualización y las comprobaciones de estado."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
<<<<<<< HEAD
ms.date: 2/23/2018
=======
<<<<<<< HEAD
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 765931d8a888432e0cc77ff86d597b6e2a029a2a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
=======
ms.date: 2/13/2018
>>>>>>> eefc9d4b1876cab186dde3b22a97f8b1abe07f8b
ms.author: subramar
ms.openlocfilehash: 765931d8a888432e0cc77ff86d597b6e2a029a2a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
<<<<<<< HEAD
ms.lasthandoff: 02/24/2018
=======
ms.lasthandoff: 02/21/2018
>>>>>>> 2b7eaf6c4cfe5413d9b581a669c96a2527a1b2ba
>>>>>>> eefc9d4b1876cab186dde3b22a97f8b1abe07f8b
---
# <a name="service-fabric-application-upgrade"></a>Actualización de la aplicación de Service Fabric
Una aplicación de Azure Service Fabric es una colección de servicios. Durante una actualización, Service Fabric compara el nuevo [manifiesto de aplicación](service-fabric-application-and-service-manifests.md) con la versión anterior y determina qué servicios de la aplicación requieren actualizaciones. Service Fabric compara los números de versión en los manifiestos de servicio con los números de versión en la versión anterior. Si un servicio no ha cambiado, no se actualiza.

## <a name="rolling-upgrades-overview"></a>Información general de las actualizaciones graduales
En una actualización de la aplicación gradual, la actualización se realiza por etapas. En cada etapa, la actualización se aplica a un subconjunto de nodos del clúster, llamado dominio de actualización. Como resultado, la aplicación permanece disponible durante la actualización. Durante la actualización, el clúster puede contener una mezcla de las versiones anteriores y nuevas.

Por esa razón, las dos versiones deben ser compatibles con las versiones anteriores y nuevas. Si no son compatibles, el administrador de la aplicación es responsable del ensayo de una actualización multifase para mantener la disponibilidad. En una actualización multifase, el primer paso es actualizar a una versión intermedia de la aplicación que sea compatible con la versión anterior. El segundo paso es actualizar la versión final que interrumpe la compatibilidad con la versión anterior a la actualización, pero es compatible con la versión intermedia.

Los dominios de actualización se especifican en el manifiesto de clúster al configurar el clúster. Los dominios de actualización no reciben actualizaciones en un orden concreto. Un dominio de actualización es una unidad lógica de implementación para una aplicación. Los dominios de actualización permiten que la disponibilidad de los servicios se mantenga alta durante una actualización.

Las actualizaciones no graduales son posibles si la actualización se aplica a todos los nodos del clúster, que es el caso cuando la aplicación tiene un solo dominio de actualización. No se recomienda este enfoque, ya que el servicio deja de funcionar y no está disponible en el momento de la actualización. Además, Azure no ofrece ninguna garantía cuando se configura un clúster con un solo dominio de actualización.

Una vez completada la actualización, se mantendría la versión de todos los servicios y réplicas (instancias), es decir, si la actualización se realiza correctamente, se actualizarán a la nueva versión; si no se realiza correctamente y se revierte, se revertirían a la versión anterior.

## <a name="health-checks-during-upgrades"></a>Comprobaciones de estado durante las actualizaciones
Para realizar una actualización, las directivas de mantenimiento deben establecerse (o se pueden usar valores predeterminados). Una actualización se califica como correcta cuando se actualizan todos los dominios de actualización dentro de los tiempos de espera especificados, y dichos dominios se consideran correctos.  Un dominio de actualización correcto significa que el dominio de actualización pasa todas las comprobaciones de estado especificadas en la directiva de mantenimiento. Por ejemplo, una directiva de mantenimiento puede exigir que todos los servicios dentro de una instancia de la aplicación tienen que ser *correctos*, tal como Service Fabric define el estado correcto.

Las directivas de mantenimiento y comprobaciones de estado durante la actualización de Service Fabric son independientes de los servicios y aplicaciones. Es decir, no se realiza ninguna prueba específica del servicio.  Por ejemplo, el servicio podría tener un requisito de rendimiento, pero Service Fabric no tiene la información para comprobar el rendimiento. Consulte los [artículos sobre mantenimiento](service-fabric-health-introduction.md) para encontrar ver las comprobaciones que se llevan a cabo. Las comprobaciones que se producen durante una actualización incluyen pruebas para saber si el paquete de aplicación se copió correctamente, si se inició la instancia y así sucesivamente.

El estado de la aplicación es una agregación de las entidades secundarias de la aplicación. En resumen, Service Fabric evalúa el estado de la aplicación a través del estado notificado en la aplicación. También evalúa el estado de todos los servicios de la aplicación de este modo. Service Fabric evalúa aún más el estado de los servicios de aplicación al agregar el estado de sus elementos secundarios, como la réplica de servicio. Cuando se cumple la directiva de mantenimiento de la aplicación, se puede continuar con la actualización. Si se infringe la directiva de mantenimiento, se produce un error en la actualización de la aplicación.

## <a name="upgrade-modes"></a>Modos de actualización
El modo en que se recomienda que se actualice la aplicación es el modo supervisado, que es el de uso más común. El modo supervisado realiza la actualización en un dominio de actualización y, si se superan todas las comprobaciones de estado (según la directiva especificada), pasa al siguiente dominio de actualización automáticamente.  Si se produce un error en las comprobaciones de estado o los tiempos de espera llegan a su fin, la actualización se revierte para el dominio de actualización o el modo cambia a manual sin supervisión. Puede configurar la actualización para elegir uno de los dos modos en actualizaciones con errores. 

El modo manual sin supervisión requiere intervención manual tras cada actualización en un dominio de actualización para iniciar la actualización en el siguiente dominio de actualización. No se realiza ninguna comprobación de mantenimiento de Service Fabric. El administrador realiza las comprobaciones de estado antes de iniciar la actualización en el siguiente dominio de actualización.

## <a name="upgrade-default-services"></a>Actualización de servicios predeterminados
Algunos parámetros del servicio predeterminados definidos en el [manifiesto de aplicación](service-fabric-application-and-service-manifests.md) también se pueden actualizar como parte de una actualización de la aplicación. Solo se pueden cambiar como parte de una actualización los parámetros del servicio que admitan su modificación mediante [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps). El comportamiento del cambio de los servicios predeterminados durante la actualización de la aplicación es el siguiente:

1. Se crean los servicios predeterminados del nuevo manifiesto de aplicación que no existen aún en el clúster.
2. Se actualizan los servicios predeterminados que existen tanto en el anterior manifiesto de aplicación como en el nuevo. Los parámetros del servicio predeterminados del nuevo manifiesto de aplicación sobrescriben los parámetros del servicio existente. La actualización de aplicaciones se revertirá automáticamente tras un error de actualización de un servicio predeterminado.
3. Se eliminan los servicios predeterminados que no existen en el nuevo manifiesto de aplicación si estos existen en el clúster. **Tenga en cuenta que eliminar un servicio predeterminado dará como resultado la eliminación del estado del servicio y no se puede deshacer.**

Cuando se revierte una actualización de la aplicación, los parámetros del servicio predeterminados se revertir a sus valores antiguos antes de iniciar la actualización, pero los servicios eliminados no se pueden volver a crear con su estado anterior.

> [!TIP]
> La opción de configuración [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) del clúster debe ser *true* para habilitar las anteriores reglas 2 y 3 (actualización y eliminación del servicio predeterminado). Esta característica se admite a partir de Service Fabric versión 5.5.

## <a name="application-upgrade-flowchart"></a>Diagrama de flujo de actualización de la aplicación
El siguiente diagrama de flujo puede ayudarle a comprender el proceso de actualización de una aplicación de Service Fabric. En concreto, el flujo describe cómo los tiempos de espera, incluidos *HealthCheckStableDuration*, *HealthCheckRetryTimeout* y *UpgradeHealthCheckInterval*, ayudan a controlar cuándo se considera un éxito o un fracaso la actualización en un dominio de actualización.

![El proceso de actualización de una aplicación de Service Fabric][image]

## <a name="next-steps"></a>pasos siguientes
[actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

[actualización de aplicaciones mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) se explica en detalle lo que tiene que hacer para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
