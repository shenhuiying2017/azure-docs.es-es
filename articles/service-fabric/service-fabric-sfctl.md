---
title: 'CLI de Azure Service Fabric: sfctl | Microsoft Docs'
description: Se describen los comandos de sfctl de la CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2dd30470ee0f6c038a8601bfca73fc97091de2fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl"></a>sfctl 
Comandos para administrar clústeres y entidades de Service Fabric. Esta versión es compatible con el tiempo de ejecución de Service Fabric 6.0. Comandos siguen el patrón de nombre-verbo; consulte los siguientes subgrupos para obtener más información.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|Descripción|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| Cree, elimine y administre aplicaciones y tipos de aplicaciones.|
| [chaos](service-fabric-sfctl-chaos.md)   | Inicie, detenga e informe sobre el servicio de prueba de Chaos.|
| [cluster](service-fabric-sfctl-cluster.md) | Seleccione, administre y opere clústeres de Service Fabric.|
| [compose](service-fabric-sfctl-compose.md) | Cree, elimine y administre aplicaciones de Docker Compose.|
| [is](service-fabric-sfctl-is.md)      | Consulte y envíe comandos al servicio de infraestructura.|
| [node](service-fabric-sfctl-node.md)    | Administre los nodos que forman un clúster.|
| [partition](service-fabric-sfctl-partition.md)  | Consulte y administre las particiones para cualquier servicio.|
| [rpm](service-fabric-sfctl-rpm.md)        | Consulte y envíe comandos al servicio del administrador de reparaciones.|
| [replica](service-fabric-sfctl-replica.md) | Administre las réplicas que pertenecen a las particiones del servicio.|
| [service](service-fabric-sfctl-service.md) | Cree, elimine y administre servicios, tipos de servicio y paquetes de servicio.|
| [store](service-fabric-sfctl-store.md)   | Realice operaciones básicas a nivel de archivo en el almacén de imágenes del clúster.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).