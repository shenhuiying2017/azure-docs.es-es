<properties
   pageTitle="Equilibrio del clúster con el Administrador de recursos de clúster de Service Fabric de Azure"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Equilibrio de su clúster de Service Fabric
El Administrador de recursos de clúster de Service Fabric permite informar de la carga dinámica, reaccionar a los cambios y generar planes de equilibrio. Sin embargo, ¿cuándo se hace esto? ¿Qué desencadena realmente la vuelta al equilibrio en el clúster si los servicios se colocan de acuerdo con sus valores de carga predeterminados cuando se crean? Hay varios controles relacionados con esto.

El primer conjunto de controles que rigen el equilibrio es un conjunto de temporizadores que determinan la frecuencia con que el Administrador de recursos de clúster examina el estado del clúster para ver lo que es necesario solucionar. Estos temporizadores están relacionados con las distintas etapas del trabajo que siempre hacen. Dichos componentes son:

1.	Selección de ubicación: esta fase tiene que ver con la colocación de las réplicas con estado o las instancias sin estado que faltan. Abarca los nuevos servicios y la administración de réplicas o instancias que han dado error y es necesario volver a crear. Aquí también se administra la eliminación de réplicas o instancias.
2.	Comprobaciones de restricciones: en esta etapa se comprueban y corrigen las infracciones (reglas) de las distintas restricciones de selección de ubicación dentro del sistema. Ejemplos de reglas son asegurarse de que los nodos no sobrepasan la capacidad y que las restricciones de selección de ubicación de un servicio se cumplen (más información al respecto más adelante).
3.	Equilibrio: en esta etapa se comprueba si el reequilibrio proactivo es necesario según el nivel de equilibrio deseado configurado para distintas métricas y, en caso afirmativo, se intenta encontrar una disposición en el clúster que sea más equilibrada.

Cada una de estas distintas etapas está controlada por un temporizador diferente que dictamina su frecuencia. Por ejemplo, si solo quiere tratar con la colocación de nuevas cargas de trabajo de servicio en el clúster cada hora (a fin de procesarlas por lote), pero quiere comprobaciones periódicas de equilibrio cada unos cuantos segundos, puede hacerlo. Cada vez que se dispara un temporizador, se establece un indicador que dice que debemos ocuparnos de esa parte de los deberes del Administrador de recursos y se selecciona en la siguiente limpieza general mediante la máquina de estados (por eso estas configuraciones se definen como "intervalos mínimos"). De manera predeterminada, el Administrador de recursos examina su estado y aplica actualizaciones cada décima de segundo, define los indicadores de selección de ubicación y comprobación de restricciones cada segundo y el indicador de equilibrio cada cinco segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

En la actualidad solo realizamos una de estas acciones a la vez, de manera secuencial. Es decir, por ejemplo, ya hemos respondido a las solicitudes para crear nuevas réplicas antes de continuar con el equilibrio del clúster. Como puede ver por los intervalos de tiempo especificados, podemos examinar y comprobar cualquier cosa que necesitemos hacer con mucha frecuencia, lo que significa que cada conjunto de cambios es habitualmente más pequeño (no examinamos horas de cambios en el clúster e intentamos corregirlos todos a la vez, sino que podemos hacer frente a las cosas con efectividad conforme van sucediendo). Esto hace que el Administrador de recursos de Service Fabric tenga una gran capacidad de respuesta a todo lo que sucede en el clúster.

Fundamentalmente, el Administrador de recursos de clúster también debe saber cuándo considerar que el clúster está desequilibrado y qué réplicas se deben mover para solucionar las cosas. Para ello contamos con dos elementos de configuración principales: umbrales de equilibrio y umbrales de actividad.

## Umbrales de equilibrio
Un umbral de equilibrio es el control principal para la activación del reequilibrio proactivo. El umbral de equilibrio define cómo de desequilibrado debe estar el clúster con respecto a una métrica determinada para que el Administrador de recursos considere que está desequilibrado y active el equilibrio durante la próxima ejecución. Los umbrales de equilibrio se definen por métrica como parte del manifiesto de clúster:

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

El umbral de equilibrio de una métrica es una proporción. Si la cantidad de carga en el nodo más cargado dividida entre la cantidad de carga en el nodo menos cargado supera esta proporción, se considera que el clúster está desequilibrado y se activa el equilibrio durante la próxima ejecución del nodo de estado del Administrador de recursos.

![Ejemplo de umbral de equilibrio][Image1]

En este sencillo ejemplo cada servicio solo consume una unidad de algunos métrica. En el ejemplo superior, la carga máxima en un nodo es 5 y la mínima es 2. Supongamos que el umbral de equilibrio de esta métrica es 3. Por lo tanto, en el ejemplo superior, se considera que el clúster está equilibrado y no se activará ningún equilibrio. En el ejemplo de la parte inferior, la carga máxima en un nodo es 10, mientras que el valor mínimo es 2, lo que nos coloca por encima del umbral de equilibrio diseñado de 3. Como resultado, casi seguramente la carga se distribuirá al Nodo3 una vez que el Administrador de recursos tiene la oportunidad de ejecutarse. Tenga en cuenta que dado que no usamos un enfoque expansivo, parte podría llegar también al Nodo2, lo que daría lugar a la reducción de las diferencias globales entre los nodos.

