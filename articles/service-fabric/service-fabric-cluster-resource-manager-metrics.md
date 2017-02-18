---
title: "Administración de la carga de microservicios de Azure mediante métricas | Microsoft Docs"
description: "Aprenda cómo configurar y usar las métricas en Service Fabric para administrar el consumo de recursos del servicio."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c0e5418574920495bf277f4127b97ff5b07b56ef


---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Administración de consumo y carga de recursos en Service Fabric con métricas
"Métricas" es el término genérico en Service Fabric para los recursos por los que se interesan sus servicios y que proporcionan los nodos del clúster. Por lo general, una métrica es todo lo que desea administrar en relación con el rendimiento de los servicios.

Las métricas son, por ejemplo, memoria, disco y uso de CPU. Estas son métricas físicas, recursos que corresponden a los recursos físicos en el nodo que es necesario a administrar. Las métricas también pueden ser (y normalmente son) métricas lógicas. Las métricas lógicas son, por ejemplo, "MyWorkQueueDepth", "MessagesToProcess" o "TotalRecords". Las métricas lógicas se definen para la aplicación y corresponden al consumo de recursos físicos, pero la aplicación no sabe realmente cómo medirlo. Esto es habitual. La mayoría de las métricas que vemos que usan las personas son métricas lógicas. Normalmente esto se debe a que, hoy en día, muchos servicios se escriben en código administrado. Código administrado significa que desde dentro de un proceso de host puede ser difícil medir e informar del consumo de recursos físicos por parte de un solo objeto de servicio. Para reducir la complejidad de la medición y la generación de informes de sus propias métricas, incluimos algunas métricas predeterminadas listas para usar.

## <a name="default-metrics"></a>Métricas predeterminadas
Supongamos que desea empezar a trabajar y no sabe qué recursos va a consumir o incluso cuáles serán importantes para usted. Por tanto, implementa y crea los servicios sin especificar ninguna métrica. ¡Eso está bien! Cluster Resource Manager de Service Fabric elige algunas métricas sencillas para usted. Las métricas predeterminadas que usamos cuando no se especifica ninguna se llaman PrimaryCount, ReplicaCount y Count. En la siguiente tabla se muestra la cantidad de carga para cada una de estas métricas que está asociada a cada objeto de servicio:

| Métrica | Carga de instancia sin estado | Carga secundaria con estado | Carga principal con estado |
| --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |
| ReplicaCount |0 |1 |1 |
| Recuento |1 |1 |1 |

Entonces, con estas métricas predeterminadas, ¿qué se obtiene? Para cargas de trabajo básicas, obtendrá una distribución del trabajo bastante buena. En el siguiente ejemplo, veamos lo que sucede cuando se crean dos servicios. El primero es un servicio con estado con tres particiones y un conjunto de tres réplicas de destino. El segundo es un servicio sin estado con una partición y un recuento de tres instancias:

<center>
![Diseño de clúster con métricas predeterminadas][Image1]
</center>

Esto es lo que obtiene:

* Las réplicas principales para el servicio con estado no están apiladas en un único nodo.
* Las réplicas de la misma partición no están en el mismo nodo.
* El número total de réplicas principales y secundarias está distribuido en el clúster.
* La asignación del número total de objetos de servicio es uniforme en cada nodo.

¡Bien!

Esto funciona bien hasta que empieza a ejecutar gran cantidad de cargas de trabajo reales: ¿cuál es la probabilidad de que el esquema de creación de particiones elegido dé como resultado una utilización perfectamente uniforme de todas las particiones? ¿Cuál es la probabilidad de que la carga de un servicio dado sea constante con el tiempo, o incluso la misma que ahora?

