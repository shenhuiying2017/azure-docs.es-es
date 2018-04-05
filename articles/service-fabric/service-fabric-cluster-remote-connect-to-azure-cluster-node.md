---
title: Conexión remota a un nodo de clúster de Azure Service Fabric | Microsoft Docs
description: Aprenda a conectarse de forma remota a una instancia del conjunto de escalado (un nodo de clúster de Service Fabric).
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: 8c7d5446429089a0fc931175b55e81e1ad0c97a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conexión remota a una instancia de conjunto de escalado de máquinas virtuales o a un nodo de clúster
En un clúster de Service Fabric que se ejecuta Azure, cada tipo de nodo de clúster que defina [configura una escala independiente de la máquina virtual](service-fabric-cluster-nodetypes.md).  También puede conectarse de forma remota a instancias específicas del conjunto de escalado (o nodos de clúster).  A diferencia de las máquinas virtuales de instancia única, las instancias de conjuntos de escalado no tienen direcciones IP virtuales. Esto puede resultar un poco complicado si desea obtener una dirección IP y un puerto que pueda usar para conectarse de manera remota a una instancia específica.

Para buscar una dirección IP y un puerto que pueda usar para conectarse remotamente a una instancia concreta, realice los pasos siguientes.

1. Busque la dirección IP virtual del tipo de nodo obteniendo las reglas NAT de entrada para el Protocolo de escritorio remoto (RDP).

    Primero, obtenga los valores de las reglas NAT de entrada que se definieron como parte de la definición del recurso para `Microsoft.Network/loadBalancers`.
    
    En Azure Portal, en la página del equilibrador de carga, seleccione **Configuración** > **Reglas NAT de entrada**. Con ello obtendrá la dirección IP y el puerto que puede usar para conectarse de forma remota a la primera instancia del conjunto de escalado. 
    
    ![Equilibrador de carga][LBBlade]
    
    En la siguiente ilustración, la dirección IP y el puerto son **104.42.106.156** y **3389**.
    
    ![Reglas NAT][NATRules]

2. Busque el puerto que puede usar para conectarse de forma remota al nodo o a la instancia específica del conjunto de escalado.

    Asignación de instancias de conjuntos de escalado a los nodos. Use la información del conjunto de escalado para determinar el puerto exacto que se usará.
    
    Los puertos se asignan en un orden ascendente que coincide con el de la instancia del conjunto de escalado. En el ejemplo anterior del tipo de nodo FrontEnd, la siguiente tabla muestra los puertos para cada una de las cinco instancias de nodo. Aplique la misma asignación a la instancia del conjunto de escalado.
    
    | **Instancia del conjunto de escalado de máquinas virtuales** | **Puerto** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Conéctese de forma remota a la instancia específica del conjunto de escalado.

    En la siguiente ilustración se muestra el uso de la Conexión a Escritorio remoto para conectarse a la instancia FrontEnd_1 del conjunto de escalado:
    
    ![Conexión del Escritorio remoto][RDP]


Para ver los próximos pasos, lea los siguientes artículos:
* Consulte [Descripción general de la característica "Deploy anywhere" y comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md).
* Obtenga más información sobre la [seguridad del clúster](service-fabric-cluster-security.md).
* [Actualización de los valores del intervalo de puertos RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) en máquinas virtuales del clúster después de la implementación
* [Cambio de la contraseña y el nombre de usuario administrador](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para máquinas virtuales del clúster

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
