<properties
   pageTitle="Administración de métricas con el Administrador de recursos de clúster de Service Fabric de Azure | Microsoft Azure"
   description="Aprenda cómo configurar y usar las métricas en Service Fabric."
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

# Administración de consumo y carga de recursos en Service Fabric con métricas
"Métricas" es el término genérico en Service Fabric para los recursos por los que se interesan sus servicios y que proporcionan los nodos del clúster. Por lo general, una métrica es todo lo que desea administrar del rendimiento de los servicios.

Métricas son, por ejemplo, memoria, disco y uso de CPU. Estas son métricas físicas, recursos que corresponden a los recursos físicos en el nodo que es necesario a administrar. Las métricas también pueden ser lógicas (y suelen serlo), como "MyWorkQueueDepth", cosas definidas por la aplicación y que corresponden a cierto nivel de consumo de recursos (pero que la aplicación realmente no conoce o no sabe cómo medirlas). La mayoría de las métricas que vemos que usan las personas son métricas lógicas. Las razones de ello son varias, pero la más común es que, en la actualidad, muchos de nuestros clientes escriben sus servicios en código administrado, y desde dentro de una instancia de servicio sin estado o un objeto de réplica de servicio con estado determinado es realmente bastante difícil medir y notificar el consumo de los recursos físicos reales. La complejidad de informar sobre sus propias métricas es también el motivo de que proporcionemos algunas métricas predeterminadas listas para su uso.

## Métricas predeterminadas
Suponga que desea empezar a trabajar y no sabe qué recursos va a consumir o incluso cuáles serán importantes para usted. Por tanto, implementa y crea los servicios sin especificar ninguna métrica. ¡Eso está bien! Nosotros elegiremos algunas métricas por usted. Las métricas predeterminadas que se usarán hoy si no especifica ninguna propia se denominan PrimaryCount, ReplicaCount y Count (algo un poco vago). En la siguiente tabla se muestra la cantidad de carga de cada una de estas métricas que está asociada a cada objeto de servicio:

| Métrica | Carga de instancia sin estado |	Carga secundaria con estado |	Carga principal con estado |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Recuento |	1 |	1 |	1 |

Entonces, con estas métricas predeterminadas, ¿qué se obtiene? Para cargas de trabajo básicas, obtendrá una distribución del trabajo bastante buena. En el ejemplo siguiente, vera lo que sucede cuando se crea un servicio con estado con tres particiones y un tamaño del conjunto de réplicas de destino de tres, así como un único servicio sin estado con un recuento de tres instancias. Obtendrá algo parecido a esto.

![Diseño de clúster con métricas predeterminadas][Image1]

En este ejemplo, se ve lo siguiente:
-	Las réplicas principales para el servicio con estado no están apiladas en un único nodo.
-	Las réplicas de la misma partición no están en el mismo nodo.
-	El número total de réplicas principales y secundarias está bien distribuido en el clúster.
-	La asignación del número total de objetos de servicio (con y sin estado) es uniformemente en cada nodo.

¡Bastante bien!

Esto funciona bien hasta que empieza a pensar en ello: ¿cuál es la probabilidad de que el esquema de creación de particiones elegido tenga como resultado una utilización perfectamente uniforme por todas las particiones con el tiempo? Además, ¿cuál es la probabilidad de que la carga en un servicio dado sea constante con el tiempo, o incluso la misma que ahora? La probabilidad de que todas las réplicas sean equivalentes para cualquier carga de trabajo importante es realmente baja, así que si le interesa sacar el máximo partido de su clúster, lo más probable es que quiera comenzar a examinar las métricas personalizadas.

De manera realista, podría trabajar perfectamente solo con las métricas predeterminadas, pero eso normalmente significa que la utilización de los clústeres es menor que lo que desearía (ya que la generación de informes no es adaptable y asume que todo es equivalente); en el peor de los casos, también puede generar nodos con programación excesiva, lo que produce problemas de rendimiento. Se puede funcionar mejor con métricas personalizadas e informes de carga dinámica, tema que se aborda a continuación.

## Métricas personalizadas
Ya hemos comentado que puede haber métricas físicas y lógicas, y que los usuarios pueden definir sus propias métricas. Estupendo. Pero, ¿cómo? Bueno, es bastante fácil. Simplemente, configure la métrica y la carga inicial predeterminada al crear el servicio; eso es todo. Al crear el servicio, se puede configurar cualquier conjunto de métricas y los valores predeterminados que representan el servicio que se prevé consumir según una instancia de servicio con nombre.

Tenga en cuenta que, al empezar a definir métricas personalizadas, es necesario volver a agregar de forma explícita las métricas predeterminadas si desea usarlas también para equilibrar el servicio. Esto es para que tenga clara la relación entre las métricas predeterminadas y las métricas personalizadas; tal vez le preocupa el consumo de memoria o la profundidad de la cola de trabajo (WorkQueueDepth) más de lo que le interesa la distribución principal.