En la práctica, podría trabajar solo con las métricas predeterminadas, pero eso suele significar que el uso del clúster es inferior al que querría. Esto es así porque los informes de métricas predeterminados no son adaptables y presuponen que todo es equivalente. En el peor de los casos, usar solo los valores predeterminados producirá una sobrecarga en los nodos y problemas de rendimiento. Se puede funcionar mejor con métricas personalizadas e informes de carga dinámica, tema que se aborda a continuación. En cualquier carga de trabajo seria, las probabilidades de que todos los servicios sean equivalentes para siempre son bajas. Si está interesado en obtener el máximo partido de su clúster y evitar problemas de rendimiento, empiece a pensar en las métricas personalizadas.

## <a name="custom-metrics"></a>Métricas personalizadas
Las métricas se configuran para cada instancia de servicio con nombre cuando se crea el servicio.

Todas las métricas tienen algunas propiedades que la describen: un nombre, una carga predeterminada y un peso.

* Nombre de la métrica: nombre de la métrica. El nombre de la métrica es un identificador único de la métrica en el clúster desde la perspectiva de Resource Manager.
* Carga predeterminada: se representa de forma distinta dependiendo de si el servicio es con o sin estado.
  * Para los servicios sin estado, cada métrica tiene una propiedad única llamada DefaultLoad.
  * Para servicios con estado, se define lo siguiente:
    * PrimaryDefaultLoad: cantidad predeterminada de la métrica que este servicio consume cuando es principal.
    * SecondaryDefaultLoad: cantidad predeterminada de la métrica que este servicio consume cuando es secundario.
* Weight: importancia de la métrica con relación a las demás métricas de este servicio.

Si define métricas personalizadas y desea usar también las métricas predeterminadas, debe volver a agregarlas de forma explícita. Esto es porque quiere ser claro sobre la relación entre las métricas predeterminadas y las métricas personalizadas. Por ejemplo, quizás le preocupa el consumo de memoria o el valor de WorkQueueDepth mucho más que la distribución principal.

### <a name="defining-metrics-for-your-service---an-example"></a>Definición de las métricas del servicio: ejemplo
Supongamos que desea configurar un servicio que informa de una métrica llamada "MemoryInMb", y que también quiere usar las métricas predeterminadas. Supongamos también que ha realizado algunas mediciones y sabe que, normalmente, una réplica principal de ese servicio ocupa 20 unidades de "MemoryInMb", mientras que las secundarias ocupan 5. Sabre que la memoria es la métrica más importante en cuanto a la administración del rendimiento de este servicio en concreto, pero desea réplicas principales que estén equilibradas. Es buena idea equilibrar los nodos principales para que la pérdida de un nodo o dominio de error no afecte a la mayoría de las réplicas principales. Aparte de estos pequeños detalles, desea usar las métricas predeterminadas.

Este es el código que debe escribir para crear un servicio con esa configuración de métricas:

Código:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Recuerde: si solo desea utilizar las métricas predeterminadas, no necesita tocar la colección de métricas en absoluto ni hacer nada especial al crear el servicio).

Ahora que hemos visto la introducción y un ejemplo, vamos a analizar cada una de estas opciones de configuración con más detalle y hablaremos sobre su comportamiento.

## <a name="load"></a>Carga
La razón fundamental de la definición de las métricas es la representación de cierta carga. Carga es la cantidad de una determinada métrica que alguna instancia del servicio o réplica consume en un nodo específico. Se puede configurar la carga prevista al crear un servicio, al actualizar el servicio después de crearlo, al generar el informe por objeto de servicio, o en todos los casos anteriores.

## <a name="default-load"></a>Carga predeterminada
Carga predeterminada es cuánta carga consume cada objeto de servicio (instancia o réplica) de este servicio de forma predeterminada. Para los servicios más sencillos, la carga predeterminada es una definición estática que nunca se actualiza y, por tanto, se utiliza mientras dure el servicio. La carga predeterminada funciona bien para escenarios de planeamiento donde se dedican determinadas cantidades de recursos a las distintas cargas de trabajo.

