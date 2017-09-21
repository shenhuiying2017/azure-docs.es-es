---
title: "Regulación de recursos de Azure Service Fabric para contenedores y servicios | Microsoft Docs"
description: "Azure Service Fabric permite especificar los límites de recursos de servicios que se ejecutan dentro o fuera de contenedores."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>Regulador de recursos 

Cuando se ejecutan varios servicios en el mismo clúster o nodo, es posible que un servicio pueda consumir más recursos y privar así a otros servicios. A este problema se le conoce como el problema del entorno ruidoso. Service Fabric permite al desarrollador establecer reservas y límites por servicio, a fin de garantizar la disponibilidad de recursos y, además, limita el uso de recursos.

>
> Antes de continuar con este artículo, debe familiarizarse con el [modelo de aplicación Service Fabric](service-fabric-application-model.md) y con el [modelo de hospedaje de Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de regulación de recursos 

Service Fabric admite la regulación de recursos en función del [paquete de servicio](service-fabric-application-model.md). Los recursos asignados al paquete de servicio pueden dividirse además entre paquetes de código. Los límites de recursos especificados también suponen la reserva de los recursos. Service Fabric admite la determinación de CPU y memoria por cada paquete de servicio mediante la utilización de dos [métricas](service-fabric-cluster-resource-manager-metrics.md) integradas:

* CPU (nombre de métrica `servicefabric:/_CpuCores`): un núcleo es un núcleo lógico que se encuentra disponible en el equipo host, y todos los núcleos de todos los nodos se ponderan de la misma forma.
* Memoria (nombre de métrica `servicefabric:/_MemoryInMB`): la memoria se expresa en megabytes y se asigna a la memoria física que está disponible en el equipo.

Para estas dos métricas, el [Administrador de recursos de clúster](service-fabric-cluster-resource-manager-cluster-description.md) realiza un seguimiento de la capacidad total del clúster, la carga de cada nodo del clúster y los recursos restantes del clúster. Estas dos métricas son equivalentes a cualquier otra métrica de usuario o personalizada, por lo que todas las características existentes se pueden usar con ellas:
* El clúster puede [equilibrarse](service-fabric-cluster-resource-manager-balancing.md) en función de estas dos métricas (comportamiento predeterminado).
* El clúster puede [desfragmentarse](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en función de estas dos métricas.
* Para [describir un clúster](service-fabric-cluster-resource-manager-cluster-description.md), se puede establecer la capacidad de almacenamiento en búfer para estas dos métricas.

El [informe de carga dinámica](service-fabric-cluster-resource-manager-metrics.md) no es compatible con estas métricas, y las cargas para estas métricas se definen en el momento de la creación.

## <a name="resource-governance-mechanism"></a>Mecanismo de gobierno de recursos

El entorno de tiempo de ejecución de Service Fabric no proporciona actualmente reserva de recursos. Cuando se abre un proceso o un contenedor, el entorno de tiempo de ejecución establece los límites de recursos en las cargas definidas en tiempo de creación. Además, el entorno de tiempo de ejecución no admite la apertura de nuevos paquetes de servicio cuando se superan los recursos disponibles. Para comprender mejor cómo funciona el proceso, veamos un ejemplo de un nodo con dos núcleos de CPU (el mecanismo para el gobierno de memoria es equivalente):

1. En primer lugar, se coloca un contenedor en el nodo, que solicita un núcleo de CPU. El entorno de tiempo de ejecución abre el contenedor y establece el límite de CPU en un núcleo. El contenedor no podrá usar más de un núcleo.
2. Después, se coloca en el nodo una réplica del servicio y el correspondiente paquete de servicio especifica un límite de un núcleo de CPU. El entorno de tiempo de ejecución abre el paquete de código y establece el límite de CPU en un núcleo.

En este momento, la suma de los límites es igual a la capacidad del nodo, y se ejecutan un proceso y un contenedor con un núcleo cada uno sin interferir entre sí. Service Fabric no colocará más contenedores o réplicas cuando se haya especificando un límite de CPU. Sin embargo, existen dos situaciones en las que otros procesos pueden competir por la CPU, y un proceso y un contenedor de nuestro ejemplo pueden experimentar el problema de vecino ruidoso:

* Combinación de servicios con gobierno y sin gobierno y contenedores: si el usuario crea un servicio sin ningún gobierno de recursos especificado, el entorno de tiempo de ejecución considera que no estaba consumiendo ningún recurso y podrá colocarlo en el nodo de nuestro ejemplo. En este caso, este nuevo proceso consumirá eficazmente algún recurso de CPU a costa de los servicios que se ejecutan en el nodo. La solución a este problema es no combinar servicios con gobierno y sin gobierno en el mismo clúster, o bien usar [restricciones de posición](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que no finalice en el mismo conjunto de nodos.
* Cuando se inicia otro proceso en el nodo, fuera de Service Fabric (por ejemplo, en algún servicio de sistema operativo), ese proceso también competirá por la CPU con los servicios existentes. La solución a este problema consiste en configurar correctamente las capacidades del nodo en la cuenta para la sobrecarga del sistema operativo, tal como se muestra en la sección siguiente.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuración del clúster para habilitar la regulación de recursos

Cuando el nodo se inicia y se une al clúster, Service Fabric detecta la cantidad de memoria disponible y el número de núcleos disponibles, y establece las capacidades del nodo para esos dos recursos. Para dejar algún espacio en búfer al sistema operativo y para otros procesos que puedan ejecutarse en el nodo, Service Fabric solo utilizará el 80 % de los recursos disponibles en el nodo. Este porcentaje es configurable y puede cambiarse en el manifiesto de clúster. Este es un ejemplo que indica a Service Fabric cómo usar el 50 % de la CPU disponible y el 70 % de memoria disponible: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

En caso de que se necesite una configuración manual completa de las capacidades de nodo, también es posible con el mecanismo habitual para describir los nodos del clúster. Este es un ejemplo de cómo configurar el nodo con cuatro núcleos y 2 GB de memoria: 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Cuando está habilitada la detección automática de los recursos disponibles y las capacidades de nodo se han definido manualmente en el manifiesto de clúster, Service Fabric comprueba si el nodo tiene suficientes recursos para admitir la capacidad que ha definido el usuario:
* Si las capacidades de nodo que se definen en el manifiesto son menores o iguales que los recursos disponibles en el nodo, Service Fabric usa las capacidades que se especifican en el manifiesto.
* Si las capacidades de nodo que se definen en el manifiesto son mayores que los recursos disponibles, Service Fabric usa los recursos disponibles como capacidades de nodo.

La detección automática de los recursos disponibles puede desactivarse por completo, en caso de que no sea necesario, cambiando la configuración siguiente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Para obtener un rendimiento óptimo, también es necesario activar la siguiente configuración en el manifiesto de clúster: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>Definición de la regulación de recursos 

Los límites de la regulación de recursos se especifican en el manifiesto de aplicación (sección ServiceManifestImport) como se muestra en el ejemplo siguiente:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
En este ejemplo, el paquete de servicio ServicePackageA obtiene un núcleo de los nodos en que se encuentra. Este paquete de servicio contiene dos paquetes de código (CodeA1 y CodeA2), y ambos especifican el parámetro `CpuShares`. La proporción de CpuShares 512:256 divide el núcleo entre los dos paquetes de código. Por lo tanto, en este ejemplo, CodeA1 obtiene dos tercios de un núcleo y CodeA2 obtiene una tercera parte de un núcleo (y una reserva de garantía flexible del mismo). En caso de que no se especifiquen las proporciones de CpuShares para los paquetes de código, Service Fabric divide los núcleos equitativamente entre ellos.

Los límites de memoria son absolutos, por lo que ambos paquetes de código están limitados a 1024 MB de memoria (con una reserva de garantía flexible de dicha capacidad). Los paquetes de código (contenedores o procesos) no pueden asignar más memoria de la que establece este límite; si se intenta, el resultado es una excepción de memoria insuficiente. Para que la aplicación del límite de recursos funcione, es necesario haber definido límites de memoria en todos los paquetes de código de un paquete de servicio.

## <a name="other-resources-for-containers"></a>Otros recursos para los contenedores
Además de la CPU y de la memoria, es posible especificar otros límites de recursos para los contenedores. Estos límites se especifican en el nivel del paquete de código y se aplican cuando se inicia el contenedor. A diferencia de con la CPU y la memoria, el Administrador de recursos del clúster no tiene en cuenta estos recursos y no realiza ninguna comprobación de capacidad ni de equilibrio de carga para ellos. 

* MemorySwapInMB: la cantidad de memoria de intercambio que puede usar un contenedor.
* MemoryReservationInMB: límite flexible para el gobierno de memoria que se aplica únicamente cuando se detecta contención de la memoria en el nodo.
* CpuPercent: porcentaje de CPU que puede usar el contenedor. Si se especifican los límites de CPU para el Service Pack, se omite este parámetro.
* MaximumIOps: E/S máximas que puede usar un contenedor (lectura y escritura).
* MaximumIOBytesps: E/S máxima (bytes por segundo) que puede usar un contenedor (lectura y escritura).
* BlockIOWeight: peso de E/S de bloque en relación con otros contenedores.

Estos recursos se pueden combinar con la CPU y la memoria. Este es un ejemplo de cómo se especifican recursos adicionales para los contenedores:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el Administrador de recursos de clúster, lea este [artículo](service-fabric-cluster-resource-manager-introduction.md).
* Para más información sobre el modelo de aplicación, los paquetes de servicio, los paquetes de código y cómo se les asignan las réplicas, lea este [artículo](service-fabric-application-model.md).

