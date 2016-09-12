<properties
   pageTitle="Cluster Resource Manager de Service Fabric: costo de movimiento | Microsoft Azure"
   description="Información general del costo de movimiento de los servicios de Service Fabric"
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

# Costo del movimiento del servicio para influir en las opciones de Cluster Resource Manager
Otro factor importante que se debe tener en cuenta al intentar determinar qué cambios realizar en un clúster y la valoración de una solución es el costo general de conseguir esa solución.

Mover instancias o réplicas de un servicio supone unos costos mínimos de tiempo de CPU y ancho de banda de red. Para los servicios con estado, también está el costo de la cantidad de espacio en el disco necesario para crear una copia del estado antes de cerrar las réplicas antiguas. Es obvio que querrá reducir el costo de cualquier solución que se elabore con Cluster Resource Manager de Azure Service Fabric. Pero tampoco quiere pasar por alto soluciones que mejorarían significativamente la asignación de recursos en el clúster.

Cluster Resource Manager cuenta con dos formas de calcular los costos y limitarlos, incluso al tratar de administrar el clúster de acuerdo con sus otrose objetivos. La primera es que, cuando Cluster Resource Manager está planeando una nueva distribución del clúster, cuenta cada uno de los movimientos que tendría que hacer. En un ejemplo sencillo, si obtiene dos soluciones con prácticamente el mismo balance general (valoración), al final, elige la que tenga el costo más bajo (en número total de movimientos).

Esto funciona bastante bien. Pero, al igual que con cargas predeterminadas o estáticas, es poco probable que en cualquier sistema complejo todos los movimientos sean iguales. Probablemente algunos sean mucho más costosos.

## Cambio del costo de movimiento de una réplica y factores a tener en cuenta
Igual que cuando se informa de la carga (otra característica de Cluster Resource Manager), dotamos al servicio de un método de autoinforme de lo costoso que es mover el servicio en cualquier momento dado.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

El valor MoveCost tiene cuatro niveles: Zero (Cero), Low (Bajo), Medium (Medio) y High (Alto). Estos son relativos entre sí, a excepción de Zero (Cero). Zero (Cero) significa que mover una réplica es gratuito y que no se debe tener en cuenta en la valoración de la solución. Establecer su costo de movimiento con el valor High (Alto) *no* garantiza que la réplica no se mueva; solo garantiza que no se moverá si no hay una buena razón para ello.

![Costo de movimiento como un factor en la selección de réplicas para el movimiento][Image1]

MoveCost ayuda a encontrar las soluciones que provocan la menor interrupción posible y que son más sencillas de conseguir a la vez que se llega a un equilibrio equivalente. La noción de costo de un servicio puede ser relativa a muchas cosas. Los factores más comunes para calcular el costo de movimiento son:

- La cantidad de estados o de datos que el servicio tiene que mover.
- El costo de desconexión de los clientes. El costo de mover una réplica principal es normalmente mayor que el costo de mover una réplica secundaria.
- El costo de interrumpir una operación en curso. Algunas operaciones en el nivel de almacén de datos o las operaciones realizadas en respuesta a una llamada del cliente son costosas. Llegadas a un cierto punto, no quiere detenerlas si no es necesario. Así que durante la operación, aumenta el costo para reducir la probabilidad de que la réplica o la instancia del servicio se muevan. Luego, cuando finaliza la operación, lo devuelve a la normalidad.

## Pasos siguientes
- Cluster Resource Manger de Service Fabric usa métricas para administrar el consumo y la capacidad en el clúster. Para más información sobre las métricas y cómo configurarlas, consulte [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte [Equilibrio del clúster de Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0831_2016-->