---
title: "Escalado o reducción horizontal de un clúster de Service Fabric | Microsoft Docs"
description: "Escale o reduzca horizontalmente un clúster de Service Fabric para satisfacer la demanda y configure para ello reglas de escalado automático en cada tipo de nodo y conjunto de escalado de máquinas virtuales. Incorporación o eliminación de nodos de un clúster de Service Fabric"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 249fb4903c7b2de3ce290850a7759a4793f10aa7
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Escalado o reducción horizontal de un clúster de Service Fabric usando reglas de escalado automático
Los conjuntos de escalas de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo que se define en un clúster de Service Fabric está configurado como un conjunto de escalado de máquinas virtuales independiente. Cada tipo de nodo se puede escalar o reducir horizontalmente de forma independiente. Cada uno cuenta con diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Puede obtener más información al respecto en el documento [Relación entre los tipos de nodos de Service Fabric y los conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md). Dado que los tipos de nodo de Service Fabric del clúster están formados por conjuntos de escalado de máquinas virtuales en el back-end, tendrá que configurar reglas de escalado automático para cada tipo de nodo y conjunto de escalado de máquinas virtuales.

> [!NOTE]
> La suscripción debe contar con núcleos suficientes para agregar las nuevas máquinas virtuales que compondrán este clúster. En estos momentos, no hay ningún ninguna validación del modelo, así que si se alcanza algún límite de cuota, se producirá un error de tiempo de implementación.
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Selección del tipo de nodo o conjunto de escalado de máquinas virtuales para escalar
Actualmente, no es posible especificar reglas de escalado automático para conjuntos de escalado de máquinas virtuales mediante el portal, por lo que vamos a usar Azure PowerShell (1.0 +) para mostrar los tipos de nodo y luego agregarles reglas de escalado automático.

Para obtener la lista de conjuntos de escalado de máquinas virtuales que componen el clúster, ejecute los siguientes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <Virtual Machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Establecimiento de reglas de escalado automático para el tipo de nodo y conjunto de escalado de máquinas virtuales
Si el clúster tiene varios tipos de nodo, repita este procedimiento con cada tipo de nodo y conjunto de escalado de máquinas virtuales que quiera escalar o reducir horizontalmente. Antes de configurar el escalado automático, tenga en cuenta el número de nodos que debe tener. El número mínimo de nodos que debe tener para el tipo de nodo principal está controlado por el nivel de confiabilidad que haya elegido. Más información sobre los [niveles de confiabilidad](service-fabric-cluster-capacity.md).

> [!NOTE]
> Reducir verticalmente el tipo de nodo principal a un número inferior al mínimo hará que el clúster sea inestable o que se desactive. Como consecuencia, se puede producir la pérdida de datos de las aplicaciones y los servicios del sistema.
> 
> 

Actualmente, la característica de escalado automático no depende de las cargas que las aplicaciones pueden notificar a Service Fabric. En este momento, está controlada por los contadores de rendimiento que emiten cada una de las instancias del conjunto de escalado de máquinas virtuales.  

