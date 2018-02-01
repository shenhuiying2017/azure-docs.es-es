---
title: Gobierno de recursos de Azure Service Fabric para contenedores y servicios | Microsoft Docs
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
ms.openlocfilehash: 974fb5bfa8b10cb5497220825b2a83ca96161b0c
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="resource-governance"></a>Regulador de recursos 

Cuando se ejecutan varios servicios en el mismo clúster o nodo, es posible que un servicio pueda consumir más recursos y privar así a otros servicios en el proceso. A este problema se le conoce como el problema del entorno ruidoso. Azure Service Fabric permite al desarrollador establecer reservas y límites por servicio, a fin de garantizar la disponibilidad de recursos y, además, limita el uso de recursos.

> Antes de continuar con este artículo, le recomendamos que se familiarice con el [modelo de aplicación de Service Fabric](service-fabric-application-model.md) y con el [modelo de hospedaje de Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Métricas de regulación de recursos 

Service Fabric admite el gobierno de recursos de acuerdo con el [paquete de servicio](service-fabric-application-model.md). Los recursos asignados al paquete de servicio pueden dividirse además entre paquetes de código. Los límites de recursos especificados también suponen la reserva de los recursos. Service Fabric admite la determinación de CPU y memoria por cada paquete de servicio, con dos [métricas](service-fabric-cluster-resource-manager-metrics.md) integradas:

* *CPU* (nombre de la métrica `servicefabric:/_CpuCores`): un núcleo lógico que está disponible en la máquina host. Todos los núcleos de los nodos se ponderan igual.

* *Memoria* (nombre de métrica `servicefabric:/_MemoryInMB`): la memoria se expresa en megabytes y se asigna a la memoria física que está disponible en la máquina.

Para estas dos métricas, [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) realiza un seguimiento de la capacidad total del clúster, la carga de cada nodo del clúster y los recursos restantes del clúster. Estas dos métricas son equivalentes a cualquier otra métrica personalizada o de usuario. Todas las características existentes pueden utilizarse con ellas:
* El clúster puede [equilibrarse](service-fabric-cluster-resource-manager-balancing.md) en función de estas dos métricas (comportamiento predeterminado).
* El clúster puede [desfragmentarse](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en función de estas dos métricas.
* Para [describir un clúster](service-fabric-cluster-resource-manager-cluster-description.md), se puede establecer la capacidad de almacenamiento en búfer para estas dos métricas.

El [informe de carga dinámica](service-fabric-cluster-resource-manager-metrics.md) no es compatible con estas métricas, y las cargas para estas métricas se definen en el momento de la creación.

## <a name="resource-governance-mechanism"></a>Mecanismo de gobierno de recursos

El entorno de tiempo de ejecución de Service Fabric no proporciona actualmente reserva de recursos. Cuando se abre un proceso o un contenedor, el entorno de tiempo de ejecución establece los límites de recursos en las cargas definidas en tiempo de creación. Además, el entorno de tiempo de ejecución no admite la apertura de nuevos paquetes de servicio disponibles cuando se superan los recursos. Para comprender mejor cómo funciona el proceso, veamos un ejemplo de un nodo con dos núcleos de CPU (el mecanismo para el gobierno de memoria es equivalente):

1. En primer lugar, se coloca un contenedor en el nodo, que solicita un núcleo de CPU. El tiempo de ejecución abre el contenedor y establece el límite de CPU en un núcleo. El contenedor no podrá usar más de un núcleo.

2. Después, se coloca en el nodo una réplica del servicio y el correspondiente paquete de servicio especifica un límite de un núcleo de CPU. El tiempo de ejecución abre el paquete de código y establece el límite de CPU en un núcleo.

En este momento, la suma de los límites es igual a la capacidad del nodo. Un proceso y un contenedor se ejecutan con un núcleo cada uno y sin interferir entre sí. Service Fabric no coloca más contenedores o réplicas cuando se haya especificado un límite de CPU.

Sin embargo, hay dos situaciones en las que otros procesos pueden competir por la CPU. En estas situaciones, un proceso y un contenedor del ejemplo pueden experimentar el problema del entorno ruidoso:

* *Combinación de servicios con gobierno y sin gobierno y contenedores*: si el usuario crea un servicio sin ningún gobierno de recursos especificado, el entorno de tiempo de ejecución considera que no estaba consumiendo ningún recurso y puede colocarlo en el nodo de nuestro ejemplo. En este caso, este nuevo proceso consume eficazmente algún recurso de CPU a costa de los servicios que se ejecutan en el nodo. Hay dos soluciones al problema. Una solución consiste en no combinar servicios con gobierno y sin gobierno en el mismo clúster, y la otra en usar [restricciones de posición](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) para que estos dos tipos de servicio no finalicen en el mismo conjunto de nodos.

* *Cuando se inicia otro proceso en el nodo, fuera de Service Fabric (por ejemplo, en algún servicio de sistema operativo)*: en esta situación, el proceso fuera de Service Fabric también competirá por la CPU con los servicios existentes. La solución a este problema consiste en configurar correctamente las capacidades del nodo en la cuenta para la sobrecarga del sistema operativo, tal como se muestra en la sección siguiente.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configuración del clúster para habilitar el gobierno de recursos

Cuando el nodo se inicia y se une al clúster, Service Fabric detecta la cantidad de memoria disponible y el número de núcleos disponibles, y establece las capacidades del nodo para esos dos recursos. 

Para dejar algún espacio en búfer al sistema operativo y para otros procesos que puedan ejecutarse en el nodo, Service Fabric solo utiliza el 80 % de los recursos disponibles en el nodo. Este porcentaje es configurable y puede cambiarse en el manifiesto de clúster. 

Este es un ejemplo que indica a Service Fabric cómo usar el 50 % de la CPU disponible y el 70 % de memoria disponible: 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Si se necesita una configuración manual completa de las capacidades de nodo, se puede usar el mecanismo habitual para describir los nodos del clúster. Este es un ejemplo de cómo configurar el nodo con cuatro núcleos y 2 GB de memoria: 

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

Puede desactivar la detección automática de los recursos disponibles si no es necesario. Para desactivar esta función, cambie la configuración siguiente:

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


## <a name="specify-resource-governance"></a>Especificación del gobierno de recursos 

Los límites de la regulación de recursos se especifican en el manifiesto de aplicación (sección ServiceManifestImport) como se muestra en el ejemplo siguiente:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
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
  
En este ejemplo, el paquete de servicio denominado **ServicePackageA** obtiene un núcleo de los nodos en que se encuentra. Este paquete de servicio contiene dos paquetes de código (**CodeA1** y **CodeA2**), y ambos especifican el parámetro `CpuShares`. La proporción de CpuShares 512:256 divide el núcleo entre los dos paquetes de código. 

Por lo tanto, en este ejemplo, CodeA1 obtiene dos tercios de un núcleo y CodeA2 obtiene una tercera parte de un núcleo (y una reserva de garantía flexible del mismo). Si no se especifican las proporciones de CpuShares para los paquetes de código, Service Fabric divide los núcleos equitativamente entre ellos.

Los límites de memoria son absolutos, por lo que ambos paquetes de código están limitados a 1024 MB de memoria (con una reserva de garantía flexible de dicha capacidad). Los paquetes de código (contenedores o procesos) no pueden asignar más memoria de la que establece este límite; si se intenta, el resultado es una excepción de memoria insuficiente. Para que la aplicación del límite de recursos funcione, es necesario haber definido límites de memoria en todos los paquetes de código de un paquete de servicio.

### <a name="using-application-parameters"></a>Uso de los parámetros de la aplicación

Al especificar la regulación de recursos es posible utilizar [parámetros de la aplicación](service-fabric-manage-multiple-environment-app-configuration.md) para administrar varias configuraciones de la aplicación. En el ejemplo siguiente se muestra el uso de los parámetros de la aplicación:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

En este ejemplo, se establecen los valores de los parámetros predeterminados para el entorno de producción, donde cada paquete de servicio obtendría 4 núcleos y 2 GB de memoria. Es posible cambiar los valores predeterminados con los archivos de parámetros de la aplicación. En este ejemplo se puede utilizar un archivo de parámetros para probar la aplicación localmente, donde obtendría menos recursos que en producción: 

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT] 
> La especificación de la regulación de recursos con parámetros de la aplicación está disponible a partir de Service Fabric versión 6.1.<br> 
>
> Cuando se usan parámetros de la aplicación para especificar la regulación de recursos, Service Fabric no se puede degradar a una versión anterior a la versión 6.1. 


## <a name="other-resources-for-containers"></a>Otros recursos para los contenedores
Además de la CPU y de la memoria, es posible especificar otros límites de recursos para los contenedores. Estos límites se especifican en el nivel del paquete de código y se aplican cuando se inicia el contenedor. A diferencia de con la CPU y la memoria, Cluster Resource Manager no tiene en cuenta estos recursos y no realiza ninguna comprobación de capacidad ni de equilibrio de carga para ellos. 

* *MemorySwapInMB*: la cantidad de memoria de intercambio que puede usar un contenedor.
* *MemoryReservationInMB*: límite flexible para el gobierno de memoria que se aplica únicamente cuando se detecta contención de la memoria en el nodo.
* *CpuPercent*: porcentaje de CPU que puede usar el contenedor. Si se especifican los límites de CPU para el Service Pack, se omite este parámetro.
* *MaximumIOps*: E/S máximas que puede usar un contenedor (lectura y escritura).
* *MaximumIOBytesps*: E/S máxima (bytes por segundo) que puede usar un contenedor (lectura y escritura).
* *BlockIOWeight*: peso de E/S de bloque en relación con otros contenedores.

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

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre Cluster Resource Manager, consulte [Presentación de Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Para más información sobre el modelo de aplicación, paquetes de servicio, paquetes de código y cómo las réplicas los asignan, lea [Modelar una aplicación en Service Fabric](service-fabric-application-model.md).
