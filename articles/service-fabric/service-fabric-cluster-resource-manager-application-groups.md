---
title: 'Cluster Resource Manager de Service Fabric: grupos de aplicaciones | Microsoft Azure'
description: "Información general de la funcionalidad de grupo de aplicaciones en Cluster Resource Manager de Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3212631ede7125bd849c2d9ba86ba2a0747d69ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-application-groups"></a>Introducción a los grupos de aplicaciones
Cluster Resource Manager de Service Fabric administra normalmente los recursos mediante la distribución uniforme de la carga (representada mediante [métricas](service-fabric-cluster-resource-manager-metrics.md)) por todo el clúster. Service Fabric administra la capacidad de los nodos del clúster y el clúster como un todo mediante la [capacidad](service-fabric-cluster-resource-manager-cluster-description.md). Las métricas y la capacidad funcionan muy bien con muchas cargas de trabajo diferentes, pero patrones que hacen un uso intensivo de diferentes instancias de aplicación de Service Fabric en ocasiones introducen requisitos adicionales. Por ejemplo, puede que desee:

- Reservar alguna capacidad en los nodos del clúster para los servicios dentro de alguna instancia de aplicación con nombre
- Limitar el número de nodos en los que se ejecutan los servicios dentro de una instancia de aplicación con nombre (en lugar de repartirlos por el clúster entero)
- Definir capacidades en la propia instancia de aplicación con nombre para limitar el número de servicios o el consumo total de recursos de los servicios que contiene

Para cumplir estos requisitos, Cluster Resource Manager de Service Fabric admite una característica llamada Grupo de aplicaciones.

## <a name="limiting-the-maximum-number-of-nodes"></a>Limitación del número máximo de nodos
El caso más simple de uso de la capacidad de aplicación es cuando es necesario limitar una instancia de aplicación a un número máximo de nodos determinado. Todos los servicios dentro de esa instancia de aplicación se consolidan en un número establecido de máquinas. La consolidación es útil cuando se intenta predecir el límite de uso de un recurso físico mediante los servicios incluidos en esa instancia de aplicación con nombre. 

La siguiente imagen muestra una instancia de aplicación con y sin un número máximo de nodos definidos:

<center>
![Definición del número máximo de nodos para la instancia de aplicación][Image1]
</center>

En el ejemplo de la izquierda, la aplicación no tiene definido un número máximo de nodos, y tiene tres servicios. Cluster Resource Manager ha repartido todas las réplicas entre seis nodos disponibles para lograr el mejor equilibrio en el clúster (el comportamiento predeterminado). En el ejemplo de la derecha, se ve la misma aplicación limitada en tres nodos.

El parámetro que controla este comportamiento se denomina MaximumNodes. Este parámetro se puede establecer durante la creación de la aplicación, o actualizarse con una instancia de la aplicación que ya se estaba ejecutando.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Dentro del conjunto de nodos, Cluster Resource Manager no garantiza que los objetos de servicio se coloquen juntos o qué nodos se van a usar.

## <a name="application-metrics-load-and-capacity"></a>Métrica, carga y capacidad de aplicación
Los grupos de aplicaciones también le permiten definir métricas asociadas con una instancia de aplicación con nombre dada, además de la capacidad de la instancia de aplicación con respecto a esas métricas. Las métricas de aplicación le permiten reservar y limitar el consumo de recursos de los servicios dentro de esa instancia de la aplicación, además de realizar un seguimiento de ellos.

En cada métrica de la aplicación, hay dos valores que se pueden establecer:

- **Capacidad total de aplicación**: esta configuración representa la capacidad total de la aplicación con respecto a una determinada métrica. Cluster Resource Manager no permite crear nuevos servicios en esta instancia de aplicación que puede provocar que la carga total supere este valor. Por ejemplo, supongamos que la instancia de la aplicación tenía una capacidad de 10 y ya tenía la carga de 5. La creación de un servicio con una carga predeterminada total de 10 no estaría permitida.
- **Capacidad máxima de nodo**: este valor especifica la carga máxima total para la aplicación en un único nodo. Si la carga supera esta capacidad, Cluster Resource Manager mueve las réplicas a otros nodos para que la carga se reduzca.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reserva de capacidad
Otro uso común de los grupos de aplicaciones consiste en asegurarse de que se reservan recursos en el clúster para una instancia de aplicación determinada. El espacio se reserva siempre cuando se crea la instancia de aplicación.

La reserva de espacio en el clúster para la aplicación sucede inmediatamente, incluso cuando:
- se crea la instancia de aplicación, pero aún no contiene ningún servicio
- el número de servicios en la instancia de aplicación cambia cada vez 
- los servicios existen pero no consumen recursos 

Para reservar recursos para una instancia de aplicación, es necesario especificar dos parámetros adicionales: *MinimumNodes* y *NodeReservationCapacity* .

- **MinimumNodes**: define el número mínimo de nodos en los que se debe ejecutar la instancia de aplicación.  
- **NodeReservationCapacity**: este valor es por métrica de la aplicación. El valor es la cantidad de esa métrica reservada para la aplicación en cualquier nodo donde se ejecutan los servicios de esa aplicación.

