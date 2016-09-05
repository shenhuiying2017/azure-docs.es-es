<properties
   pageTitle="Equilibrio del clúster con el Administrador de recursos de clúster de Service Fabric de Azure | Microsoft Azure"
   description="Una introducción al equilibrio del clúster con el Administrador de recursos de clúster de Service Fabric"
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

# Equilibrio del clúster de Service Fabric
Cluster Resource Manager de Service Fabric permite crear informes sobre la carga dinámica, reaccionar a los cambios en el clúster, corregir las infracciones de restricciones y volver a equilibrar el clúster si es necesario. Pero, ¿con qué frecuencia se hace esto y qué lo desencadena? Hay varios controles relacionados con esto.

El primer conjunto de controles en torno al equilibrio es un conjunto de temporizadores. Estos temporizadores controlan la frecuencia con la que Cluster Resource Manager examina el estado del clúster para ver qué cosas hay que solucionar. Hay tres categorías de trabajo, cada uno con su propio temporizador correspondiente. Son las siguientes:

1.	Selección de ubicación: esta fase tiene que ver con la colocación de las réplicas con estado o las instancias sin estado que faltan. Abarca los nuevos servicios y la administración de réplicas con estado o instancias sin estado que han dado error y es necesario volver a crear. Aquí también se administra la eliminación de réplicas o instancias.
2.	Comprobaciones de restricciones: en esta etapa se comprueban y corrigen las infracciones (reglas) de las distintas restricciones de selección de ubicación dentro del sistema. Ejemplos de reglas son asegurarse de que los nodos no sobrepasan la capacidad y que las restricciones de selección de ubicación de un servicio se cumplen (más información al respecto más adelante).
3.	Equilibrio: en esta etapa se comprueba si el reequilibrio proactivo es necesario según el nivel de equilibrio deseado configurado para distintas métricas y, en caso afirmativo, se intenta encontrar una disposición en el clúster que sea más equilibrada.