Suponga que desea configurar un servicio que informará sobre una métrica llamada "Memoria" (además de las métricas predeterminadas). Para la memoria, suponga que ha realizado algunas mediciones básicas y sabe que normalmente una réplica principal de ese servicio ocupa 20 MB de memoria, mientras que las secundarias de ese mismo servicio ocupan 5 MB. Sabe que Memoria es la métrica más importante para administrar el rendimiento de este servicio concreto, pero desea equilibrar las réplicas principales para que la pérdida de algún nodo o dominio defectuoso no use un número de réplicas principales excesivo. Aparte de eso, usará los valores predeterminados.

Esto es lo que haría:

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
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Recuerde: si solo desea utilizar las métricas predeterminadas, no necesita tocar la colección de métricas en absoluto ni hacer nada especial al crear el servicio).

Ahora que ha visto cómo definir sus propias métricas, se cubrirán las distintas propiedades que pueden tener las métricas. Ya las ha visto, pero es el momento de hablar acerca de lo que realmente significan. Hay cuatro propiedades diferentes que una métrica puede tener en la actualidad:

-	Nombre de la métrica: el nombre de la métrica. Se trata de un identificador único para la métrica en el clúster desde la perspectiva de Resource Manager.
- Carga predeterminada: se representa de forma distinta dependiendo de si el servicio es con o sin estado.
  - Para los servicios sin estado, cada métrica tiene una propiedad única que se denomina carga predeterminada
  - Para servicios con estado, usted define
    -	PrimaryDefaultLoad: la cantidad predeterminada de carga que este servicio aplicará para esta métrica como principal.
    -	SecondaryDefaultLoad: la cantidad predeterminada de carga que este servicio aplicará para esta métrica como réplica secundaria.
-	Ponderación: la importancia de la métrica en relación a las demás métricas configuradas para este servicio.

## Carga
Carga es el concepto general de la cantidad de una determinada métrica que alguna instancia del servicio o réplica consume en un nodo específico.

## Carga predeterminada
La carga predeterminada es la cantidad de carga que Cluster Resource Manager debe suponer que cada instancia del servicio o réplica de este servicio consumirán hasta que reciba alguna actualización de las instancias o réplicas del servicio reales. Para los servicios más sencillos, es una definición estática que nunca se actualiza dinámicamente y, por tanto, se utilizará mientras dure el servicio. Esto funciona muy bien para un planeamiento simple de la capacidad, porque es exactamente lo que estamos acostumbrados a hacer: dedicar algunos recursos a ciertas cargas de trabajo, pero la ventaja es que al menos ahora se funciona teniendo en mente los microservicios, en los que los recursos en realidad no se asignan estáticamente a cargas de trabajo concretas y en los que las personas no están en el bucle de toma de decisiones.

Se permite que los servicios con estado especifiquen la carga predeterminada para sus réplicas principales y secundarias; siendo realistas, para muchos servicios estos números son diferentes debido a las diferentes cargas de trabajo ejecutadas por las réplicas principales y secundarias, y puesto que las principales suelen servir las lecturas y escrituras (así como la mayoría de la carga de procesamiento), la carga predeterminada para una réplica principal es mayor que para las secundarias.

Supongamos que ha estado ejecutando el servicio durante un tiempo y nota que algunas instancias o réplicas consumen muchos más recursos que otras, o bien que el consumo varía a lo largo del tiempo; quizás están asociadas a un cliente determinado o quizás solo corresponden a cargas de trabajo que varían a lo largo del día, como el tráfico de mensajería, las llamadas telefónicas o las transacciones de la bolsa. En cualquier caso, ve que no hay un "número único" que pueda usar para la carga sin que se quede fuera una cantidad considerable durante un tiempo como mínimo. También observa que al quedar fuera de su estimación inicial, Cluster Resource Manager asigna demasiados o demasiado pocos recursos a su servicio y, por tanto, tiene nodos infrautilizados o usados en exceso.

¿Qué debe hacer? Bueno, el servicio podría informar acerca de la carga sobre la marcha.

## Carga dinámica
Los informes de carga dinámica permiten a las réplicas o instancias ajustar su asignación o el uso de métricas comunicado en el clúster mientras estén vigentes. Normalmente, una instancia o una réplica del servicio pasiva que no realice ningún trabajo informarán de que utilizan poca cantidad de unas métricas en particular, mientras que las réplicas o instancias ocupadas comunicarán que usan más. Este nivel general de rotación en el clúster permite reorganizar las instancias y réplicas del servicio del clúster sobre la marcha para garantizar que reciben los recursos que necesitan; de hecho, los servicios ocupados pueden reclamar recursos de otras réplicas o instancias actualmente pasivas o con menos trabajo. La generación de informes de carga sobre la marcha puede realizarse mediante el método ReportLoad, disponible en ServicePartition como una propiedad en StatefulService base o StatelessService class a través del modelo de programación de Reliable Services. En su servicio, el código sería similar al siguiente:

Código:

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("metric1", 42) });
```

Las instancias y réplicas del servicio solo comunican la carga de las métricas para las que se han configurado. La lista de métricas se establece al crear cada servicio y se puede actualizar después. Si una instancia o una réplica del servicio intenta informar de la carga para una métrica para la que no está configurada, Service Fabric registra el informe pero lo ignora, lo que significa que no se usará al calcular o informar sobre el estado del clúster. Esto es práctico porque permite una mayor experimentación; el código puede medir e informar sobre todo lo que sabe, y el operador puede configurar, modificar y actualizar las reglas de equilibrio de los recursos para ese servicio sobre la marcha, sin tener que cambiar el código. Esto incluye, por ejemplo, deshabilitar una métrica con un informe de errores, reconfigurar las ponderaciones de métricas según el comportamiento o habilitar una nueva métrica solo después de que el código ya se haya implementado y validado mediante otros mecanismos.

## Combinación de valores de carga predeterminados e informes de carga dinámica
¿Tiene sentido tener una carga predeterminada especificada para un servicio que va a notificar la carga dinámicamente? Por supuesto. En este caso, la carga predeterminada actúa como una estimación hasta que los informes reales empiezan a aparecer en la instancia o la réplica del servicio real. Esto es muy útil, ya que proporciona a Cluster Resource Manager algo con lo que trabajar: la estimación de carga predeterminada permite colocar las instancias o réplicas del servicio en el lugar adecuado desde el principio. Cuando no se proporciona información de carga predeterminada, la ubicación de los servicios se realiza aleatoriamente en el momento de su creación y, si las cargas cambian más adelante, Cluster Resource Manager seguramente deba cambiar las cosas de sitio.

Tome el ejemplo anterior y vea lo que sucede cuando se agrega cierta carga personalizada y cuando después de crear el servicio se actualiza dinámicamente. En este ejemplo, se usará "Memoria" como ejemplo y se supondrá que se ha creado inicialmente el servicio con estado con el comando siguiente:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Ya se habló acerca de esta sintaxis (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), pero se hablará más sobre lo que significa el valor específico para Ponderación más adelante.

El aspecto de un diseño del clúster podría ser similar a este:

![Clúster equilibrado con métricas predeterminadas y personalizadas][Image2]

Algunas cosas que debe tener en cuenta:

-	Puesto que las réplicas o instancias utilizan la carga predeterminada del servicio hasta que informan de su propia carga, se sabe que las réplicas dentro de la partición 1 del servicio con estado no han comunicado su propia carga
-	Las réplicas secundarias dentro de una partición pueden tener su propia carga
-	En general las métricas son bastante buenas, con la diferencia entre la carga máxima y mínima en un nodo (para Memoria, la métrica personalizada que más interesa) de solo un factor de 1,75 (el nodo con mayor carga para la memoria es N3, con la menor es N2 y 28/16 = 1,75); bastante equilibrada.

Hay algunas cosas que es necesario explicar

-	¿Qué determinó si una relación de 1,75 era razonable o no? ¿Cómo se sabe que está bastante bien o si hay más trabajo que hacer?
-	¿Cuándo se produce el equilibrio?
-	¿Qué significa que la ponderación de Memoria es alta?

## Pesos de métrica
Las ponderaciones de métricas son lo que permite que dos servicios diferentes informen de las mismas métricas, pero se vea la importancia de equilibrar esas métricas de manera diferente. Por ejemplo, suponga un motor de análisis en memoria y una base de datos persistente; probablemente ambos se preocupan de la métrica "Memoria", pero el servicio en memoria probablemente no tiene que preocuparse mucho la métrica "Disco", que puede consumir un poco de él, pero no es esencial para el rendimiento del servicio, por lo que probablemente ni siquiera la comunica. Poder realizar un seguimiento de las mismas métricas en diferentes servicios está muy bien, ya que es lo que permite que Cluster Resource Manager realice el seguimiento del consumo real en el clúster, se asegure de que los nodos no exceden su capacidad, etc.

Las ponderaciones de métricas también permiten a Cluster Resource Manager tomar decisiones sobre cómo equilibrar el clúster cuando no hay ninguna respuesta perfecta (lo que supone mucho tiempo). Las métricas pueden tener cuatro niveles diferentes de ponderación: cero, baja, media o alta. Una métrica con una ponderación de cero no aporta nada a la hora de considerar si las cosas están equilibradas o no, pero su carga contribuye a aspectos como la medición de la capacidad.

El verdadero impacto de tener diferentes ponderaciones de métricas en el clúster es que se llega a disposiciones diferentes de los servicios, ya que, en resumen, se ha indicado a Cluster Resource Manager que determinadas métricas son más importantes que otras. Como ya lo sabe, cuando entran en conflicto con otras métricas que tienen distinta ponderación, Cluster Resource Manager puede elegir soluciones que equilibren mejor las métricas mejor ponderadas.

Eche un vistazo a un ejemplo sencillo de algunos informes de carga y cómo ponderaciones de métricas diferentes pueden generar distintas asignaciones en el clúster. En este ejemplo, se puede ver que al cambiar la ponderación relativa de las métricas Resource Manager preferirá determinadas soluciones al crear diferentes disposiciones de los servicios.

![Ejemplo de ponderación de métricas y su impacto en soluciones de equilibrio][Image3]

En este ejemplo hay cuatro servicios diferentes; todos informan de distintos valores para dos métricas diferentes: A y B. En un caso, todos los servicios definen que MetricA es la más importante (ponderación = alta) y MetricB tiene relativamente poca importancia (ponderación = baja); de hecho, se puede ver que Cluster Resource Manager coloca los servicios para que MetricA esté mejor equilibrada (tenga una desviación inferior) que MetricB. En el segundo caso, se invierten las ponderaciones de métricas y se ve que Cluster Resource Manager probablemente intercambiaría los servicios A y B para conseguir una asignación en la que MetricB quede mejor equilibrada que MetricA.

### Ponderaciones de métricas globales
Por tanto, si ServiceA define MetricA como la más importante y a ServiceB no le interesa en absoluto, ¿cuál es la ponderación real que se usará?

En realidad hay dos ponderaciones para realizar el seguimiento de cada métrica: la ponderación que el servicio ha definido y la ponderación media global en todos los servicios que se interesan por esa métrica. Se usan estas dos ponderaciones al calcular las puntuaciones de las soluciones que se generan, ya que es importante asegurarse de que un servicio está equilibrado en relación a sus propias prioridades, pero también que el clúster en conjunto está asignado correctamente.

¿Qué ocurriría si no se ha preocupado por el equilibrio global y local? Bueno, no tiene mucho sentido crear soluciones que estén equilibradas globalmente, pero que tengan un equilibrio y una asignación de recursos muy deficientes para los servicios individuales. En el siguiente ejemplo se considerarán las métricas predeterminadas con las que está configurado un servicio con estado: PrimaryCount, ReplicaCount y Count, y veremos qué sucede cuando solo se tiene en cuenta el equilibrio global:

![El impacto de una única solución global][Image4]

En el ejemplo anterior, en el que solo se analizó el equilibrio global, el clúster en conjunto está equilibrado: todos los nodos tienen el mismo número de réplicas principales y totales. Sin embargo, si examina el impacto real de esta asignación, no es tan bueno: la pérdida de cualquier nodo afecta a una determina carga de trabajo desproporcionadamente, ya que toma todas sus principales. Suponga, por ejemplo, que fuera a perder el primer nodo. Si esto sucediera, se perderían simultáneamente las tres principales para las tres diferentes particiones del servicio Circle. Por el contrario, las particiones de los otros dos servicios (Triangle y Hexagon) perderían una réplica, pero no generarían interrupciones (aparte de tener que recuperar la réplica).

En el ejemplo inferior se han distribuido las réplicas en función del equilibrio global y por servicio. Al calcular la puntuación de la solución, se asigna la mayor parte de la ponderación a la solución global, pero una parte (configurable) va a garantizar que los servicios también están equilibrados entre ellos todo lo posible. Como resultado, si fuera a perder el mismo primer nodo, verá que la pérdida de las principales (y secundarias) se distribuye entre todas las particiones de todos los servicios y el impacto en cada una es el mismo.

Al tener en cuenta las ponderaciones de métricas, el equilibrio global se calcula en función del promedio de las ponderaciones de métricas configuradas para cada servicio. Un servicio se equilibra con respecto a sus propias ponderaciones de métricas.

## Pasos siguientes
- Para más información sobre las otras opciones disponibles para configurar servicios, consulte el tema sobre las demás configuraciones de Cluster Resource Manager disponibles en [Más información sobre la configuración de servicios](service-fabric-cluster-resource-manager-configure-services.md).
- Definir las métricas de desfragmentación es una manera de consolidar la carga en los nodos en lugar de distribuirla. Para saber cómo configurar la desfragmentación, consulte [este artículo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)
- Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- El costo del movimiento es una forma de señalizar al Administrador de recursos de clúster que determinados servicios son más caros de mover que otros. Para obtener más información sobre el costo del movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0824_2016-->