Cluster Resource Manager permite a los servicios con estado especificar una carga predeterminada diferente para sus réplicas principales y secundarias, mientras que los servicios sin estado solo pueden especificar un valor. Para los servicios con estado, la carga predeterminada para las réplicas principales y secundarias son normalmente diferentes porque las réplicas realizan diferentes tipos de trabajo en cada rol. Por ejemplo, las réplicas principales atienden normalmente lecturas y escrituras (y la mayoría de la carga de cálculo), mientras que las secundarias no. Se espera que la carga predeterminada de una réplica principal sea mayor que la de las réplicas secundarias, pero las cifras reales dependerán de sus propias medidas.

## <a name="dynamic-load"></a>Carga dinámica
Supongamos que lleva un tiempo ejecutando el servicio. Con cierta supervisión, habrá observado lo siguiente:

1. Algunas particiones o instancias de un servicio determinado consumen más recursos que otras.
2. Algunos servicios tienen cargas que varían con el tiempo.

Hay una gran cantidad de cosas que podrían causar estos tipos de fluctuaciones de la carga. El servicio o la partición puede asociarse con un cliente determinado, o quizás corresponden a cargas de trabajo que varían a lo largo del día. Independientemente del motivo, no hay una sola cifra que se pueda usar para la carga predeterminada. Cualquier valor que elija como carga predeterminada será incorrecto en algún momento. Esto es un problema porque las cargas predeterminadas incorrectas hacen que Cluster Resource Manager asigne excesivos recursos al servicio, o demasiado pocos. Como resultado, tiene nodos sobre o infrautilizados, aunque Cluster Resource Manager piense que el clúster está equilibrado. Las cargas predeterminadas siguen siendo buenas porque proporcionan cierta información, pero no es la historia completa de las cargas de trabajo reales durante la mayoría del tiempo. Este es el motivo por el que Cluster Resource Manager permite que cada objeto de servicio actualice su propia carga en tiempo de ejecución. Esto se llama informes de carga dinámica.

Los informes de carga dinámica permiten a las réplicas o instancias ajustar su asignación o la carga de métricas notificada mientras estén vigentes. Normalmente, una instancia o una réplica del servicio que estaba inactiva y sin realizar ningún trabajo informará de que usó poca cantidad de una métrica determinada. Una réplica o instancia ocupadas indicarían que están usando más.

Los informes por instancia o réplica permiten que Cluster Resource Manager reorganice los objetos de servicio individuales del clúster para asegurarse de que los servicios obtienen los recursos que necesitan. Los servicios ocupados “reclaman” recursos de otras réplicas o instancias que están actualmente inactivas o realizando menos trabajo.

En el servicio confiable, el código para notificar la carga dinámicamente sería similar al siguiente:

Código:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

Las instancias y réplicas del servicio solo comunican la carga de las métricas para las que se configuraron cuando se crearon. La lista de métricas que un servicio puede notificar es el mismo conjunto especificado al crear el servicio. La lista de las métricas asociadas con el servicio también puede actualizarse dinámicamente. Si una instancia o una réplica del servicio intenta informar de la carga para una métrica que no está configurada, Service Fabric registra el informe pero lo ignora. Si se notifican otras métricas válidas en la misma llamada de API, dichos informes se aceptan y se utilizan. Esto es útil porque permite una mayor experimentación. El código puede medir y notificar todas las métricas que conoce, y el operador puede especificar y actualizar la configuración de las métricas de ese servicio sin tener que cambiar el código. Por ejemplo, el administrador o el equipo de operaciones podrían deshabilitar una métrica con un informe de errores para un servicio determinado, reconfigurar las ponderaciones de las métricas según el comportamiento, o habilitar una nueva métrica solo después de que el código se haya implementado y validado mediante otros mecanismos.

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Combinación de valores de carga predeterminados e informes de carga dinámica
Si la carga predeterminada no es suficiente y se recomiendan los informes de carga dinámicos, ¿se pueden usar ambos? Sí. De hecho, esta es la configuración recomendada. Cuando se establece la carga predeterminado y se usan los informes de carga dinámicos, la carga predeterminada actúa como una estimación hasta que se muestran los informes dinámicos. Esto es útil porque proporciona a Cluster Resource Manager algo con lo que trabajar. La carga predeterminada permite a Cluster Resource Manager colocar los objetos de servicio en un buen lugar cuando se crean. Si no se proporciona ninguna información de carga predeterminada, la ubicación de los servicios es aleatoria. Cuando más tarde llegan los informes de carga, Cluster Resource Manager casi siempre tiene que recolocar los servicios.