Al combinar **MinimumNodes** y **NodeReservationCapacity** se garantiza una reserva mínima de carga para la aplicación dentro del clúster. Si al clúster le queda menos capacidad que la reserva total necesaria, la creación de la aplicación dará error. 

Veamos un ejemplo de la reserva de capacidad:

<center>
![Definición de la capacidad reservada para la instancia de aplicación][Image2]
</center>

En el ejemplo de la izquierda, las aplicaciones no tienen definida ninguna capacidad de aplicación. Cluster Resource Manager equilibra todos los elementos según las reglas normales.

En el ejemplo de la derecha, supongamos que Application1 se creó con la configuración siguiente:

- Valor de MinimumNodes establecido en 2
- Una aplicación métrica definida con
  - NodeReservationCapacity de 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserva capacidad en dos nodos para Application1 y no permite que los servicios de Application2 consuman esa capacidad incluso si en ese momento los servicios de Application1 no consumen ninguna carga. Esta capacidad de aplicación reservada se considera consumida y cuenta para la capacidad restante en ese nodo y dentro del clúster.  La reserva se deduce inmediatamente de la capacidad restante del clúster; sin embargo, el consumo reservado se deduce de la capacidad de un nodo específico solo cuando al menos un objeto de servicio se coloca dentro de él. Esta reserva posterior permite flexibilidad y una mejor utilización de recursos, ya que solo se reservan recursos en nodos cuando resulta necesario.

## <a name="obtaining-the-application-load-information"></a>Obtención de la información de carga de la aplicación
Para cada aplicación que tenga una capacidad de aplicación definida para una o varias métricas, puede obtener la información sobre la carga agregada notificada por las réplicas de sus servicios.

Powershell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

La consulta ApplicationLoad devuelve la información básica de la capacidad de aplicación que se especificó. Esta información incluye la información de nodos mínimos y máximos, y la cantidad que la aplicación está usando actualmente. También incluye información de cada métrica de carga de la aplicación, por ejemplo:

* Nombre de métrica: nombre de la métrica.
* Capacidad de reserva: capacidad del clúster que está reservada en el clúster para esta aplicación.
* Carga de la aplicación: carga total de las réplicas secundarias de esta aplicación.
* Capacidad de aplicación: valor máximo permitido de la carga de la aplicación.

## <a name="removing-application-capacity"></a>Eliminación de la capacidad de aplicación
Cuando se han establecido los parámetros de capacidad de aplicación, se pueden eliminar mediante las API de actualización de aplicaciones o los cmdlets de PowerShell. Por ejemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando quita todos los parámetros de administración de la capacidad de aplicación de la instancia de aplicación. Es decir, MinimumNodes, MaximumNodes y las métricas de la aplicación, si las hay. El efecto del comando es inmediato. Después de que finaliza este comando, Cluster Resource Manager usa el comportamiento predeterminado para administrar las aplicaciones. Los parámetros de la capacidad de aplicación se pueden especificar de nuevo mediante `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Restricciones en la capacidad de aplicación
Existen varias restricciones en los parámetros de capacidad de aplicación que se deben respetar. Si hay errores de validación, no se producirá ningún cambio.

- Todos los parámetros de número entero deben ser números no negativos.
- MinimumNodes nunca debe ser mayor que MaximumNodes.
- Si se definen capacidades para una métrica de carga, deben seguir estas reglas:
  - La capacidad de reserva del nodo no debe ser superior a la capacidad máxima del nodo. Por ejemplo, no puede limitar la capacidad de la métrica "CPU" en el nodo a 2 unidades ni tratar de reservar 3 unidades en cada nodo.
  - Si se especifica MaximumNodes, el producto de MaximumNodes y la capacidad máxima de nodo no debe ser superior a la capacidad total de la aplicación. Por ejemplo, supongamos que la capacidad máxima del nodo de métrica para la carga "CPU" se establece en 8. Supongamos también que establece los nodos máximos en 10. En este caso, la capacidad total de aplicación debe ser mayor que 80 en esta métrica de carga.

Las restricciones se aplican tanto durante la creación de aplicaciones como durante las actualizaciones.

## <a name="how-not-to-use-application-capacity"></a>Cuándo no usar la capacidad de aplicación
- No trate de usar las características de grupo de aplicaciones para restringir la aplicación a un subconjunto de nodos _específico_. En otras palabras, puede especificar que la aplicación se ejecute en, al menos, 5 nodos, pero decidir en qué 5. Puede restringir una aplicación a nodos específicos mediante las restricciones de selección de ubicación de los servicios.
- No intente usar la capacidad de aplicación para asegurarse de que dos servicios de la misma aplicación se coloquen en los mismos nodos. En su lugar use la [afinidad](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) o las [restricciones de colocación](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre la configuración de servicios, vaya a [este vínculo](service-fabric-cluster-resource-manager-configure-services.md).
- Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Para más información sobre cómo funcionan las métricas en general, lea el artículo [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre ellas, consulte este artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
