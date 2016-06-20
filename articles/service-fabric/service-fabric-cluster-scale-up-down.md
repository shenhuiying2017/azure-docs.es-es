<properties
   pageTitle="Escalado o reducción vertical de un clúster de Service Fabric | Microsoft Azure"
   description="Escalado horizontal o reducción vertical de un clúster de Service Fabric para satisfacer la demanda mediante la configuración de reglas de escalado automático para cada tipo de nodo y conjunto de escalado de VM."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>


# Escalado horizontal o reducción vertical de un clúster de Service Fabric mediante reglas de escalado automático

Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo que se define en un clúster de Service Fabric está configurado como un conjunto de escalado de máquinas virtuales independiente. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Más información al respecto en el documento [Relación entre los tipos de nodos de Service Fabric y los conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md). Dado que los tipos de nodo de Service Fabric del clúster están formados por conjuntos de escalado de VM en el back-end, deberá configurar reglas de escalado automático para cada tipo de nodo y conjunto de escalado de VM.

>[AZURE.NOTE] La suscripción debe contar con núcleos suficientes para agregar las nuevas máquinas virtuales que compondrán este clúster. Actualmente no hay ningún ninguna validación del modelo, así que si se alcanza algún límite de cuota, se producirá un error de tiempo de implementación.

## Selección del tipo de nodo y conjunto de escalado de VM que se van a escalar

Actualmente, no es posible especificar las reglas de escalado automático para conjuntos de escalado de máquinas virtuales mediante el portal, por lo que vamos a usar Azure PowerShell (1.0 +) para mostrar los tipos de nodo y luego agregarles reglas de escalado automático.

Para obtener la lista de conjuntos de escalado de máquinas virtuales que componen el clúster, ejecute los siguientes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## Establecimiento de reglas de escalado automático para el tipo de nodo y conjunto de escalado de VM

Si el clúster tiene varios tipos de nodo, deberá realizar esta operación para cada uno de los tipos de nodo y conjuntos de escalado de VM que quiera escalar horizontalmente o reducir verticalmente. Antes de configurar el escalado automático, tenga en cuenta el número de nodos que debe tener. El número mínimo de nodos que debe tener para el tipo de nodo principal está controlado por el nivel de confiabilidad que haya elegido. Más información sobre los [niveles de confiabilidad](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Reducir verticalmente el tipo de nodo principal a un número inferior al mínimo hará que el clúster sea inestable o que se desactive. Como consecuencia, se puede producir la pérdida de datos de las aplicaciones y los servicios del sistema.

Actualmente, la característica de escalado automático no depende de las cargas que las aplicaciones pueden notificar a Service Fabric. En este momento, está controlada por los contadores de rendimiento que emiten cada una de las instancias del conjunto de escalado de VM.

Siga las instrucciones que se describen en [ para configurar el escalado automático para cada conjunto de escalado de VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] En un escenario de reducción vertical, a menos que su tipo de nodo tenga un nivel de durabilidad Gold o Silver deberá llamar al cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) con el nombre de nodo adecuado.

## Comportamientos que se pueden observar en Service Fabric Explorer

Al escalar verticalmente un clúster con Service Fabric Explorer se reflejará el número de nodos (instancias de conjunto de escalado de VM) que son parte del clúster. Sin embargo, al reducir un clúster verticalmente verá que el nodo o instancia de VM quitados se muestran con un estado incorrecto a menos que llame a [Remove-ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) con el nombre de nodo apropiado.

Esta es la explicación de este comportamiento.

Los nodos que se muestran en Service Fabric Explorer son el reflejo de lo que los servicios del sistema de Service Fabric (en concreto FM) saben acerca del número de nodos que tiene o ha tenido el clúster. Al reducir verticalmente el conjunto de escalado de VM, la VM se elimina pero el servicio del sistema FM sigue pensando que el nodo (que se asignó a la máquina virtual que se ha eliminado) volverá. Por lo tanto, Service Fabric Explorer sigue mostrando el nodo (aunque el estado de mantenimiento puede indicar error o desconocido).

Para tener la seguridad de que un nodo se elimina cuando una máquina virtual se elimina, tiene dos opciones:

(1) Elija un nivel de durabilidad Gold o Silver (disponible próximamente) para los tipos de nodo del clúster. Esto le proporcionará la integración de la infraestructura. Con esto, a su vez, se eliminan automáticamente los nodos del estado de nuestros servicios del sistema (FM) cuando reduzca verticalmente. Consulte [los detalles sobre los niveles de durabilidad](service-fabric-cluster-capacity.md)

2) Cuando haya reducido verticalmente la instancia de VM, deberá llamar al cmdlet [Remove-ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Los clústeres de Service Fabric requieren que un cierto número de nodos estén activos en todo momento con el fin de mantener la disponibilidad y conservar el estado (esto se conoce como "mantenimiento del cuórum"). Por lo tanto, normalmente no es seguro apagar todas las máquinas del clúster, a menos que antes haya realizado una [copia de seguridad completa del estado](service-fabric-reliable-services-backup-restore.md).

## Pasos siguientes
Lea la información siguiente para aprender sobre el planeamiento de la capacidad del clúster, la actualización de un clúster y los servicios de creación de particiones:

- [Consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md)
- [Actualización de un clúster de Service Fabric](service-fabric-cluster-upgrade.md)
- [Partición de Reliable Services de Service Fabric](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

<!---HONumber=AcomDC_0608_2016-->