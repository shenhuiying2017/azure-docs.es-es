---
title: "Regulación de recursos de Azure Service Fabric para contenedores y servicios | Microsoft Docs"
description: "Azure Service Fabric permite especificar los límites de recursos de servicios que se ejecutan dentro o fuera de contenedores."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>Regulador de recursos 

Cuando se ejecutan varios servicios en el mismo clúster o nodo, es posible que un servicio pueda consumir más recursos y privar así a otros servicios. A este problema se le conoce como el problema del entorno ruidoso. Service Fabric permite al desarrollador establecer reservas y límites por servicio, a fin de garantizar la disponibilidad de recursos y, además, limita el uso de recursos. 

## <a name="resource-governance-metrics"></a>Métricas de regulación de recursos 

Service Fabric admite la regulación de recursos en función del [paquete de servicio](service-fabric-application-model.md). Los recursos asignados al paquete de servicio pueden dividirse además entre paquetes de código. Los límites de recursos especificados también suponen la reserva de los recursos. Service Fabric admite la determinación de CPU y memoria por cada paquete de servicio mediante la utilización de dos [métricas](service-fabric-cluster-resource-manager-metrics.md) integradas:

* CPU (nombre de métrica `ServiceFabric:/_CpuCores`): un núcleo es un núcleo lógico que se encuentra disponible en el equipo host, y todos los núcleos de todos los nodos se ponderan de la misma forma.
* Memoria (nombre de métrica `ServiceFabric:/_MemoryInMB`): la memoria se expresa en megabytes y se asigna a la memoria física que está disponible en el equipo.

Solo se ofrecen garantías de reserva flexible; el runtime rechaza la apertura de los nuevos paquetes de servicio en los que se superan los recursos disponibles. Sin embargo, si se coloca otro archivo ejecutable o contenedor en el nodo, se pueden infringir las garantías de reserva originales.

Para estas dos métricas, el [Administrador de recursos de clúster](service-fabric-cluster-resource-manager-cluster-description.md) realiza un seguimiento de la capacidad total del clúster, la carga de cada nodo del clúster y los recursos restantes del clúster. Estas dos métricas son equivalentes a cualquier otra métrica de usuario o personalizada, por lo que todas las características existentes se pueden usar con ellas:
* El clúster puede [equilibrarse](service-fabric-cluster-resource-manager-balancing.md) en función de estas dos métricas (comportamiento predeterminado).
* El clúster puede [desfragmentarse](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en función de estas dos métricas.
* Para [describir un clúster](service-fabric-cluster-resource-manager-cluster-description.md), se puede establecer la capacidad de almacenamiento en búfer para estas dos métricas.

El [informe de carga dinámica](service-fabric-cluster-resource-manager-metrics.md) no es compatible con estas métricas, y las cargas para estas métricas se definen en el momento de la creación.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuración del clúster para habilitar la regulación de recursos

La capacidad debe definirse manualmente en cada tipo de nodo del clúster como se indica a continuación:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
La regulación de recursos solo se admite en servicios de usuario y no en todos los servicios de sistema. Al especificar la capacidad, algunos núcleos y la memoria deben dejarse desasignados para los servicios de sistema. Para obtener un rendimiento óptimo, también es necesario activar la siguiente configuración en el manifiesto de clúster: 

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


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el Administrador de recursos de clúster, lea este [artículo](service-fabric-cluster-resource-manager-introduction.md).
* Para más información sobre el modelo de aplicación, los paquetes de servicio, los paquetes de código y cómo se les asignan las réplicas, lea este [artículo](service-fabric-application-model.md).

