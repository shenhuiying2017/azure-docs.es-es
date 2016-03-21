<properties
   pageTitle="Presentación de Service Fabric Cluster Resource Manager"
   description="Una introducción a Service Fabric Cluster Resource Manager."
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Limitaciones
A veces, aunque se haya configurado correctamente Resource Manager, se pueden producir interrupciones en el clúster (muchos errores de nodos o dominios de error, problemas mientras se están aplicando o ejecutando las actualizaciones, etc.). Resource Manager hará todo lo posible por corregir estos problemas, pero en estos casos podría plantearse un mecanismo de respaldo para que el clúster pueda estabilizarse (los nodos que vayan a regresar, regresen, las condiciones de la red se solucionen por sí mismas y se implementen los bits corregidos). Para ayudarle con este tipo de situaciones, Resource Manager incluye varias limitaciones. Tenga en cuenta que se trata de medidas bastante disruptivas a las que no se debe recurrir a menos que se hayan realizado cálculos detallados con respecto a la cantidad de trabajo paralelo que se puede realizar en el clúster y que exista una necesidad frecuente de abordar estos tipos de eventos de reconfiguración macroscópicos no planeados.

Por lo general, recomendamos evitar estos eventos mediante otras opciones, como actualizar el código periódicamente y evitar programar demasiadas tareas en el clúster, en lugar de limitarlo para impedir que utilice recursos cuando está intentando corregirse a sí mismo. Dicho esto, es posible que haya casos en los que deba aplicar varias limitaciones (hasta que pueda solucionarlos), incluso si esto implica que el clúster tardará más tiempo en estabilizarse.

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

Tenga en cuenta que, la mayoría de las veces, los clientes han utilizado estas limitaciones porque
-	ya se encontraban en un entorno de recursos restringido (como un ancho de banda de red limitado en nodos individuales), por lo que dichas operaciones no se realizarían correctamente o se ejecutarían con lentitud de todos modos.
-	Además, los clientes asumían que era posible que estuvieran ampliando el tiempo necesario para que el clúster se estabilizara y que incluso se podrían ejecutar las tareas con una confiabilidad general inferior mientras se estuvieran aplicando las limitaciones.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Obtenga información sobre cómo Cluster Resource Manager equilibra la carga en el clúster](service-fabric-cluster-resource-manager-balancing.md).

<!---HONumber=AcomDC_0309_2016-->