![Acciones de ejemplo de umbral de equilibrio][Image2]

Observe que el objetivo explícito no es estar por debajo del umbral de equilibrio; los umbrales de equilibrio son solo el desencadenador. Aunque los nodos están relativamente desequilibrados, la cantidad total de carga en el clúster es baja. Esto puede ser debido simplemente a la hora del día o porque el clúster es nuevo y se acaba de arrancar. En cualquier caso, es posible que no quiera pasarse el tiempo equilibrando ya que tampoco hay mucho que ganar; gastará recursos de red y de procesos para que las cosas funcionen. Existe otro control dentro del Administrador de recursos, conocido como Umbral de actividad, que le permite especificar algún límite inferior para la actividad: si ningún nodo tiene al menos esta carga, el equilibrio no se desencadenará incluso si se cumple el umbral de equilibrio. Como ejemplo, supongamos que tenemos informes con los siguientes totales de consumo en estos nodos. Supongamos también que conservamos nuestro umbral de equilibrio de 3, pero ahora también tenemos un umbral de actividad de 1536. En el primer caso, aunque el clúster está desequilibrado según el umbral de equilibrio, ningún nodo satisface el umbral mínimo de actividad, por lo que dejamos que las cosas sigan su curso. En el ejemplo de abajo, el Nodo1 supera con creces el umbral de actividad, así que no se realizará ningún equilibrio.

![Ejemplo de umbral de actividad][Image3]

Al igual que los umbrales de equilibrio, los umbrales de actividad se definen por métrica a través del manifiesto de clúster:

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## Equilibrio conjunto de los servicios
Algo interesante que se debe advertir es que tanto si el clúster está desequilibrado como si no, se trata de una decisión que engloba al clúster entero, pero la forma en que vamos a solucionarlo es moviendo las réplicas e instancias de servicio individuales. Tiene sentido, ¿no? Si la memoria se acumula en un nodo, varias réplicas o instancias podrían estar contribuyendo a ello, así que puede que sea necesario mover todas las réplicas e instancias que usan la métrica desequilibrada afectada.

En ocasiones, un cliente nos llamará o presentará un resguardo diciendo que un servicio que no estaba desequilibrado se ha movido. ¿Cómo podría pasar esto si todas las métricas del servicio estaban equilibradas, además perfectamente, en el momento del otro desequilibrio? ¡Veamos!

Tomemos, por ejemplo, cuatro servicios, S1, S2, S3 y S4. Los informes S1 contra las métricas M1 y M2, los informes S2 contra las métricas M2 y M3, los informes S3 contra las métricas M3 y M4 y los informes S4 contra la métrica M99. Seguramente puede ver adónde queremos llegar. ¡Tenemos una cadena! Desde la perspectiva del Administrador de recursos, en realidad no tenemos cuatro servicios independientes, sino un grupo de servicios que están relacionados (S1, S2 y S3) y uno que va por su cuenta.

![Equilibrio conjunto de los servicios][Image4]

Por lo tanto, es posible que un desequilibrio en la Métrica1 haga que se muevan las réplicas o instancias que pertenecen al Servicio3. Por lo habitual, estos movimientos son bastante limitados, pero pueden ser más grandes en función de cómo de desequilibrada esté la Métrica1 y qué cambios hayan sido necesarios en el clúster para corregirla. También podemos decir con certeza que un desequilibrio en las Métricas 1, 2 o 3 nunca producirá movimientos en el Servicio4: no tendría sentido, dado que el hecho de mover las réplicas o las instancias que pertenecen al Servicio4 no tiene absolutamente ningún efecto sobre el equilibrio de las Métricas 1, 2 o 3.

El Administrador de recursos calcula automáticamente qué servicios están relacionados cada vez que se ejecuta, puesto que se pueden haber agregado o eliminado servicios o haberse cambiado su configuración de métricas: por ejemplo, puede que entre dos ejecuciones de equilibrio el Servicio2 se haya reconfigurado para eliminar la Métrica2. Esto rompe la cadena entre el Servicio1 y el Servicio2. Ahora en lugar de dos grupos de servicios, tiene tres:

![Equilibrio conjunto de los servicios][Image5]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Más información sobre las métricas](service-fabric-cluster-resource-manager-metrics.md)
- [Más información sobre las limitaciones del Administrador de recursos](service-fabric-cluster-resource-manager-advanced-throttling.md)
- [Más información sobre los costos del movimiento de servicios](service-fabric-cluster-resource-manager-movement-cost.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0309_2016-->