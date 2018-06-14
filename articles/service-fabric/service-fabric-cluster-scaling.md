---
title: Escalado de un clúster de Azure Service Fabric | Microsoft Docs
description: Obtenga información acerca de cómo escalar horizontal y verticalmente clústeres de Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: f9e3f190decdc907cf57a0235b9d7142081bd2f1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208036"
---
# <a name="scaling-service-fabric-clusters"></a>Escalado de clústeres de Service Fabric
Un clúster de Service Fabric es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Un equipo o máquina virtual que forma parte de un clúster se denomina nodo. Los clústeres pueden contener potencialmente miles de nodos. Después de crear un clúster de Service Fabric, puede escalar el clúster horizontalmente (cambiar el número de nodos) o verticalmente (cambiar los recursos de los nodos).  Puede escalar el clúster en cualquier momento, incluso con cargas de trabajo en ejecución en el clúster.  Según se escala el clúster, las aplicaciones se escalan automáticamente.

¿Por qué escalar el clúster? Las necesidades de las aplicaciones cambian a lo largo del tiempo.  Puede necesitar aumentar los recursos del clúster para cumplir con aumentos del tráfico de red o de la carga de trabajo de la aplicación o reducir los recursos del clúster cuando la demanda baja.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Escalado horizontal
Cambia el número de nodos del clúster.  Una vez que los nuevos nodos se unen al clúster, el [Administrador de recursos del clúster](service-fabric-cluster-resource-manager-introduction.md) mueve servicios a ellos, lo que reduce la carga en los nodos existentes.  Si los nodos del clúster no se usan de forma eficaz, puede reducir el número de nodos.  Cuando los nodos abandonan el clúster, los servicios son trasladados de dichos nodos y la carga aumenta en los nodos restantes.  La reducción del número de nodos en un clúster que se ejecuta en Azure puede ahorrarle dinero, ya que se paga por el número de máquinas virtuales utilizadas y no por la carga de trabajo en esas máquinas virtuales.  

- Ventajas: escala infinita, en teoría.  Si la aplicación está diseñada para ofrecer escalabilidad, puede habilitar un crecimiento ilimitado agregando más nodos.  Las herramientas de los entornos de nube permiten agregar o quitar nodos fácilmente, por lo que resulta sencillo ajustar la capacidad y solo se paga por los recursos que utiliza.  
- Inconvenientes: las aplicaciones deben [diseñarse para ofrecer escalabilidad](service-fabric-concepts-scalability.md).  Las bases de datos y la persistencia de la aplicación pueden requerir un trabajo de arquitectura adicional para el escalado.  Las[colecciones confiables](service-fabric-reliable-services-reliable-collections.md) de los servicios con estado de Service Fabric, sin embargo, hacen mucho más fácil el escalado de los datos de la aplicación.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Escalado vertical 
Cambia los recursos (CPU, memoria o almacenamiento) de los nodos del clúster.
- Ventajas: el software y la arquitectura de la aplicación siguen siendo los mismos.
- Inconvenientes: escala finita, ya que hay un límite respecto a cuánto puede aumentar los recursos en los nodos individuales. Tiempo de inactividad, ya que tendrá máquinas físicas o virtuales sin conexión con el fin de agregar o quitar recursos.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Escalado horizontal de un clúster de Azure
Los conjuntos de escalas de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo que se define en un clúster de Azure está [configurado como un conjunto de escalado independiente](service-fabric-cluster-nodetypes.md). Cada tipo de nodo se puede escalar o reducir horizontalmente de forma independiente. Cada uno cuenta con diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. 

