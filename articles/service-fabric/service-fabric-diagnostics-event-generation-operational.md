---
title: Canal de operaciones de Azure Service Fabric | Microsoft Docs
description: "Lista completa de los registros generados en el canal operativo de clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2017
---
# <a name="operational-channel"></a>Canal operativo 

El canal operativo consta de registros de acciones de alto nivel llevadas a cabo por Service Fabric en los nodos y el clúster. Cuando los diagnósticos están habilitados para un clúster, el agente de Azure Diagnostics se implementa en el clúster y se configura de forma predeterminada para leer los registros del canal operativo. Obtenga más información sobre la configuración del [agente de Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para modificar la configuración de diagnósticos del clúster para seleccionar más registros o contadores de rendimiento. 

## <a name="operational-channel-logs"></a>Registros de canal operativo 

A continuación figura una lista completa de los registros proporcionados por Service Fabric en el canal operativo. 

| EventId | Nombre | Origen (tarea) | Nivel |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo |
| 25622 | NodeOpenedFailed | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | NodeClosed | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContainerActivated | Hospedaje | Informativo |
| 23075 | ContainerDeactivated | Hospedaje | Informativo |
| 29620 | ApplicationCreated | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | ApplicationDeleted | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiceDeleted | FM | Informativo |

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [generación de eventos general en el nivel de plataforma ](service-fabric-diagnostics-event-generation-infra.md) en Service Fabric
* Modificación de la configuración de [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para recopilar más registros
* [Configuración de Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver los registros de canal operativo
