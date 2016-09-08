<properties
   pageTitle="Cluster Resource Manager de Service Fabric: grupos de aplicaciones | Microsoft Azure"
   description="Información general de la funcionalidad de grupo de aplicaciones en Cluster Resource Manager de Service Fabric"
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

# Introducción a los grupos de aplicaciones
Cluster Resource Manager de Service Fabric administra normalmente los recursos mediante la distribución uniforme de la carga (representada mediante métricas) por todo el clúster. Service Fabric también administra la capacidad de los nodos del clúster y el clúster como un todo por medio de la noción de capacidad. Esta solución funciona muy bien con muchos tipos de cargas de trabajo diferentes, pero patrones que hacen un uso intensivo de diferentes instancias de aplicación de Service Fabric en ocasiones introducen requisitos adicionales. Estos requisitos adicionales pueden ser normalmente:

- Posibilidad de reservar capacidad para los servicios de una instancia de aplicación en alguna cantidad de nodos.
- Posibilidad de limitar el número total de nodos en los que se permite ejecutar un conjunto dado de servicios dentro de una aplicación.
- Definición de capacidades en la propia instancia de aplicación con el fin de limitar el número o el consumo total de recursos de los servicios que contiene.

Para cumplir estos requisitos, hemos desarrollado compatibilidad con lo que llamamos grupos de aplicaciones.

## Administración de capacidad de aplicación
La capacidad de aplicación se puede usar para limitar el número de nodos distribuidos por una aplicación, así como la métrica de carga total de las instancias de la aplicación en nodos individuales. También puede usarse para reservar recursos del clúster para la aplicación.

Se puede definir capacidad de aplicación para nuevas aplicaciones cuando se crean, o también se puede actualizar para aplicaciones existentes que se crearon sin especificar dicha capacidad.

### Limitación del número máximo de nodos
El caso más simple de uso de la capacidad de aplicación es cuando es necesario limitar una instancia de aplicación a un determinado número máximo de nodos. Si no se ha especificado capacidad de aplicación, Cluster Resource Manager de Service Fabric crea una instancia de las réplicas de acuerdo con reglas normales (equilibrio o desfragmentación). Esto significa normalmente que sus servicios se distribuirán entre todos los nodos disponibles en el clúster o, si la desfragmentación está activada, en un número de nodos más pequeño pero arbitrario.

En la imagen siguiente se muestra la posible colocación de una instancia de aplicación sin el número máximo de nodos definido, y luego la misma aplicación con un número máximo de nodos establecido. Tenga en cuenta que no hay ninguna garantía sobre qué réplicas o instancias de qué servicios se colocarán juntas.

![Definición del número máximo de nodos para la instancia de aplicación][Image1]

En el ejemplo de la izquierda, la aplicación no tiene definida capacidad y tiene tres servicios. CRM ha tomado una decisión lógica para distribuir todas las réplicas entre seis nodos disponibles con el fin de conseguir el mejor equilibrio en el clúster. En el ejemplo de la derecha, vemos la misma aplicación que está limitada a tres nodos; aquí CRM de Service Fabric ha conseguido el mejor equilibrio para las réplicas de los servicios de la aplicación.

El parámetro que controla este comportamiento se denomina MaximumNodes. Este parámetro se puede establecer durante la creación de aplicaciones, o se puede actualizar para una instancia de aplicación que ya se estaba ejecutando, en cuyo caso CRM de Service Fabric restringe las réplicas de los servicios de la aplicación al número máximo de nodos definido.

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

## Métrica, carga y capacidad de aplicación
Los grupos de aplicaciones también le permiten definir métricas asociadas con una instancia de aplicación dada, además de la capacidad de la aplicación con respecto a esas métricas. Así, por ejemplo, podría definir que se creen tantos servicios como quiera.

Para cada métrica, se pueden establecer dos valores para describir la capacidad de esa instancia de aplicación:

-	Capacidad total de aplicación: representa la capacidad total de la aplicación con respecto a una determinada métrica. CRM de Service Fabric intentará limitar la suma de métricas de carga de los servicios de esta aplicación al valor especificado; además, si los servicios ya están consumiendo carga hasta este límite, Cluster Resource Manager de Service Fabric no permitirá la creación de ningún nuevo servicio o partición que haga que la carga total supere este límite.
-	Capacidad máxima de nodo: especifica la carga máxima total de réplicas de los servicios de las aplicaciones en un único nodo. Si la carga total en el nodo supera esta capacidad, CRM de Service Fabric intentará mover las réplicas a otros nodos de forma que se respete la restricción de capacidad.

## Reserva de capacidad
Otro uso común de los grupos de aplicaciones es garantizar la reserva de recursos del clúster para una instancia de aplicación dada, incluso si la instancia de aplicación aún no contiene los servicios o los servicios no están consumiendo aún los recursos. Veamos cómo funciona esto.

### Especificación de un número mínimo de nodos y reserva de recursos
Para reservar recursos para una instancia de aplicación, es necesario especificar un par de parámetros adicionales: *MinimumNodes* y *NodeReservationCapacity*.

- MinimumNodes: igual que puede especificar un número máximo de nodos de destino en los que se pueden ejecutar los servicios de una aplicación, también puede especificar el número mínimo de nodos en los que se debe ejecutar una aplicación. Esta configuración define eficazmente el número de nodos en los que se debe reservar un mínimo de recursos, lo que garantiza la capacidad dentro de un clúster como parte de la creación de la instancia de aplicación.
- NodeReservationCapacity: la capacidad de reserva del nodo se puede definir para cada métrica dentro de la aplicación. Con ello se define la cantidad de métrica de carga reservada para la aplicación en cualquier nodo donde se coloque alguna de las réplicas o instancias de los servicios que contiene.

Veamos un ejemplo de la reserva de capacidad:

![Definición de la capacidad reservada para la instancia de aplicación][Image2]

En el ejemplo de la izquierda, las aplicaciones no tienen definida ninguna capacidad de aplicación. Cluster Resource Manager de Service Fabric equilibrará las réplicas y las instancias de los servicios secundarios de la aplicación junto con las de otros servicios (fuera de ella) para garantizar el equilibrio en el clúster.

En el ejemplo de la derecha, supongamos que la aplicación se creó con un valor de MinimumNodes establecido en 2, un valor de MaximumNodes establecido en 3 y una métrica de aplicación definida con una reserva de 20, además la capacidad máxima en un nodo es 50 y la capacidad total de la aplicación es 100. En este caso, Service Fabric reservará capacidad en dos nodos para la aplicación azul y no permitirá que otras réplicas del clúster consuman esa capacidad. Esta capacidad de aplicación reservada se considera consumida y cuenta para la capacidad restante del clúster.

Cuando se crea una aplicación con reserva, Cluster Resource Manager reserva una capacidad equivalente al valor de MinimumNodes * NodeReservationCapacity en el clúster, pero no reserva capacidad en nodos específicos hasta que las réplicas de los servicios de la aplicación se hayan creado y reservado. Con esto se consigue flexibilidad, dado que solo se eligen nodos para nuevas réplicas cuando se crean. Se reserva capacidad en un nodo específico cuando al menos una réplica se coloca en él.

## Obtención de la información de carga de la aplicación
Para cada aplicación que tenga definida la capacidad, puede obtener la información de la carga agregada notificada por las réplicas de sus servicios. Con este fin, Service Fabric proporciona consultas de PowerShell y de API administrada.

Por ejemplo, se puede recuperar la carga mediante el siguiente cmdlet de PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1