Vamos a tomar nuestro ejemplo anterior y vemos qué sucede cuando se agregan algunas métricas personalizadas e informes de carga dinámicos. En este ejemplo, utilizamos "Memoria" como una métrica de ejemplo. Suponemos que inicialmente se ha creado el servicio con estado con el comando siguiente:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Como recordatorio, esta sintaxis es ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

El aspecto de un diseño del clúster podría ser similar a este:

<center>
![Clúster equilibrado con métricas predeterminadas y personalizadas][Image2]
</center>

Algunas cosas que debe tener en cuenta:

* Puesto que las réplicas o instancias usan la carga predeterminada del servicio hasta que informan de su propia carga, se sabe que las réplicas dentro de la partición 1 del servicio con estado no han comunicado su carga dinámicamente.
* Las réplicas secundarias dentro de una partición pueden tener su propia carga
* En general, las métricas parecen equilibradas. En el caso de la memoria, la proporción entre la carga máxima y mínima es 1,75 (el nodo con la mayor carga es N3, con la mínima es N2 y 28/16 = 1,75).

Hay algunas cosas aún que es necesario explicar:

* ¿Qué determinó si una relación de 1,75 era razonable o no? ¿Cómo sabe Cluster Resource Manager si eso es suficiente o si hay más trabajo que hacer?
* ¿Cuándo se produce el equilibrio?
* ¿Qué significa que la ponderación de Memoria es alta?

## <a name="metric-weights"></a>Pesos de métrica
Es importante poder realizar un seguimiento de las mismas métricas en los diferentes servicios. Esta vista es la que permite que Cluster Resource Manager realice un seguimiento del consumo en el clúster, equilibre el consumo en todos los nodos y se asegure de que los nodos no exceden su capacidad. Sin embargo, los servicios pueden tener vistas diferentes en cuanto a la importancia de la misma métrica. Además, en un clúster con muchas métricas y una gran cantidad de servicios, puede que no existan soluciones perfectamente equilibradas para todas las métricas. ¿Cómo debe controlar Cluster Resource Manager estos casos?

La ponderación de las métricas permite a Cluster Resource Manager tomar decisiones sobre cómo equilibrar el clúster cuando no hay ninguna respuesta perfecta. La ponderación de las métricas también permite a Cluster Resource Manager equilibrar servicios específicos de manera diferente. Las métricas pueden tener cuatro niveles diferentes de ponderación: cero, baja, media o alta. Una métrica con una ponderación de cero no aporta nada a la hora de considerar si todo está equilibrado o no, pero su carga contribuye a aspectos como la capacidad.

El impacto real de las diferentes ponderaciones de métricas en el clúster es que Cluster Resource Manager genera diferentes soluciones. Las ponderaciones de las métricas indican a Cluster Resource Manager que ciertas métricas son más importantes que otras. Cuando no hay ninguna solución perfecta, Cluster Resource Manager puede preferir soluciones que equilibren mejor las métricas ponderadas. Si un servicio considera que una métrica no es importante, quizás encuentre que el uso de esa métrica está desequilibrado. Esto permite que otro servicio obtenga una distribución uniforme que es importante para él.

Eche un vistazo a un ejemplo de algunos informes de carga y cómo ponderaciones de métricas diferentes pueden generar distintas asignaciones en el clúster. En este ejemplo, se puede ver que al cambiar la ponderación relativa de las métricas, Cluster Resource Manager preferirá determinadas soluciones y creará diferentes disposiciones de los servicios.

