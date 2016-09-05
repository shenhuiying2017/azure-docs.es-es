<properties
   pageTitle="Administrador de recursos de clúster de Service Fabric: costo del movimiento | Microsoft Azure"
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

# Costo del movimiento del servicio para influir en las opciones del Administrador de recursos
Otro factor importante que tenemos en cuenta al intentar determinar qué cambios realizar en un clúster y la valoración de una solución es el costo general de esa solución.

Como mínimo, desplazar las instancias de servicio o las réplicas cuesta un tiempo de CPU y un ancho de banda de red; además, a los servicios con estado se les debe sumar también la cantidad de espacio en disco que se necesite para crear una copia del estado antes de apagar las réplicas antiguas. Evidentemente, lo que busca es minimizar el costo de cualquier solución que aporte el Administrador de recursos de clúster, pero tampoco quiere hacer caso omiso las soluciones que puedan mejorar significativamente la asignación de recursos en el clúster.

El Administrador de recursos de clúster cuenta con dos formas de calcular los costos y limitarlos, incluso al tratar de administrar el clúster de acuerdo con sus demás objetivos. La primera es que, cuando el Administrador de recursos de clúster está planeando una nueva distribución del clúster, cuenta cada uno de los movimientos que tendría que hacer. En un ejemplo sencillo, si obtiene dos soluciones con aproximadamente el mismo balance general (valoración) al final, elige la que tenga el costo más bajo (en número total de movimientos).

Este procedimiento funciona bastante bien hasta que nos encontramos con el mismo problema que teníamos con las cargas estáticas o predeterminadas: es poco probable que en un sistema complejo todos los movimientos sean iguales; es posible que unos serán mucho más caros que otros.

## Cambio del costo de movimiento de una réplica y factores a tener en cuenta
Igual que cuando se informa de la carga (otra característica del Administrador de recursos de clúster), dotamos al servicio de un método de autoinforme de lo costoso que es mover el servicio en cualquier momento dado.

Código

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

El valor MoveCost tiene cuatro niveles: Zero (Cero), Low (Bajo), Medium (Medio) y High (Alto). De nuevo, están relacionados entre sí (excepto el valor Zero, que significa que el movimiento de una réplica es gratuito y no debería contarse en la valoración de la solución). Establecer su costo de movimiento con el valor High (Alto) *no* garantiza que la réplica no se mueva; solo garantiza que no se moverá si no hay una buena razón para ello.

![Costo de movimiento como un factor en la selección de réplicas para el movimiento][Image1]

MoveCost nos ayuda a encontrar las soluciones que conllevan la menor interrupción posible y las que resultan más sencillas de lograr a la vez que se logra un equilibrio equivalente. Una noción de costo de un servicio puede ser relativa a muchas cosas, pero los factores más comunes para calcular el costo de movimiento son:

1.	La cantidad de estados o de datos que el servicio tiene que mover.
2.	El costo de la desconexión de los clientes (el costo de mover una réplica principal será normalmente mayor que el de mover una réplica secundaria).
3.	El costo de interrumpir alguna operación en curso (algunas operaciones de nivel de almacenamiento de datos o realizadas a modo de respuesta a la llamada de un cliente son costosas y, a partir de cierto punto, es preferible no anularlas si no es necesario). Así pues, durante la operación aumenta el costo para reducir la probabilidad de que la instancia o la réplica de servicio se mueva, y después, cuando la operación se ha realizado, la devuelve al estado normal.

## Pasos siguientes
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- Para obtener más información sobre cómo el Administrador de recursos de clúster administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0824_2016-->