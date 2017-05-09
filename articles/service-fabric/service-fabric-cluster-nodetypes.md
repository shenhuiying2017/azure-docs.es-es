---
title: "Tipos de nodos de Service Fabric y conjuntos de escalado de máquinas virtuales | Microsoft Docs"
description: "Describe cómo se relacionan los tipos de nodos de Service Fabric con los conjuntos de escalado de máquinas virtuales y cómo conectarse de forma remota a una instancia de conjunto de escalado de máquinas virtuales o un nodo de clúster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 32119a6ef586d616407c69e89a0d0f05758438bc
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Relación entre los tipos de nodos de Service Fabric y los conjuntos de escalado de máquinas virtuales
Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo que se define en un clúster de Service Fabric está configurado como un conjunto de escalado de VM independiente. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad.

En la siguiente captura de pantalla, se muestra un clúster que tiene dos tipos de nodos: front-end y back-end.  A su vez, cada tipo de nodo tiene cinco nodos.

![Captura de pantalla de un clúster que tiene dos tipos de nodos][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Asignación de instancias de conjuntos de escalado de máquinas virtuales a los nodos
Como se ha indicado anteriormente, las instancias de conjuntos de escalado de máquinas virtuales comienzan por la instancia 0 y van aumentando. La numeración se refleja en los nombres. Por ejemplo, BackEnd_0 es la instancia 0 del conjunto de escalado de máquinas virtuales de back-end. En concreto, este conjunto de escalado de máquinas virtuales tiene cinco instancias, denominadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 y BackEnd_4.

Al escalar verticalmente un conjunto de escalado de máquinas virtuales, se crea una nueva instancia. El nuevo nombre de instancia de conjunto de escalado de máquinas virtuales será normalmente el nombre del conjunto de escalado de máquinas virtuales seguido del número de instancia. En nuestro ejemplo, es BackEnd_5.

## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Asignación de equilibradores de carga de conjunto de escalado de máquinas virtuales a cada tipo de nodo o conjunto de escalado de máquinas virtuales
Si ha implementado el clúster desde el portal o ha usado la plantilla de Azure Resource Manager que le hemos proporcionado, al mostrar una lista de todos los recursos incluidos en un grupo de recursos, verá los equilibradores de carga para cada conjunto de escalado de VM o tipo de nodo.

El nombre sería algo parecido a: **LB-&lt;Nombre del tipo de nodo&gt;**. Por ejemplo, LB-sfcluster4doc-0, como se muestra en esta captura de pantalla:

![Recursos][Resources]

## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Conexión remota a una instancia de conjunto de escalado de máquinas virtuales o un nodo de clúster
Cada tipo de nodo que se define en un clúster está configurado como un conjunto de escalado de VM independiente.  Esto significa que los tipos de nodos se pueden escalar o reducir verticalmente de forma independiente y que pueden estar formados por diferentes SKU de máquina virtual. A diferencia de las máquinas virtuales de instancia única, las instancias de conjunto de escalado de máquinas virtuales no obtienen una dirección IP virtual por sí mismas. Por lo que puede ser un poco complicado si desea obtener una dirección IP y un puerto que pueda usar para conectarse de manera remota a una instancia específica.

Estos son los pasos que puede seguir para detectarlos.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Paso 1: Averiguar la dirección IP virtual para el tipo de nodo y, a continuación, las reglas NAT de entrada para RDP
Para conseguirlo, debe obtener los valores de las reglas NAT de entrada que se especificaron como parte de la definición de recursos para **Microsoft.Network/loadBalancers**.

En el portal, vaya a la hoja del equilibrador de carga y, a continuación, acceda a **Configuración**.

![LBBlade][LBBlade]

En **Configuración**, haga clic en **Reglas NAT de entrada**. Aquí verá la dirección IP y el puerto que puede usar para conectarse de forma remota a la primera instancia de conjunto de escalado de máquinas virtuales. En la captura de pantalla siguiente, los valores son **104.42.106.156** y **3389**.

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Paso 2: Buscar el puerto que puede usar para conectarse de forma remota al nodo o a la instancia específica del conjunto de escalado de máquinas virtuales
Anteriormente en este documento, se describió la forma de asignar instancias de conjunto de escalado de máquinas virtuales a los nodos. Usaremos ese procedimiento para averiguar el puerto exacto.

Los puertos se asignan en orden ascendente de la instancia del conjunto de escalado de máquinas virtuales. Así que en mi ejemplo del tipo de nodo de front-end, los puertos de cada una de las cinco instancias son los siguientes: Ahora debe realizar la misma asignación para la instancia del conjunto de escalado de máquinas virtuales.

| **Instancia de conjunto de escalado de VM** | **Puerto** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Paso 3: Conectarse remotamente a la instancia específica del conjunto de escalado de máquinas virtuales
En la siguiente captura de pantalla, se usa la conexión a escritorio remoto para conectarse a FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Cómo cambiar los valores del intervalo de puertos RDP
### <a name="before-cluster-deployment"></a>Antes de la implementación del clúster
Cuando se configura el clúster mediante una plantilla de Azure Resource Manager, se puede especificar el intervalo en **inboundNatPools**.

Acceda a la definición de recursos para **Microsoft.Network/loadBalancers**. Ahí encontrará la descripción de **inboundNatPools**.  Reemplace los valores de *frontendPortRangeStart* y *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Después de la implementación del clúster
Esto es un poco más complicado y puede provocar que las máquinas virtuales se reciclen. Ahora tendrá que establecer nuevos valores usando Azure PowerShell. Asegúrese de que Azure PowerShell 1.0 esté instalado en su equipo (o una versión posterior). Si no lo ha hecho antes, se aconseja encarecidamente que siga los pasos descritos en [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview)

Inicie sesión en la cuenta de Azure. Si este comando de PowerShell da error por algún motivo, debe comprobar si tiene instalado correctamente Azure PowerShell.

```
Login-AzureRmAccount
```

Ejecute lo siguiente para obtener detalles sobre el equilibrador de carga y verá los valores para la descripción de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Ahora configure *frontendPortRangeEnd* y *frontendPortRangeStart* con los valores que desee.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Pasos siguientes
* [Descripción general de la característica "Deploy anywhere" y comparación con los clústeres administrados de Azure](service-fabric-deploy-anywhere.md)
* [Seguridad de clúster](service-fabric-cluster-security.md)
* [ SDK de Service Fabric e introducción](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