<center>
![Ejemplo de ponderación de métricas y su impacto en soluciones de equilibrio][Image3]
</center>

En este ejemplo, hay cuatro servicios diferentes y todos ellos informan de los valores de dos métricas, MetricA y MetricB. En un caso, todos los servicios definen que MetricA es la más importante (ponderación = alta) y MetricB no es importante (ponderación = baja). En este caso, vemos que Cluster Resource Manager coloca los servicios de modo que MetricA está mejor equilibrada (tiene una desviación estándar menor) que MetricB. En el segundo caso, se invierten las ponderaciones de las métricas. Como resultado, Cluster Resource Manager probablemente intercambiaría los servicios A y B para conseguir una asignación en la que MetricB quede mejor equilibrada que MetricA.

### <a name="global-metric-weights"></a>Ponderaciones de métricas globales
Por tanto, si ServiceA define MetricA como la más importante y a ServiceB no le interesa en absoluto, ¿cuál es la ponderación real que se usará?

En realidad, para cada métrica se realiza el seguimiento con varias ponderaciones. El primer conjunto es la ponderación de cada servicio definido para la métrica. La otra ponderación es global, y es el promedio de todos los servicios que informan de esa métrica. Cluster Resource Manager usa ambas ponderaciones para calcular las puntuaciones de las soluciones. Esto se debe a que es importante que un servicio esté equilibrado según sus propias prioridades, pero también que el clúster en su conjunto esté correctamente asignado.

¿Qué ocurriría si Cluster Resource Manager no se preocupara por el equilibrio global y local? Bueno, no tiene mucho sentido crear soluciones que estén equilibradas globalmente, pero que tengan una asignación de recursos muy deficiente para los servicios individuales. En el siguiente ejemplo, veamos las métricas predeterminadas con las que se configura un servicio con estado, y qué sucede cuando solo se tiene en cuenta el equilibrio global:

<center>
![El impacto de una única solución global][Image4]
</center>

En el ejemplo de la parte superior, en el que solo se analizó el equilibrio global, el clúster en conjunto está equilibrado. Todos los nodos tienen el mismo número de réplicas principales y el mismo número total de réplicas. Sin embargo, si examina el impacto real de esta asignación, no es tan bueno: la pérdida de cualquier nodo afecta a una determina carga de trabajo desproporcionadamente, porque se lleva todas sus réplicas principales. Por ejemplo, si se produce un error en el primer nodo, se perderían las tres réplicas principales de las tres particiones diferentes del servicio Circle. Por el contrario, las particiones de los otros dos servicios (Triangle y Hexagon) perderían una réplica, pero no generarían interrupciones (aparte de tener que recuperar la réplica).

En el ejemplo inferior, Cluster Resource Manager ha distribuido las réplicas en función del equilibrio global y por servicio. Al calcular la puntuación de la solución, ofrece la mayor ponderación a la solución global, y una parte (configurable) a los servicios individuales. El equilibrio global se calcula en función del promedio de las ponderaciones de métricas configuradas para cada servicio. Cada servicio se equilibra según sus propias ponderaciones de métricas. Esto garantiza que los servicios están todo lo equilibrado que es posible, según sus propias necesidades. Como resultado, si se produce un error en el mismo primer nodo, la pérdida de las réplicas principales (y secundarias) se distribuye entre todas las particiones de todos los servicios. El impacto en cada una es el mismo.

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md).
* Definir las métricas de desfragmentación es una manera de consolidar la carga en los nodos en lugar de distribuirla. Para saber cómo configurar la desfragmentación, consulte [este artículo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* El costo del movimiento es una forma de señalizar al Administrador de recursos de clúster que determinados servicios son más caros de mover que otros. Para obtener más información sobre el costo del movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png



<!--HONumber=Jan17_HO4-->


