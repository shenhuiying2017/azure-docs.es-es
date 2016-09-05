<properties
   pageTitle="Limitación de Cluster Resource Manager de Service Fabric | Microsoft Azure"
   description="Aprenda a configurar las limitaciones proporcionadas por Cluster Resource Manager de Service Fabric."
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
   ms.date="08/19/2016"
   ms.author="masnider"/>


# Limitación del comportamiento de Cluster Resource Manager de Service Fabric
Aunque haya configurado correctamente Cluster Resource Manager, el clúster puede ver alterado su comportamiento. Por ejemplo, podría haber errores de nodos simultáneos o de dominios de error. ¿Y qué pasaría si sucediera esto durante una actualización? Resource Manager hará todo lo posible por corregir estos problemas, pero en estos casos podría plantearse un mecanismo de respaldo para que el clúster pueda estabilizarse (los nodos que vayan a regresar, regresen, las condiciones de la red se solucionen por sí mismas y se implementen los bits corregidos). Para ayudarle con este tipo de situaciones, Cluster Resource Manager de Service Fabric incluye varias limitaciones. Tenga en cuenta que estas limitaciones son bastante disruptivas y, por lo general, no se debe recurrir a ellas a menos que se hayan realizado cálculos detallados con respecto a la cantidad de trabajo paralelo que se puede realizar en el clúster y que exista una necesidad frecuente de abordar estos tipos de eventos de reconfiguración macroscópicos no planeados.

Por lo general, recomendamos evitar estos eventos mediante otras opciones, como actualizar el código periódicamente y evitar programar demasiadas tareas en el clúster, en lugar de limitarlo para impedir que utilice recursos cuando está intentando corregirse a sí mismo. Las limitaciones no tienen valores predeterminados que gracias nuestra experiencia hayamos encontrado que son adecuados, sino que lo más probable es que tenga que echar un vistazo y ajustarlos a la carga real esperada. Aunque una buena práctica puede ser no restringir o cargar el clúster en demasía, puede haber casos en los que (hasta que no los solucione) tenga que aplicar algunas limitaciones, aunque eso signifique que el clúster tarde más en estabilizarse.

##Configuración de las limitaciones
Las limitaciones que se incluyen de forma predeterminada son las siguientes:

-	GlobalMovementThrottleThreshold: controla el número total de movimientos en el clúster durante cierto tiempo (definido como el valor en segundos de GlobalMovementThrottleCountingInterval).
-	MovementPerPartitionThrottleThreshold: controla el número total de movimientos en cualquier partición del servicio durante cierto tiempo (el valor en segundos de MovementPerPartitionThrottleCountingInterval).

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Tenga en cuenta que la mayoría de las veces que vemos que los clientes usan estas limitaciones ha sido porque ya estaban en un entorno con restricción de recursos (por ejemplo, ancho de banda de red limitado en nodos individuales o discos que no cumplían los requisitos de las compilaciones de réplicas paralelas que debían cumplir), lo que significaba que tales operaciones no se realizarían o serían lentas de todos modos. En estas situaciones, los clientes asumían que era posible que estuvieran ampliando el tiempo necesario para que el clúster se estabilizara y que incluso se podrían ejecutar las tareas con una confiabilidad general inferior mientras se estuvieran aplicando las limitaciones.

## Pasos siguientes
- Para más información sobre cómo el Administrador de recursos de clúster administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

<!---HONumber=AcomDC_0824_2016-->