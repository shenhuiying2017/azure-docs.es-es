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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: eef9cbca0479801cbac53ae1ae5713d4dac6a4bf
ms.openlocfilehash: 57525cd41d518b12f3f465d5dea7247659e3c8b6


---
# <a name="introduction-to-application-groups"></a>Introducción a los grupos de aplicaciones
Cluster Resource Manager de Service Fabric administra normalmente los recursos mediante la distribución uniforme de la carga (representada mediante [métricas](service-fabric-cluster-resource-manager-metrics.md)) por todo el clúster. Service Fabric también administra la capacidad de los nodos del clúster y el clúster como un todo por medio de la noción de [capacidad](service-fabric-cluster-resource-manager-cluster-description.md). Las métricas y la capacidad funcionan muy bien con muchas cargas de trabajo diferentes, pero patrones que hacen un uso intensivo de diferentes instancias de aplicación de Service Fabric en ocasiones introducen requisitos adicionales. Estos requisitos adicionales pueden ser normalmente:

* Posibilidad de reservar capacidad para los servicios de una instancia de aplicación en el clúster
* Posibilidad de limitar el número total de nodos en los que se ejecutan servicios dentro de una aplicación
* Definición de capacidades en la propia instancia de aplicación con el fin de limitar el número o el consumo total de recursos de los servicios que contiene

Para cumplir estos requisitos, Service Fabric Cluster Resource Manager admite grupos de aplicaciones.

## <a name="managing-application-capacity"></a>Administración de capacidad de aplicación
La capacidad de la aplicación puede utilizarse para limitar el número de nodos que va abarcar una aplicación. También puede limitar la carga de métrica de que los servicios de las aplicaciones de los nodos individuales y en total. También puede usarse para reservar recursos del clúster para la aplicación.

Se puede definir capacidad de aplicación para nuevas aplicaciones cuando se crean, o también se puede actualizar para aplicaciones existentes.

### <a name="limiting-the-maximum-number-of-nodes"></a>Limitación del número máximo de nodos
El caso más simple de uso de la capacidad de aplicación es cuando es necesario limitar una instancia de aplicación a un determinado número máximo de nodos. Si no se especifica ninguna capacidad de aplicación, Service Fabric Cluster Resource Manager crea y coloca los servicios de acuerdo con las reglas normales (equilibrio o desfragmentación).

La siguiente imagen muestra una instancia de la aplicación con y sin un número máximo de nodos definidos. No hay ninguna garantía sobre qué réplicas o instancias de qué servicios se colocarán juntas, o qué nodos concretos se usarán.

<center>
![Definición del número máximo de nodos para la instancia de aplicación][Image1]
</center>

En el ejemplo de la izquierda, la aplicación no tiene definida capacidad y tiene tres servicios. Cluster Resource Manager reparte todas las réplicas a través de seis nodos disponibles para lograr el mejor equilibrio en el clúster. En el ejemplo de la derecha, se ve la misma aplicación limitada en tres nodos.

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
fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;