```

El resultado de esta consulta contendrá la información básica sobre la capacidad de aplicación que se especificó para ella, como los nodos mínimos y máximos. También habrá información sobre el número de nodos que la aplicación usa actualmente. Por lo tanto, para cada métrica de carga habrá la siguiente información:
- Nombre de métrica: nombre de la métrica.
-	Capacidad de reserva: capacidad del clúster que está reservada en el clúster para esta aplicación.
-	Carga de la aplicación: carga total de las réplicas secundarias de esta aplicación.
-	Capacidad de aplicación: valor máximo permitido de la carga de la aplicación.

## Eliminación de la capacidad de aplicación
Cuando se han establecido los parámetros de capacidad de aplicación, se pueden eliminar mediante las API de actualización de aplicaciones o los cmdlets de PowerShell. Por ejemplo:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Este comando quitará todos los parámetros de capacidad de aplicación, y Cluster Resource Manager de Service Fabric comenzará a tratarla como cualquier otra aplicación del clúster que no tenga estos parámetros definidos. El efecto del comando es inmediato y Cluster Resource Manager eliminará todos los parámetros de capacidad de aplicación; para especificarlos de nuevo, será necesario llamar a las API de actualización de la aplicación con los parámetros adecuados.

## Restricciones en la capacidad de aplicación
Existen varias restricciones en los parámetros de capacidad de aplicación que se deben respetar. En caso de errores de validación, la creación o actualización de la aplicación se rechazará con un error. Todos los parámetros de número entero deben ser números no negativos. Además, los parámetros individuales presentan las siguientes restricciones:

-	MinimumNodes nunca debe ser mayor que MaximumNodes.
-	Si se definen capacidades para una métrica de carga, deben seguir estas reglas:
  - La capacidad de reserva del nodo no debe ser superior a la capacidad máxima del nodo. Por ejemplo, no puede limitar la capacidad de la métrica "CPU" en el nodo a 2 unidades e intentar reservar 3 unidades en cada nodo.
  - Si se especifica MaximumNodes, el producto de MaximumNodes y la capacidad máxima de nodo no debe ser superior a la capacidad total de la aplicación. Por ejemplo, si establece la capacidad máxima de nodo de la métrica de carga "CPU" en 8 y establece los nodos máximos en 10, entonces la capacidad total de la aplicación debe ser superior a 80 para esta métrica de carga.

Las restricciones se aplican durante la creación de la aplicación (en el cliente) y durante la actualización de la aplicación (en el servidor). Durante la creación, este es un ejemplo de una infracción clara de los requisitos dado que MaximumNodes < MinimumNodes, luego el comando producirá un error en el cliente antes de que la solicitud se envíe incluso al clúster de Service Fabric:

``` posh
New-ServiceFabricApplication –Name fabric:/MyApplication1 –MinimumNodes 6 –MaximumNodes 2
```

El siguiente sería un ejemplo de actualización no válida. Si tomamos una aplicación existente y actualizamos los nodos máximos con algún valor, la actualización pasa:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MaximumNodes 2
```

Después de eso, podemos intentar actualizar los nodos mínimos:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 6 –MinimumNodes 6
```

El cliente no tiene suficiente contexto sobre la aplicación, así que permitirá que la actualización pase al clúster de Service Fabric. Sin embargo, en el clúster, Service Fabric validará el nuevo parámetro junto con los parámetros existentes y la operación de actualización dará error porque el valor de los nodos mínimos es superior al de los nodos máximos. En este caso, los parámetros de la capacidad de aplicación permanecerán sin cambios.

Estas restricciones se establecen con el fin de que Cluster Resource Manager pueda proporcionar la mejor colocación para las réplicas de los servicios de las aplicaciones.

## Cuándo no usar la capacidad de aplicación

-	No utilice la capacidad de aplicación para restringir la aplicación a un subconjunto específico de nodos: aunque Service Fabric garantiza que se respetan los nodos máximos para cada aplicación que tiene la capacidad especificada, los usuarios no pueden decidir en qué nodos se crearán instancias. Para ello, se pueden usar restricciones de colocación para servicios.
-	No utilice la capacidad de aplicación para asegurarse de que dos servicios de la misma aplicación siempre se colocarán juntos. Para ello, utilice relaciones de afinidad entre servicios; la afinidad solo se puede limitar a los servicios que realmente se deben colocar juntos.

## Pasos siguientes
- Para obtener más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles [Configuración del Administrador de recursos de clúster para los servicios de Service Fabric](service-fabric-cluster-resource-manager-configure-services.md).
- Para más información sobre la forma en que Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md).
- Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- Para más información sobre cómo funcionan las métricas en general, lea el artículo [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md).
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte el artículo [Descripción de un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).


[Image1]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png

<!---HONumber=AcomDC_0824_2016-->