## Configuración de pasos y temporizadores del Administrador de recursos de clúster
Cada uno de estos tipos diferentes de correcciones que se pueden realizar con Cluster Resource Manager se controla mediante un temporizador diferente que determina su frecuencia. Por ejemplo, si solo quiere tratar con la colocación de nuevas cargas de trabajo de servicio en el clúster cada hora (a fin de procesarlas por lotes), pero quiere comprobaciones periódicas de equilibrio cada unos cuantos segundos, puede configurar ese comportamiento. Cuando se desencadena cada temporizador, se programa la tarea. De forma predeterminada, Resource Manager examina su estado y aplica actualizaciones (procesando por lotes todos los cambios que se han producido desde el último examen, como advertir que un nodo está inactivo) cada décima de segundo, establece las marcas de comprobación de ubicación y restricción cada segundo y la marca de equilibrio cada cinco segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Ahora vamos a realizar todas estas acciones, de forma secuencial (es por eso que hacemos referencias a estas configuraciones como "intervalos mínimos”). Es decir, por ejemplo, ya hemos respondido a las solicitudes pendientes para crear nuevas réplicas antes de continuar con el equilibrio del clúster. Como puede ver por los intervalos de tiempo especificados, podemos examinar y comprobar cualquier cosa que necesitemos hacer con mucha frecuencia, de modo que el conjunto de cambios que realizamos al final de cada paso es habitualmente más pequeño: no examinamos horas de cambios en el clúster ni intentamos corregirlos todos a la vez, sino que tratamos de hacer frente a las cosas más o menos conforme van sucediendo, recurriendo al procesamiento por lotes cuando ocurren muchas cosas a la vez. Esto hace que el Administrador de recursos de Service Fabric tenga una gran capacidad de respuesta a todo lo que sucede en el clúster.

Aunque la mayor parte de estas tareas son sencillas (si hay infracciones de restricción, corríjalas, si hay servicios que es necesario crear, créelos), Cluster Resource Manager también necesita información adicional para determinar si el clúster está desequilibrado. Para ello contamos con dos elementos de configuración: *umbrales de equilibrio* y *umbrales de actividad*.

## Umbrales de equilibrio
Un umbral de equilibrio es el control principal para desencadenar el reequilibrio proactivo (recuerde que el temporizador es solo para determinar la frecuencia con la que Cluster Resource Manager debe realizar las comprobaciones, no significa que nada vaya a ocurrir). El umbral de equilibrio define cómo de desequilibrado debe estar el clúster con respecto a una métrica determinada para que Cluster Resource Manager considere que está desequilibrado y desencadene el equilibrio.

Los umbrales de equilibrio se definen por métrica como parte de la definición de clúster. Para más información sobre las métricas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

El umbral de equilibrio de una métrica es una proporción. Si la cantidad de carga en el nodo más cargado dividida entre la cantidad de carga en el nodo menos cargado supera este número, se considera que el clúster está desequilibrado y la próxima vez que Cluster Resource Manager realice las comprobaciones, se desencadenará el equilibrio (de forma predeterminada, cada cinco segundos, según se determina en MinLoadBalancingInterval, mostrado anteriormente).

![Ejemplo de umbral de equilibrio][Image1]

En este sencillo ejemplo cada servicio solo consume una unidad de alguna métrica. En el ejemplo superior, la carga máxima en un nodo es 5 y la mínima es 2. Supongamos que el umbral de equilibrio de esta métrica es 3. Por lo tanto, en el ejemplo superior, el clúster se considera equilibrado y no se desencadenará el equilibrio cuando Cluster Resource Manager realice las comprobaciones (ya que la proporción del clúster es de 5/2 = 2,5, que es inferior al umbral de equilibrio especificado de 3).

En el ejemplo de la parte inferior, la carga máxima en un nodo es 10, mientras que el valor mínimo es 2, lo que da lugar a una proporción de 5. Este valor sitúa al clúster en el umbral de equilibrio diseñado de 3 para esa métrica. Como resultado, la próxima vez que el temporizador de equilibrio se active, se programará un reequilibrio global. Tenga en cuenta que solo porque la búsqueda de equilibrio se haya iniciado no significa que vaya a pasar nada; en ocasiones, el clúster está desequilibrado pero la situación no se puede mejorar. Pero en una situación como esta (al menos de forma predeterminada) casi seguro que alguna carga se distribuirá al Nodo3. Tenga en cuenta que dado que no usamos un enfoque expansivo, parte podría distribuirse también al Nodo2, lo que daría lugar a la reducción de las diferencias globales entre los nodos, si bien es de esperar que la mayoría de la carga vaya al Nodo3.

![Acciones de ejemplo de umbral de equilibrio][Image2]

Tenga en cuenta que el objetivo explícito no es estar por debajo del umbral de equilibrio; los umbrales de equilibrio son solo el *desencadenador* que indica a Cluster Resource Manager que examine el clúster para determinar qué mejoras puede realizar, si es que las hay.

## Umbrales de actividad
En ocasiones, aunque los nodos están relativamente desequilibrados, la cantidad de carga *total* en el clúster es baja. Esto puede ser debido simplemente a la hora del día o porque el clúster es nuevo y se acaba de arrancar. En cualquier caso, es posible que no quiera pasarse el tiempo equilibrando el clúster ya que tampoco hay mucho que ganar; gastará recursos de red y de proceso, pero nada cambiará. Para que no tenga que hacer esto, existe otro control dentro de Resource Manager, conocido como umbrales de actividad, que le permite especificar algún límite inferior absoluto para la actividad: si ningún nodo tiene al menos esta carga, el equilibrio no se desencadenará incluso si se cumple el umbral de equilibrio.

Como ejemplo, supongamos que tenemos informes con los siguientes totales de consumo en estos nodos. Supongamos también que conservamos nuestro umbral de equilibrio de 3 para esta métrica, pero ahora también tenemos un umbral de actividad de 1536. En el primer caso, aunque el clúster está desequilibrado según el umbral de equilibrio, ningún nodo satisface el umbral mínimo de actividad, por lo que dejamos que las cosas sigan su curso. En el ejemplo de abajo, el Nodo1 está por encima del umbral de actividad, por lo que se realizará el equilibrio (ya que se superan el umbral de equilibrio y el umbral de actividad de la métrica)

![Ejemplo de umbral de actividad][Image3]

Al igual que los umbrales de equilibrio, los umbrales de actividad se definen por métrica a través de la definición de clúster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Observe que los umbrales de equilibrio y actividad están vinculados a la métrica; el equilibrio solo se desencadenará si se superan los umbrales de equilibrio y actividad de la misma métrica. Por lo tanto, si se superan el umbral de equilibrio de la memoria y el umbral de actividad de la CPU, el equilibrio no se activará siempre que no se superan los umbrales restantes (umbral de equilibrio de la CPU y umbral de actividad de la memoria).

## Equilibrio conjunto de los servicios
Algo interesante que se debe advertir es que tanto si el clúster está desequilibrado como si no, se trata de una decisión que engloba al clúster entero, pero la forma en que vamos a solucionarlo es moviendo las réplicas e instancias de servicio individuales. Tiene sentido, ¿no? Si la memoria se acumula en un nodo, varias réplicas o instancias podrían estar contribuyendo a ello, así que puede que sea necesario mover todas las réplicas con estado o instancias sin estado que usan la métrica desequilibrada afectada.

En ocasiones, un cliente nos llamará o presentará un resguardo diciendo que un servicio que no estaba desequilibrado se ha movido. ¿Cómo podría pasar esto si todas las métricas del servicio estaban equilibradas, además perfectamente, en el momento del otro desequilibrio? ¡Veamos!

Tomemos, por ejemplo, cuatro servicios, Servicio1, Servicio2, Servicio3 y Servicio4. Servicio1 informa de la Métrica1 y la Métrica2, Servicio2 de la Métrica2 y la Métrica3, Servicio3 de la Métrica3 y la Métrica4 y Servicio4 de algunos valores de la Métrica99. Seguramente puede ver adónde queremos llegar. ¡Tenemos una cadena! Desde la perspectiva de Cluster Resource Manager, en realidad no tenemos cuatro servicios independientes, sino un grupo de servicios que están relacionados (Servicio1, Servicio2 y Servicio3) y uno que va por su cuenta.

![Equilibrio conjunto de los servicios][Image4]

Por lo tanto, es posible que un desequilibrio en la Métrica1 haga que se muevan las réplicas o instancias que pertenecen al Servicio3. Por lo habitual, estos movimientos son bastante limitados, pero pueden ser más grandes en función de cómo de desequilibrada esté la Métrica1 y qué cambios hayan sido necesarios en el clúster para corregirla. También podemos decir con certeza que un desequilibrio en las Métricas 1, 2 o 3 nunca producirá movimientos en el Servicio4: no tendría sentido, dado que el hecho de mover las réplicas o las instancias que pertenecen al Servicio4 no tiene absolutamente ningún efecto sobre el equilibrio de las Métricas 1, 2 o 3.

Cluster Resource Manager calcula automáticamente qué servicios están relacionados, puesto que se pueden haber agregado o eliminado servicios o haberse cambiado su configuración de métricas: por ejemplo, puede que entre dos ejecuciones de equilibrio el Servicio2 se haya reconfigurado para eliminar la Métrica2. Esto rompe la cadena entre el Servicio1 y el Servicio2. Ahora en lugar de dos grupos de servicios, tiene tres:

![Equilibrio conjunto de los servicios][Image5]

## Pasos siguientes
- Las métricas son el modo en que el Administrador de recursos de clúster de Service Fabric administra la capacidad y el consumo en el clúster. Para obtener más información sobre ellas y cómo configurarlas, consulte [este artículo](service-fabric-cluster-resource-manager-metrics.md).
- El costo del movimiento es una forma de señalizar al Administrador de recursos de clúster que determinados servicios son más caros de mover que otros. Para obtener más información sobre el costo del movimiento, consulte [este artículo](service-fabric-cluster-resource-manager-movement-cost.md).
- El Administrador de recursos de clúster presenta varias limitaciones que se pueden configurar para ralentizar la renovación del clúster. Aunque no son normalmente necesarias, si las necesita, puede encontrar información sobre ellas [aquí](service-fabric-cluster-resource-manager-advanced-throttling.md).


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0824_2016-->