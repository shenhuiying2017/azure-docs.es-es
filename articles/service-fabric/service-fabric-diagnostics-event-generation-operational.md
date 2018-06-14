---
title: Lista de eventos de Azure Service Fabric | Microsoft Docs
description: Lista completa de eventos proporcionados por Azure Service Fabric como ayuda para supervisar clústeres.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212966"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos de Service Fabric 

Service Fabric expone un conjunto principal de eventos de clúster para informarle sobre el estado del clúster como [eventos de Service Fabric](service-fabric-diagnostics-events.md). Estos se basan en las acciones realizadas por Service Fabric en los nodos y el clúster, o las decisiones de administración tomadas por un operador/propietario del clúster. Para tener acceso a estos eventos, puede consultar [EventStore](service-fabric-diagnostics-eventstore.md) en el clúster o a través del canal operativo. En equipos con Windows, el canal operativo también está enlazado a EventLog, por lo que puede ver los eventos de Service Fabric en el Visor de eventos. 

>[!NOTE]
>Para obtener una lista de eventos de Service Fabric para clústeres en versiones anteriores a la 6.2, consulte la sección siguiente. 

Aquí tiene una lista de todos los eventos disponible en la plataforma, ordenada por la entidad a la que están asignados.

## <a name="cluster-events"></a>Eventos de clúster

**Eventos de actualización de clústeres**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informativo | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informativo | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informativo | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informativo | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informativo | 1 |

**Eventos de informe de estado de clústeres**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informativo | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informativo | 1 |

**Eventos del servicio Chaos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Capacidad de prueba | Informativo | 1 |
| 50023 | ChaosStoppedEvent | Capacidad de prueba | Informativo | 1 |

## <a name="node-events"></a>Eventos de nodos

**Eventos del ciclo de vida de los nodos** 

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informativo | 1 |
| 18603 | NodeUpOperational | FM | Informativo | 1 |
| 18604 | NodeDownOperational | FM | Informativo | 1 |
| 18605 | NodeAddedOperational | FM | Informativo | 1 |
| 18606 | NodeRemovedOperational | FM | Informativo | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informativo | 1 |
| 25620 | NodeOpening | FabricNode | Informativo | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informativo | 1 |
| 25623 | NodeClosing | FabricNode | Informativo | 1 |
| 25624 | NodeClosed | FabricNode | Informativo | 1 |
| 25625 | NodeAborting | FabricNode | Informativo | 1 |
| 25626 | NodeAborted | FabricNode | Informativo | 1 |

**Eventos de informe de estado de nodos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informativo | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informativo | 1 |

**Eventos de nodos de Chaos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Capacidad de prueba | Informativo | 1 |

## <a name="application-events"></a>Eventos de aplicación

**Eventos del ciclo de vida de aplicaciones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informativo | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informativo | 1 |
| 23083 | ProcessExitedOperational | Hospedaje | Informativo | 1 |

**Eventos de actualización de aplicaciones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informativo | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informativo | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informativo | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informativo | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informativo | 1 |

**Eventos de informe de estado de aplicaciones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informativo | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informativo | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informativo | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informativo | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informativo | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informativo | 1 |

**Eventos de aplicación Chaos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Capacidad de prueba | Informativo | 1 |

## <a name="service-events"></a>Eventos de servicios

**Eventos de ciclo de vida de servicios**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informativo | 1 |
| 18658 | ServiceDeletedOperational | FM | Informativo | 1 |

**Eventos de informe de estado de servicios**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informativo | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informativo | 1 |

## <a name="partition-events"></a>Eventos de particiones

**Eventos de movimiento de particiones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Informativo | 1 |

**Eventos de informe de estado de particiones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informativo | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informativo | 1 |

**Eventos de particiones de Chaos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |

**Eventos de análisis de particiones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Capacidad de prueba | Informativo | 1 |

## <a name="replica-events"></a>Eventos de réplicas

**Eventos de informe de estado de réplicas**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informativo | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informativo | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informativo | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informativo | 1 |

**Eventos de réplicas de Chaos**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Capacidad de prueba | Informativo | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Capacidad de prueba | Informativo | 1 |

## <a name="container-events"></a>Eventos de contenedores

**Eventos de ciclo de vida de contenedores** 

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hospedaje | Informativo | 1 |
| 23075 | ContainerDeactivatedOperational | Hospedaje | Informativo | 1 |
| 23082 | ContainerExitedOperational | Hospedaje | Informativo | 1 |

## <a name="other-events"></a>Otros eventos

**Eventos de correlaciones**

| EventId | NOMBRE | Origen (tarea) | Nivel | Versión |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Capacidad de prueba | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores a la versión 6.2

A continuación figura una lista completa de los eventos proporcionados por Service Fabric antes de la versión 6.2.

| EventId | NOMBRE | Origen (tarea) | Nivel |
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
