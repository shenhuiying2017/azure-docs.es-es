---
title: "Cluster Resource Manager de Service Fabric: directivas de colocación | Microsoft Docs"
description: "Información general sobre las reglas y directivas de colocación adicionales para los servicios de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Directivas de colocación de servicios de Service Fabric
Hay muchas reglas adicionales distintas que puede tener que configurar en ciertos escenarios poco frecuentes. Algunos ejemplos de esos escenarios son:
* Si el clúster de Service Fabric está distribuido geográficamente, como en varios centros de datos locales o en regiones de Azure.
* Si su entorno abarca diversas áreas de control geopolítico (o cualquier otro caso en el que haya limites legales o de directiva que le afecten).
* Existen consideraciones reales en el rendimiento o latencia debido a la comunicación en el clúster que viaja grandes distancias o pasa por ciertas redes más lentas o menos confiables.

En estos tipos de situaciones, puede ser importante que un servicio determinado se ejecute siempre o que nunca se ejecute en ciertas regiones. Del mismo modo, puede ser importante intentar colocar el servicio principal en cierta región con el fin de minimizar la latencia del usuario final.

Las directivas de colocación avanzadas son:

1. Dominios no válidos
2. Dominios requeridos
3. Dominios de preferencia
4. No permitir el empaquetado de réplicas

La mayoría de los controles siguientes pueden configurarse mediante propiedades de nodo y restricciones de colocación, pero otros son más complicados. Para simplificar las cosas, Cluster Resource Manager de Service Fabric proporciona estas directivas de colocación adicionales. Al igual que sucede con otras restricciones de colocación, las directivas de colocación pueden configurarse en función de la instancia de servicio con nombre y actualizarse de forma dinámica.

## <a name="specifying-invalid-domains"></a>Especificación de dominios no válidos
La directiva de colocación InvalidDomain le permite especificar que un determinado dominio de error no es válido para esta carga de trabajo. Resulta útil para garantizar que un servicio específico no se ejecute nunca en un área determinada, por ejemplo por motivos geopolíticos o relacionados con la directiva corporativa. Pueden especificarse varios dominios no válidos mediante distintas directivas.

<center>
![Ejemplo de dominio no válido][Image1]
</center>

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Especificación de dominios requeridos
La directiva de colocación de dominios requeridos exige que todas las réplicas con estado o instancias de servicio sin estado del servicio existan en el dominio especificado. Pueden especificarse varios dominios requeridos mediante directivas diferentes.

<center>
![Ejemplo de dominio requerido][Image2]
</center>

Código:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Especificación de un dominio preferido para las réplicas principales
El dominio principal preferido es un control interesante, ya que permite seleccionar el dominio de error en el que debe existir el dominio principal, si es posible hacerlo. El dominio principal acaba en este dominio cuando todo está correcto. Si se produce un error en el dominio o en la réplica principal, o bien se apagan por algún motivo, el dominio principal se migrará a otra ubicación. Si esta ubicación nueva no está en el dominio preferido, Cluster Resource Manager la devolverá al dominio preferido lo antes posible. Evidentemente, esta configuración solo tiene sentido para los servicios con estado. Esta directiva resulta útil principalmente en clústeres que están distribuidos entre regiones de Azure o entre varios centros de datos, pero se preferiría que las réplicas principales estén colocadas en una ubicación determinada. Mantener los dominios principales cerca de los usuarios permite brindar una menor latencia, especialmente en el caso de las lecturas.

<center>
![Dominios principales preferidos y conmutación por error][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Exigir que las réplicas se distribuyan entre todos los dominios y no permitir el empaquetado
Las réplicas _habitualmente_ se distribuyen entre los dominios en que el clúster está en buen estado; sin embargo, hay casos en los que las réplicas de una partición determinada pueden terminar temporalmente empaquetados en un solo dominio. Por ejemplo, digamos que el clúster tiene nueve nodos en tres dominios de error (fd:/0, fd:/1 y fd:/2) y el servicio tiene tres réplicas. Supongamos que los nodos que se usaban para esas réplicas en fd:/1 y fd:/2 quedaron inactivos. Ahora, lo habitual es que Cluster Resource Manager prefiera otros nodos en esos mismos dominios de error. En este caso, supongamos que, debido a problemas de capacidad, ninguno de los demás nodos de esos dominios eran válidos. Si Cluster Resource Manager crea reemplazos para esas réplicas, debería tener que escoger los nodos de fd:/0. Sin embargo, con _esa acción_ se crea una situación en la que se infringe la restricción de dominio de error. También aumenta la probabilidad de que todo el conjunto de réplicas quede inactivo o se pierda (si FD 0 se pierde definitivamente). Para más información sobre las restricciones y las prioridades de restricción en general, consulte [este tema](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Si alguna vez vio una advertencia de estado como `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`, es porque se topó con esta condición o algo parecido. Esta situación es poco frecuente, pero puede suceder y, usualmente, estas situaciones son transitorias porque los nodos vuelven a estar activos. Si los nodos permanecen inactivos y Cluster Resource Manager debe crear reemplazos, habitualmente hay otros nodos disponibles en los dominios de error ideales.

Algunas cargas de trabajo preferirían tener siempre el número de réplicas de destino, incluso si se empaquetan en menos dominios. Estas cargas de trabajo apuesta contra el número total de errores permanentes del dominio simultáneos y normalmente pueden recuperar el estado local. Otras cargas de trabajo preferirían asumir el tiempo de inactividad antes que arriesgar la exactitud o la pérdida de los datos. Como la mayoría de las cargas de trabajo de producción se ejecutan con más de tres réplicas, más de tres dominios de error y muchos nodos válidos por dominio de error, el valor predeterminado es no requerir la distribución de dominio. Esto permite que el equilibrio y la conmutación por error normal controlen estos casos, incluso si eso significa que un dominio pueda tener temporalmente varias réplicas empaquetadas en él.

Si desea deshabilitar ese empaquetado para una carga de trabajo determinada, puede especificar la directiva "RequireDomainDistribution" en el servicio. Cuando se establece esta directiva, Cluster Resource Manager garantiza que nunca se permitirán dos réplicas de la misma partición en el mismo dominio de error o de actualización.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Se preguntará si sería posible usar estas configuraciones para los servicios de un clúster que no esté distribuido geográficamente. Por supuesto que se puede. Sin embargo, no hay razones de peso para hacerlo. Las configuraciones de dominio requerido, no valido y preferido se deben evitar, a menos que realmente se ejecute un clúster distribuido geográficamente. No tiene sentido intentar forzar que una carga de trabajo determinada se ejecute en un solo bastidor o dar preferencia a algún segmento del clúster local sobre otro. Distintas configuraciones de hardware se deben distribuir entre dominios, y entre los administrados a través de restricciones de ubicación normales y propiedades de nodo.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las demás opciones disponibles para configurar servicios, vaya a [Información acerca de la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


