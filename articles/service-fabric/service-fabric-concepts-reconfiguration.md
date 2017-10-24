---
title: "Reconfiguración en Azure Service Fabric | Microsoft Docs"
description: "Reconfiguración de particiones en Service Fabric"
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
ms.openlocfilehash: ee2808286012720210070817d7ee6c2349247858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguración en Azure Service Fabric
Una *configuración* se define como las réplicas y sus roles para una partición de un servicio con estado.

Una *reconfiguración* es el proceso de mover una configuración a otra configuración. Se realiza un cambio en el conjunto de réplicas en una partición de un servicio con estado. La configuración anterior se denomina *configuración anterior (PC)* y la nueva configuración se llama *configuración actual (CC)*. El protocolo de reconfiguración de Azure Service Fabric conserva la coherencia y mantiene la disponibilidad durante cualquier cambio en el conjunto de réplicas.

El Administrador de conmutación por error inicia las reconfiguraciones en respuesta a diversos eventos del sistema. Por ejemplo, si la principal no funciona, se inicia una reconfiguración para promover una secundaria activa a una principal. Otro ejemplo es en respuesta a actualizaciones de aplicaciones cuando es posible que sea necesario mover la principal a otro nodo para actualizar el nodo.

## <a name="reconfiguration-types"></a>Tipos de reconfiguración
Las reconfiguraciones se pueden clasificar en dos tipos:

- Reconfiguraciones donde cambia la principal:
    - **Conmutación por error**: las conmutaciones por error son reconfiguraciones en respuesta al error de una principal en ejecución.
    - **SwapPrimary**: los intercambios son reconfiguraciones donde Service Fabric debe mover una principal en ejecución de un nodo a otro normalmente en respuesta a la actualización o el equilibrio de carga.

- Reconfiguraciones donde no cambia la principal.

## <a name="reconfiguration-phases"></a>Fases de reconfiguración
Una reconfiguración se realiza en varias fases:

- **Fase 0**: esta fase tiene lugar en reconfiguraciones principales de intercambio donde la principal actual transfiere su estado a la nueva principal y las transiciones a la secundaria activa.

- **Fase 1**: esta fase tiene lugar durante las reconfiguraciones donde cambia la principal. Durante esta fase, Service Fabric identifica entre las réplicas actuales cuál es la principal correcta. Esta fase no es necesaria durante las reconfiguraciones principales de intercambio porque ya se ha elegido la nueva principal. 

- **Fase 2**: durante esta fase, Service Fabric garantiza que todos los datos estén disponibles en una mayoría de las réplicas de la configuración actual.

Hay varias fases más que son solo para uso interno.

## <a name="stuck-reconfigurations"></a>Reconfiguraciones bloqueadas
Las reconfiguraciones pueden *bloquearse* por numerosas razones. Entre algunas de las razones habituales se incluyen:

- **Réplicas fuera de servicio**: algunas fases de reconfiguración requieren que una mayoría de las réplicas de la configuración estén instaladas
- **Problemas de red o comunicación**: las reconfiguraciones requieren conectividad de red entre distintos nodos.
- **Errores de API**: el protocolo de reconfiguración requiere que las implementaciones del servicio completen determinadas API. Por ejemplo: no responder al token de cancelación en un servicio de confianza provoca el bloqueo de las reconfiguraciones SwapPrimary.

Use los informes de mantenimiento de componentes del sistema como System.FM, System.RA y System.RAP para diagnosticar dónde se bloquea una reconfiguración. En la [página de los informes de mantenimiento del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) se describen estos informes de mantenimiento.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

- [Ciclo de vida de Reliable Services: C#](service-fabric-reliable-services-lifecycle.md)
- [Informes de mantenimiento del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas e instancias](service-fabric-concepts-replica-lifecycle.md)
