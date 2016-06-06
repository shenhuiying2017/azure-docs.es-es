<properties
   pageTitle="Administrador de recursos de clúster de Service Fabric: directivas de colocación | Microsoft Azure"
   description="Información general sobre las reglas y directivas de colocación adicionales para los servicios de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>

# Directivas de colocación de servicios de Service Fabric
Existen numerosas reglas adicionales que podrían afectarle si el clúster de Service Fabric está distribuido geográficamente, como en varios centros de datos o regiones de Azure, o si su entorno abarca diversas áreas de control geopolítico (o cualquier otro caso en el que haya límites legales o de directiva que le afecten, o en el que las distancias implicadas tengan un impacto real en el rendimiento o la latencia). La mayoría pueden configurarse mediante propiedades de nodo y restricciones de colocación, pero otras son más complicadas. En cualquier caso, proporcionamos estos accesos directos. Al igual que las restricciones de colocación, las directivas de colocación pueden configurarse en función de la instancia de servicio con nombre.

## Especificación de dominios no válidos
La directiva de colocación InvalidDomain le permite especificar que un determinado dominio de error no es válido para esta carga de trabajo. Resulta útil para garantizar que un servicio específico no se ejecute nunca en un área determinada, por ejemplo por motivos geopolíticos o relacionados con la directiva corporativa. Pueden especificarse varios dominios no válidos.

![Ejemplo de dominio no válido][Image1]

Código:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Especificación de dominios requeridos
La directiva de colocación de dominio requerido exige que todas las réplicas estén presentes en el dominio especificado. Pueden especificarse varios dominios requeridos.

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

## Especificación de un dominio preferido para las réplicas principales
El dominio principal preferido es un control interesante, ya que permite seleccionar el dominio de error en el que debe existir el dominio principal, si es posible hacerlo. Cuando todo está correcto, el dominio principal acabará en este dominio. Si se produce un error en el dominio o la réplica principal, o bien se apagan por algún motivo, el dominio principal se migrará a otra ubicación. Cuando sea posible, volverá al dominio preferido. Evidentemente, esta configuración solo tiene sentido para los servicios con estado. Esta directiva resulta muy útil en clústeres geográficamente distribuidos que utilizan otras ubicaciones para la redundancia, pero sería preferible que las réplicas principales se colocaran en una ubicación determinada a fin de proporcionar una menor latencia para las operaciones que van a la réplica principal (las escrituras y también, de forma predeterminada, todas las lecturas son atendidas por la principal).

![Dominios principales preferidos y conmutación por error][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Exigir que las réplicas se distribuyan entre todos los dominios y no permitir el empaquetado
Otra directiva que puede especificar es exigir que las réplicas se distribuyan siempre entre los dominios disponibles. Observe que, en el ejemplo anterior, es posible temporalmente que varias réplicas (de hecho, un cuórum) se empaqueten en el mismo centro de datos. En este caso, probablemente no haya ningún problema porque la réplica principal no se encuentra en el mismo controlador de dominio que la mayoría de las réplicas, pero lo que importa es que indica que podríamos estar sujetos temporalmente a una configuración que nos interesa evitar. Esto depende de otras condiciones del clúster en ese momento, pero aun así podría interesarle evitarlo por completo asegurándose de que las réplicas siempre están en dominios distintos. Tenga en cuenta que si selecciona esta opción, está eligiendo tiempo de inactividad o errores en lugar de permitir que el servicio continúe ejecutándose en los nodos restantes. La directiva RequireDomainDistribution configura este comportamiento, pero tenga en cuenta que esto puede significar que no colocaremos ninguna réplica en absoluto si el resultado es que se van a empaquetar. Esto significa que, temporalmente, se ejecuta un número de réplicas menor que el objetivo hasta que vuelva a haber nodos en un dominio viable. Algunos clientes prefieren tener en todo momento el número de réplicas objetivo (copias de estado), mientras que otros no quieren asumir el riesgo de tener problemas de disponibilidad, ya que si se empaquetan las réplicas en un dominio de error y se produce un error en dicho dominio, se perderán todas las réplicas al mismo tiempo. En el caso de un número reducido de réplicas, esto podría causar la pérdida de cuórum. En el improbable caso en que el dominio nunca volviera, esto podría provocar la pérdida de datos. Como la mayoría de las personas ejecutan más de 3 réplicas, el valor predeterminado es no exigir la distribución de dominios y permitir que el equilibrio y la conmutación por error controlen los casos con normalidad, aunque eso signifique que temporalmente se empaquetarán varias réplicas en un dominio.

Código:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Se preguntará si sería posible usar estas configuraciones para los servicios de un clúster que no esté distribuido geográficamente. Por supuesto que se puede. Sin embargo, no hay razones de peso para hacerlo. Además, deben evitarse las configuraciones de dominio requerido, no valido y preferido, a menos que se ejecute un clúster distribuido geográficamente.

## Pasos siguientes
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones del Administrador de recursos de clúster disponibles. [Más información sobre cómo configurar los servicios](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0525_2016-->