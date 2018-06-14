---
title: Tipos de nodos y conjuntos de escalado de máquinas virtuales de Azure Service Fabric | Microsoft Docs
description: Aprenda a relacionar los tipos de nodos de Azure Service Fabric con los conjuntos de escalado de máquinas virtuales y a conectarse de forma remota a una instancia de conjunto de escalado o un nodo de clúster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 84d7f407781f09fed4667a22f0a46bc72c6e02a9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212371"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nodos y conjuntos de escalado de máquinas virtuales de Azure Service Fabric
Los [conjuntos de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets) son un recurso de Azure Compute. Puede usarlos para implementar y administrar una colección de máquinas virtuales como conjunto. Cada tipo de nodo que defina en un clúster de Azure Service Fabric configura un escalado independiente.  El tiempo de ejecución de Service Fabric instalado en cada máquina virtual en el conjunto de escalado. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente; puede cambiar la SKU del sistema operativo que se ejecuta en cada nodo de clúster, tener diferentes conjuntos de puertos abiertos y usar distintas métricas de capacidad.

En la siguiente ilustración, se muestra un clúster que tiene dos tipos de nodos llamados front-end y back-end. A su vez, cada tipo de nodo tiene cinco nodos.

![Un clúster que tiene dos tipos de nodos][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Asignación de instancias de conjuntos de escalado de máquinas virtuales a los nodos
Como se muestra en la ilustración anterior, las instancias del conjunto de escalado empiezan por la instancia 0 y, a continuación, se van incrementando de uno en uno. La numeración se refleja en los nombres de los nodos. Por ejemplo, el nodo BackEnd_0 es la instancia 0 del conjunto de escalado de BackEnd. En concreto, este conjunto de escalado tiene cinco instancias, denominadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 y BackEnd_4.

Al escalar verticalmente un conjunto de escalado, se crea una nueva instancia. El nuevo nombre de la instancia de conjunto de escalado será normalmente el nombre del conjunto de escalado seguido del número de instancia. En nuestro ejemplo, es BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Asignación de los equilibradores de carga del conjunto de escalado a los tipos de nodos y conjuntos de escalado
Si ha implementado el clúster en Azure Portal o ha usado la plantilla de Azure Resource Manager de ejemplo, obtendrá una lista de todos los recursos en un grupo de recursos. Puede ver los equilibradores de carga de cada conjunto de escalado o tipo de nodo. El nombre del equilibrador de carga usa el formato siguiente: **LB-&lt;nombre del tipo de nodo&gt;**. Por ejemplo: LB-sfcluster4doc-0, tal y como se muestra en la ilustración siguiente:

![Recursos][Resources]


## <a name="next-steps"></a>Pasos siguientes
* Consulte [Descripción general de la característica "Deploy anywhere" y comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md).
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* [Conexión remota](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a la instancia específica del conjunto de escalado
* [Actualización de los valores del intervalo de puertos RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) en máquinas virtuales del clúster después de la implementación
* [Cambio de la contraseña y el nombre de usuario administrador](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para máquinas virtuales del clúster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