Siga estas instrucciones [para configurar el escalado automático para cada conjunto de escalado de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> En un escenario de reducción vertical, a menos que su tipo de nodo tenga un nivel de durabilidad Gold o Silver, deberá llamar al cmdlet [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) con el nombre de nodo adecuado.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Adición manual de máquinas virtuales a un tipo de nodo o conjunto de escalado de máquinas virtuales
Siga las instrucciones o el ejemplo de la [galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales en cada tipo de nodo. 

> [!NOTE]
> El proceso de agregar máquinas virtuales lleva tiempo, así que tenga en cuenta que las adiciones no se realizan de manera instantánea. Por tanto, planee agregar capacidad con la suficiente antelación como para que pasen más de 10 minutos antes de que la capacidad de las máquinas virtuales esté disponible para colocar las instancias de servicio o las réplicas.
> 
> 

## <a name="manually-remove-vms-from-the-primary-node-typevirtual-machine-scale-set"></a>Eliminación manual de máquinas virtuales del conjunto de escalado de máquinas virtuales o tipo de nodo principal
> [!NOTE]
> Los servicios del sistema de Service Fabric se ejecutan en el tipo de nodo principal del clúster. Por tanto, nunca apague las instancias de esos tipos de nodo ni las reduzca a un número inferior al que garantiza el nivel de confiabilidad. Consulte [la información sobre los niveles de confiabilidad aquí](service-fabric-cluster-capacity.md). 
> 
> 

Debe realizar los siguientes pasos en una instancia de máquina virtual cada vez. De este modo, los servicios del sistema (y los servicios con estado) se cerrarán correctamente en la instancia de máquina virtual que se va a quitar y en las nuevas réplicas creadas en otros nodos.

1. Ejecute [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) con RemoveNode para deshabilitar el nodo que va a quitar (la instancia más alta de dicho tipo de nodo).
2. Ejecute [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para asegurarse de que el nodo vaya a deshabilitarse. Si no es así, espere hasta que se deshabilite. Este paso no puede saltarse.
3. Siga las instrucciones o el ejemplo de la [galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales por uno de ese tipo de nodo. La instancia que se quita es la instancia de máquina virtual más alta. 
4. repita los pasos del 1 al 3 según vea necesario, pero nunca apague las instancias del nodo principal ni las reduzca a un número inferior al que garantiza el nivel de confiabilidad. Consulte [la información sobre los niveles de confiabilidad aquí](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevirtual-machine-scale-set"></a>Eliminación manual de máquinas virtuales del conjunto de escalado de máquinas virtuales o tipo de nodo no principal
> [!NOTE]
> Para los servicios con estado, necesita que un determinado número de nodos estén siempre activos para mantener la disponibilidad y preservar el estado del servicio. Como mínimo, necesita que el número de nodos sea igual al recuento de conjuntos de réplicas de destino de la partición o el servicio. 
> 
> 

Debe realizar los siguientes pasos en una instancia de máquina virtual cada vez. De este modo, los servicios del sistema (y los servicios con estado) se cerrarán correctamente en la instancia de máquina virtual que se va a quitar y en las nuevas réplicas creadas en otros nodos.

1. Ejecute [Disable-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) con RemoveNode para deshabilitar el nodo que va a quitar (la instancia más alta de dicho tipo de nodo).
2. Ejecute [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) para asegurarse de que el nodo vaya a deshabilitarse. Si no, espere hasta que se deshabilite. Este paso no puede saltarse.
3. Siga las instrucciones o el ejemplo de la [galería de plantillas de inicio rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para cambiar el número de máquinas virtuales por uno de ese tipo de nodo. Esta acción quitará la instancia de máquina virtual más alta. 
4. repita los pasos del 1 al 3 según vea necesario, pero nunca apague las instancias del nodo principal ni las reduzca a un número inferior al que garantiza el nivel de confiabilidad. Consulte [la información sobre los niveles de confiabilidad aquí](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamientos que se pueden observar en Service Fabric Explorer
Al escalar verticalmente un clúster, Service Fabric Explorer reflejará el número de nodos (instancias del conjunto de escalado de máquinas virtuales) que son parte del clúster.  Sin embargo, al reducir un clúster horizontalmente verá que el nodo o la instancia de máquina virtual quitados se mostrarán con un estado incorrecto, a menos que llame a [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) con el nombre de nodo apropiado.   

Esta es la explicación de este comportamiento.

Los nodos que se muestran en Service Fabric Explorer son el reflejo de lo que los servicios del sistema de Service Fabric (en concreto FM) saben acerca del número de nodos que tiene o ha tenido el clúster. Al reducir verticalmente el conjunto de escalado de máquinas virtuales, la máquina virtual se elimina pero el servicio del sistema FM sigue pensando que el nodo (que se asignó a la máquina virtual que se ha eliminado) volverá. Por lo tanto, Service Fabric Explorer sigue mostrando el nodo (aunque el estado de mantenimiento puede indicar error o desconocido).

Para tener la seguridad de que un nodo se elimina cuando una máquina virtual se elimina, tiene dos opciones:

1) Elija un nivel de durabilidad Gold o Silver para los tipos de nodo del clúster. Esto permitirá realizar la integración de la infraestructura. Con esto, a su vez, se eliminan automáticamente los nodos del estado de nuestros servicios del sistema (FM) cuando reduzca verticalmente.
Vea [los detalles sobre los niveles de durabilidad aquí](service-fabric-cluster-capacity.md).

2) Cuando haya reducido verticalmente la instancia de máquina virtual, necesitará llamar al [cmdlet Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

> [!NOTE]
> Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster, a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Lea la información siguiente para aprender sobre el planeamiento de la capacidad del clúster, la actualización de un clúster y los servicios de creación de particiones:

* [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md)
* [Actualización de un clúster de Service Fabric](service-fabric-cluster-upgrade.md)
* [Partición de Reliable Services de Service Fabric](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