adUpdate.Metrics.Add(appMetric);
```

## <a name="application-metrics-load-and-capacity"></a>Métrica, carga y capacidad de aplicación
Los grupos de aplicaciones también le permiten definir métricas asociadas con una instancia de aplicación dada, además de la capacidad de la aplicación con respecto a esas métricas. De este modo, podrá reservar y limitar el consumo de recursos de los servicios dentro de esa instancia de la aplicación, además de realizar un seguimiento de ellos.

En cada métrica de la aplicación, hay dos valores que se pueden establecer:

* **Capacidad total de aplicación**: esta configuración representa la capacidad total de la aplicación con respecto a una determinada métrica. Cluster Resource Manager no permite crear nuevos servicios en esta instancia de aplicación que puede provocar que la carga total supere este valor. Por ejemplo, supongamos que la instancia de la aplicación tenía una capacidad de 10 y ya tenía la carga de 5. En este caso, podría no poder crearse servicio con una carga total predeterminada de 10.
* **Capacidad máxima de nodo**: esta configuración especifica la carga máxima total de réplicas de los servicios de las aplicaciones en un único nodo. Si la carga total en el nodo supera esta capacidad, Cluster Resource Manager tratará de mover las réplicas a otros nodos de forma que se respete la restricción de capacidad.

## <a name="reserving-capacity"></a>Reserva de capacidad
Otro uso común de los grupos de aplicaciones consiste en asegurarse de que se reservan recursos en el clúster para una instancia de aplicación determinada. Esto ocurre incluso si la instancia de la aplicación aún no tiene ningún servicio, o aunque no se hayan consumido todavía los recursos. Veamos cómo funciona esto.

### <a name="specifying-a-minimum-number-of-nodes-and-resource-reservation"></a>Especificación de un número mínimo de nodos y reserva de recursos
Para reservar recursos para una instancia de aplicación, es necesario especificar un par de parámetros adicionales: *MinimumNodes* y *NodeReservationCapacity*.

* **MinimumNodes**: igual que puede especificar un número máximo de nodos de destino en los que se pueden ejecutar los servicios de una aplicación, también puede especificar el número mínimo. Esta configuración define el número de nodos en los que se debe reservar recursos, lo que garantiza la capacidad dentro de un clúster como parte de la creación de la instancia de aplicación.
* **NodeReservationCapacity**: la capacidad de reserva del nodo se puede definir para cada métrica dentro de la aplicación. Esta configuración define la cantidad de métrica de carga reservada para la aplicación en cualquier nodo donde se coloque alguna de las réplicas o instancias de los servicios que contiene.

Veamos un ejemplo de la reserva de capacidad:

<center>
![Definición de la capacidad reservada para la instancia de aplicación][Image2]
</center>

En el ejemplo de la izquierda, las aplicaciones no tienen definida ninguna capacidad de aplicación. Cluster Resource Manager equilibra todos los elementos según las reglas normales.

En el ejemplo de la derecha, supongamos que la aplicación se creó con la configuración siguiente:

* Valor de MinimumNodes establecido en&2;
* Valor de MaximumNodes se establece en&3;
* Una aplicación métrica definida con
  * NodeReservationCapacity de 20
  * MaximumNodeCapacity de 50
  * TotalApplicationCapacity de 100

Service Fabric reserva capacidad en dos nodos para la aplicación azul y no permite que los servicios de otras instancias de aplicación en el clúster consuman esa capacidad. Esta capacidad de aplicación reservada se considera consumida y cuenta para la capacidad restante del clúster en ese nodo y en el clúster.

Cuando se crea una aplicación con reserva, Cluster Resource Manager reserva una capacidad igual a MinimumNodes * NodeReservationCapacity (para cada métrica). Sin embargo, se reserva capacidad en un nodo específico cuando al menos una réplica se coloca en él. Esta reserva posterior permite flexibilidad y una mejor utilización de recursos, ya que solo se reservan recursos en nodos cuando resulta necesario.

## <a name="obtaining-the-application-load-information"></a>Obtención de la información de carga de la aplicación
Para cada aplicación que tenga definida la capacidad, puede obtener la información de la carga agregada notificada por las réplicas de sus servicios.

Por ejemplo, se puede recuperar la carga mediante el siguiente cmdlet de PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

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

Este comando quita todos los parámetros de capacidad de aplicación de la aplicación. El efecto del comando es inmediato. Una vez completado este comando, Cluster Resource Manager vuelve a utilizar el comportamiento predeterminado para administrar aplicaciones. Los parámetros de capacidad de aplicación pueden especificarse de nuevo a través de Update-ServiceFabricApplication.

### <a name="restrictions-on-application-capacity"></a>Restricciones en la capacidad de aplicación
Existen varias restricciones en los parámetros de capacidad de aplicación que se deben respetar. En caso de errores de validación, la creación o actualización de la aplicación se rechazará con un error y no se realizarán los cambios.

* Todos los parámetros de número entero deben ser números no negativos.
* MinimumNodes nunca debe ser mayor que MaximumNodes.
* Si se definen capacidades para una métrica de carga, deben seguir estas reglas:
  * La capacidad de reserva del nodo no debe ser superior a la capacidad máxima del nodo. Por ejemplo, no puede limitar la capacidad de la métrica "CPU" en el nodo a&2; unidades ni tratar de reservar&3; unidades en cada nodo.
  * Si se especifica MaximumNodes, el producto de MaximumNodes y la capacidad máxima de nodo no debe ser superior a la capacidad total de la aplicación. Por ejemplo, supongamos que la capacidad máxima del nodo de métrica para la carga "CPU" se establece en&8;. Supongamos también que establece el máximo de nodos en&10;. En este caso, la capacidad total de aplicación debe ser mayor que 80 en esta métrica de carga.

Las restricciones se aplican durante la creación de la aplicación (en el cliente) y durante la actualización de la aplicación (en el servidor).

## <a name="how-not-to-use-application-capacity"></a>Cuándo no usar la capacidad de aplicación
* No trate de usar las características de grupo de aplicaciones para restringir la aplicación a un subconjunto de nodos _específico_. En otras palabras, puede especificar que la aplicación se ejecute en, al menos,&5; nodos, pero decidir en qué&5;. Puede restringir una aplicación a nodos específicos mediante las restricciones de selección de ubicación de los servicios.
* No trate de usar la capacidad de aplicación para asegurarse de que dos servicios de la misma aplicación siempre se colocarán juntos. Garantizar que los servicios se ejecuten en el mismo nodo es posible gracias a las restricciones de selección de ubicación o afinidad en función de los requisitos concretos.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md)
* Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para más información sobre cómo funcionan las métricas en general, lea el artículo [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre ellas, consulte este artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png



<!--HONumber=Jan17_HO1-->