Al cambiar la escala de un clúster de Azure, tenga en cuenta las directrices siguientes:
- Los tipos de nodo principal que ejecutan cargas de trabajo de producción siempre deben tener cinco o más nodos.
- Los tipos de nodo no principal que ejecutan cargas de trabajo de producción con estado siempre deben tener cinco o más nodos.
- Los tipos de nodo no principal que ejecutan cargas de trabajo de producción sin estado siempre deben tener dos o más nodos.
- Cualquier tipo de nodo del [nivel de durabilidad](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Gold o Silver siempre debe tener cinco o más nodos.
- No elimine nodos o instancias de máquina virtual aleatorias de un tipo de nodo, use siempre la característica de reducción vertical del conjunto de escalado de máquinas virtuales. La eliminación de instancias de máquina virtual aleatorias puede afectar negativamente a la capacidad de los sistemas para equilibrar la carga correctamente.
- Si usa reglas de escalado automático, establezca dichas reglas para que la reducción horizontal (eliminar instancias de máquina virtual) se realice en un nodo cada vez. No es seguro reducir verticalmente más de una instancia a la vez.

Dado que los tipos de nodo de Service Fabric del clúster están formados por conjuntos de escalado de máquinas virtuales en el back-end, puede [configurar reglas de escalado automático o manual](service-fabric-cluster-scale-up-down.md) para cada tipo de nodo o conjunto de escalado de máquinas virtuales.

### <a name="programmatic-scaling"></a>Escalado mediante programación
En muchos escenarios, el [escalado de un clúster de forma manual o mediante reglas de escalado automático](service-fabric-cluster-scale-up-down.md) son buenas soluciones. En escenarios más avanzados, pueden no ser la mejor opción. Entre las desventajas potenciales de estos métodos se incluyen:

- El escalado manual requiere que inicie sesión y solicite de forma explícita las operaciones de escalado. Si las operaciones de escalado se requieren con frecuencia o en momentos imprevisibles, este enfoque puede no ser una buena solución.
- Cuando las reglas de escalado automático quitan una instancia de un conjunto de escalado de máquinas virtuales, no quitan automáticamente el conocimiento de ese nodo desde el clúster de Service Fabric asociado, a menos que el tipo de nodo tenga un nivel de durabilidad Silver o Gold. Dado que las reglas de escalado automático funcionan a nivel de conjunto de escalado (en lugar de al nivel de Service Fabric), las reglas de escalado automático pueden quitar nodos de Service Fabric sin cerrarlos correctamente. Esta eliminación forzada de un nodo dejará un estado de nodo de Service Fabric "fantasma" después de operaciones de escalado. Es necesario que una persona (o un servicio) limpie periódicamente el estado de los nodos eliminados en el clúster de Service Fabric.
- Un tipo de nodo con un nivel de durabilidad Gold o Silver limpia automáticamente los nodos eliminados, por lo que no se requiere una limpieza adicional.
- Aunque hay [muchas métricas](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) compatibles con las reglas de escalado automático, se trata aún de un conjunto limitado. Si su escenario requiere un escalado automático basado en alguna métrica que no se trata en ese conjunto, es posible que las reglas de escalado automático no sean una buena opción.

La forma en la que debe enfocar el escalado de Service Fabric depende de su escenario. Si el escalado no es común, la capacidad de agregar o quitar nodos de forma manual es probablemente suficiente. Para escenarios más complejos, las reglas de escalado automático y los SDK con la capacidad de escalar mediante programación ofrecen unas eficaces alternativas.

Hay algunas API de Azure que permiten que las aplicaciones trabajen de forma programática con conjuntos de escalado de máquinas virtuales y clústeres de Service Fabric. Si las opciones existentes de escalado automático no funcionan para su escenario, estas API permiten implementar una lógica de escalado personalizada. 

Un enfoque para implementar esta funcionalidad de escalado automático "casero" consiste en agregar un nuevo servicio sin estado a la aplicación de Service Fabric para administrar las operaciones de escalado. Crear su propio servicio de escalado proporciona el mayor grado de control y personalización sobre el comportamiento del escalado de su aplicación. Esto puede ser útil para escenarios que requieren un control preciso sobre cuándo y cómo una aplicación se escala o reduce horizontalmente. Sin embargo, este control acarrea el inconveniente de la complejidad del código. Para usar este enfoque tiene que tener en propiedad código de escalado, lo que no es fácil. Dentro del método `RunAsync` del servicio, un conjunto de desencadenadores puede determinar si es necesario el escalado (incluyendo la comprobación de parámetros como el tamaño máximo del clúster y las recuperaciones de escalado).   

La API que se utiliza para las interacciones de conjunto de escalado de máquinas virtuales (tanto para comprobar el número actual de instancias de máquina virtual como para modificarlo) es la fluida [Azure Compute Management Library](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Esta biblioteca de proceso fluida proporciona una API fácil de usar para interactuar con los conjuntos de escalado de máquinas virtuales.  Para interactuar con el clúster de Service Fabric propiamente dicho, utilice [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Sin embargo, el código de escalado no tiene por qué ejecutarse como un servicio en el clúster que se va a escalar. Ambos `IAzure` y `FabricClient` pueden conectarse a sus recursos asociados de Azure de forma remota, por lo que el servicio de escalado podría ser fácilmente una aplicación de consola o el servicio de Windows que se ejecuta desde fuera de la aplicación de Service Fabric.

En función de estas limitaciones, puede que desee [implementar modelos de escalado automático más personalizados](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Escalado horizontal de un clúster independiente
Los clústeres independientes le permiten implementar el clúster de Service Fabric de modo local o en el proveedor en la nube de su elección.  Los tipos de nodo están formados por máquinas físicas o virtuales, según la implementación. En comparación con los clústeres que se ejecutan en Azure, el proceso de escalado de un clúster independiente es un poco más complicado.  Debe cambiar manualmente el número de nodos del clúster y, a continuación, ejecutar una actualización de la configuración de clúster.

La eliminación de nodos puede iniciar varias actualizaciones. Algunos nodos están marcados con la etiqueta `IsSeedNode=”true”` y se pueden identificar consultando el manifiesto del clúster mediante [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). La eliminación de estos nodos puede tardar más que la de otros, ya que se tendrán que mover los nodos de inicialización en estos escenarios. El clúster debe tener un mínimo de tres nodos del tipo de nodo principal.

Al escalar un clúster independiente, tenga en cuenta las directrices siguientes:
- Los nodos principales se deben sustituir uno por uno, en lugar de quitarlos y agregarlos de forma masiva.
- Antes de eliminar un tipo de nodo, compruebe que no hay algún nodo que haga referencia a dicho tipo. Elimine estos nodos antes de quitar el tipo de nodo correspondiente. Una vez se han eliminado todos los nodos correspondientes, puede eliminar el valor de NodeType de la configuración del clúster y comenzar una actualización de la configuración mediante [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Para más información, consulte [Escalado de un clúster independiente](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Escalado vertical de un clúster de Azure
Los conjuntos de escalas de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Cada tipo de nodo que se define en un clúster de Azure está [configurado como un conjunto de escalado independiente](service-fabric-cluster-nodetypes.md). Cada tipo de nodo, a continuación, se puede administrar por separado.  El escalado vertical de un tipo de nodo implica la modificación de la SKU de las instancias de las máquinas virtuales del conjunto de escalado. 

> [!WARNING]
> Se recomienda no cambiar la SKU de las máquinas virtuales de un tipo de nodo o conjunto de escalado a menos que se esté ejecutando en la [durabilidad Silver o mayor](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Modificar el tamaño de la SKU de VM constituye una operación de infraestructura local de destrucción de datos. Sin la posibilidad de retrasar o supervisar este cambio, es posible que la operación pueda provocar una pérdida de datos en los servicios con estado o bien causar otros problemas de funcionamiento imprevistos, incluso en las cargas de trabajo sin estado. 
>

Al cambiar la escala de un clúster de Azure, tenga en cuenta las directrices siguientes:
- Si reduce verticalmente un tipo de nodo principal, no deberá hacerlo más de lo que permite el [nivel de confiabilidad](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster).

El proceso de escalado vertical de un tipo de nodo es diferente dependiendo de si es un tipo de nodo principal o no principal.

### <a name="scaling-non-primary-node-types"></a>Escalado de tipos de nodo no principales
Cree un nuevo tipo de nodo con los recursos que necesita.  Actualice las restricciones de posición de los servicios en ejecución para que incluyan el nuevo tipo de nodo.  Gradualmente (de uno en uno), reduzca hasta cero el número de instancias de la instancia del tipo de nodo anterior para que no se vea afectada la confiabilidad del clúster.  Los servicios se migrarán gradualmente al nuevo tipo de nodo con la cancelación de las asignaciones del tipo de nodo anterior.

### <a name="scaling-the-primary-node-type"></a>Escalado del tipo de nodo principal
Se recomienda que no cambie la SKU de las máquinas virtuales del tipo de nodo principal. Si necesita más capacidad en el clúster, se recomienda agregar más instancias. 

Si ello no es posible, puede crear un clúster nuevo y [restaurar el estado de la aplicación](service-fabric-reliable-services-backup-restore.md) (si procede) a partir del clúster anterior. No es preciso restaurar el estado de ningún servicio del sistema, ya que se vuelven a crear al implementar las aplicaciones en el clúster nuevo. Si ha ejecutado aplicaciones sin estado en el clúster, lo único que hace es implementar las aplicaciones en el clúster nuevo, no hay nada que para restaurar. Si decide ir por la ruta no compatible y desea cambiar la SKU de la máquina virtual, realice modificaciones en la definición del modelo del conjunto de escalado de máquinas virtuales para que refleje la SKU nueva. Si el clúster tiene un solo tipo de nodo, asegúrese de que las aplicaciones con estado responden a todos los [eventos del ciclo de vida de la réplica del servicio](service-fabric-reliable-services-lifecycle.md) (como que la réplica en creación está bloqueada) en el momento adecuado y que la duración de la regeneración de la réplica del servicio es inferior a cinco minutos (en el nivel de durabilidad Silver). 

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre [escalabilidad de aplicaciones](service-fabric-concepts-scalability.md).
* [Escalado horizontal de un clúster de Azure](service-fabric-tutorial-scale-cluster.md).
* [Escalado de un clúster de Azure mediante programación](service-fabric-cluster-programmatic-scaling.md) con el SDK de proceso de Azure.
* [Escalado horizontal de un clúster independiente](service-fabric-cluster-windows-server-add-remove-nodes.md).

