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
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cc136c567279ff9da655ed3536fae6d2ae7be3df


---
# <a name="placement-policies-for-service-fabric-services"></a>Directivas de colocación de servicios de Service Fabric
Existen numerosas reglas adicionales que podrían afectarle si el clúster de Service Fabric está distribuido entre varias distancias geográficas, por ejemplo, entre varios centros de datos o regiones de Azure, o si su entorno abarca diversas áreas de control geopolítico (o cualquier otro caso en el que haya límites legales o de directiva que le afecten, o en el que las distancias implicadas tengan un impacto real en el rendimiento o la latencia). La mayoría pueden configurarse mediante propiedades de nodo y restricciones de colocación, pero otras son más complicadas. Para simplificar las cosas, proporcionamos estos comandos adicionales. Al igual que sucede con otras restricciones de ubicación, las directivas de selección de ubicación pueden configurarse en función de la instancia de servicio con nombre.

## <a name="specifying-invalid-domains"></a>Especificación de dominios no válidos
La directiva de colocación InvalidDomain le permite especificar que un determinado dominio de error no es válido para esta carga de trabajo. Resulta útil para garantizar que un servicio específico no se ejecute nunca en un área determinada, por ejemplo por motivos geopolíticos o relacionados con la directiva corporativa. Pueden especificarse varios dominios no válidos mediante distintas directivas.

![Ejemplo de dominio no válido][Image1]

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
La directiva de selección de ubicación de dominios requeridos exige que todas las réplicas con estado o instancias de servicio sin estado del servicio existan en el dominio especificado. Pueden especificarse varios dominios requeridos mediante directivas diferentes.

![Ejemplo de dominio requerido][Image2]

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
El dominio principal preferido es un control interesante, ya que permite seleccionar el dominio de error en el que debe existir el dominio principal, si es posible hacerlo. Cuando todo está correcto, el dominio principal acabará en este dominio. Si se produce un error en el dominio o la réplica principal, o bien se apagan por algún motivo, el dominio principal se migrará a otra ubicación. Si esta ubicación no está en el dominio preferido, Cluster Resource Manager la devolverá al dominio preferido, siempre que sea posible. Evidentemente, esta configuración solo tiene sentido para los servicios con estado. Esta directiva resulta útil principalmente en clústeres que están distribuidos entre regiones de Azure o entre varios centros de datos. En estas situaciones usará todas las ubicaciones de cara a la redundancia, pero preferirá que las réplicas principales se coloquen en una ubicación determinada a fin de proporcionar una menor latencia en operaciones que se dirigen a las principales (las escrituras y, de forma predeterminada, todas las lecturas se atienden en las principales).

![Dominios principales preferidos y conmutación por error][Image3]

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
Otra directiva que puede especificar es exigir que las réplicas se distribuyan siempre entre los dominios de error disponibles. En la mayoría de los casos en los que el clúster tenga un estado correcto, esto sucederá de forma predeterminada; sin embargo, hay casos degenerados en los que las réplicas de una partición dada pueden acabar empaquetadas temporalmente en un único dominio de error o actualización. Por ejemplo, aunque el clúster tuviera 9 nodos en 3 dominios de error (0, 1 y 2) y el servicio tuviera 3 réplicas, los nodos que se usaran para esas réplicas en los dominios de error 1 y 2 se volverían inactivos y, debido a los problemas de capacidad, ninguno de los otros nodos de esos dominios sería válido. Si Service Fabric crea sustituciones para esas réplicas, Cluster Resource Manager tendrá que ponerlas en el dominio de error 0, lo que crea una situación en la que se infringe la restricción del dominio de error. También aumenta la probabilidad de que todo el conjunto de réplicas se pierda (si el dominio de error 0 se pierde definitivamente). (Para más información sobre las restricciones y las prioridades de restricción en general, consulte [en este tema](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) ).

Si alguna vez ha visto una advertencia de estado como "El equilibrador de carga detectó una infracción de limitación para esta réplica:fabric:/<some service name> La partición secundaria <some partition ID> está infringiendo la restricción: FaultDomain", se ha topado con esta condición o algo parecido. Estas situaciones suelen ser transitorias (los nodos no permanecen inactivos mucho tiempo o, si lo hacen, es necesario crear sustituciones donde haya otros nodos en los dominios de error correctos que sean válidos), pero hay algunas cargas de trabajo en las que preferirá disponibilidad antes que arriesgarse a perder todas sus réplicas. Para ello, podemos especificar la directiva "RequireDomainDistribution"que garantizará que dos réplicas cualesquiera de la misma partición no podrán estar jamás en el mismo dominio de error o actualización.

Algunas cargas de trabajo preferirán tener siempre el número objetivo de réplicas (copias de estado) en todo momento (apostando contra el número total de errores del dominio y sabiendo que habitualmente pueden recuperar el estado local), mientras que otras preferirán tener tiempo de inactividad antes que arriesgarse a problemas de exactitud y pérdida de datos. Como la mayoría de las cargas de trabajo de producción se ejecutan con más de 3 réplicas, el valor predeterminado es no exigir la distribución de dominios y permitir que el equilibrio y la conmutación por error controlen los casos con normalidad, aunque eso signifique que temporalmente se empaquetarán varias réplicas en un dominio.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Se preguntará si sería posible usar estas configuraciones para los servicios de un clúster que no esté distribuido geográficamente. Por supuesto que se puede. Pero tampoco hay una buena razón para ello; en concreto, se deben evitar configuraciones de dominios preferidos requeridos y no válidos a no ser que realmente vaya a ejecutar un clúster distribuido geográficamente - no tiene sentido intentar forzar que una carga de trabajo determinada se ejecute en un solo bastidor, o dar preferencia a algún segmento del clúster local sobre otro a menos que haya diferentes tipos de hardware o que se produzca la segmentación de la carga de trabajo, y esos casos se pueden administrar mediante restricciones de ubicación normales.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)

[Imagen 1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Imagen 2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Imagen 3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Nov16_HO3-->


