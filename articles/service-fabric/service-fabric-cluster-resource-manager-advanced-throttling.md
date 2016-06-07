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
   ms.date="05/20/2016"
   ms.author="masnider"/>


# Limitación del comportamiento del Administrador de recursos de clúster de Service Fabric
A veces, aunque se haya configurado correctamente el Administrador de recursos, se pueden producir interrupciones en el clúster (muchos errores de nodos o dominios de error, problemas mientras se están aplicando las actualizaciones, etc.). Resource Manager hará todo lo posible por corregir estos problemas, pero en estos casos podría plantearse un mecanismo de respaldo para que el clúster pueda estabilizarse (los nodos que vayan a regresar, regresen, las condiciones de la red se solucionen por sí mismas y se implementen los bits corregidos). Para ayudarle con este tipo de situaciones, Resource Manager incluye varias limitaciones. Tenga en cuenta que se trata de medidas bastante disruptivas a las que no se debe recurrir a menos que se hayan realizado cálculos detallados con respecto a la cantidad de trabajo paralelo que se puede realizar en el clúster y que exista una necesidad frecuente de abordar estos tipos de eventos de reconfiguración macroscópicos no planeados.

Por lo general, recomendamos evitar estos eventos mediante otras opciones, como actualizar el código periódicamente y evitar programar demasiadas tareas en el clúster, en lugar de limitarlo para impedir que utilice recursos cuando está intentando corregirse a sí mismo. Dicho esto, es posible que haya casos en los que deba aplicar varias limitaciones (hasta que pueda solucionarlos), incluso si esto implica que el clúster tardará más tiempo en estabilizarse.

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

Tenga en cuenta que la mayoría de las veces que hemos visto que los clientes usan estas limitaciones es porque ya estaban en un entorno restringido de recursos (como ancho de banda de red limitado en nodos individuales), lo que significaba que tales operaciones no tendrían éxito o serían lentas de todos modos y los clientes se sentían mejor sabiendo que estaban aumentando potencialmente la cantidad de tiempo que tardaría el clúster en alcanzar un estado estable, sabiendo también que podían acabar ejecutándose con una confiabilidad general baja con esas limitaciones.

## Pasos siguientes
- Para más información sobre cómo el Administrador de recursos de clúster administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

<!---HONumber=AcomDC_0525_2016-->