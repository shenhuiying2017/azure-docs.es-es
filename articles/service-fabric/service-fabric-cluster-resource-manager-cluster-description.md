---
title: "Descripción del clúster del equilibrador de recursos | Microsoft Docs"
description: "Descripción de un clúster de Service Fabric mediante la especificación de dominios de error, dominios de actualización, propiedades de nodo y capacidades de nodo en el Administrador de recursos de clúster."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 62000440292dd12f5744bbc8ec558cd92d8c6235


---
# <a name="describing-a-service-fabric-cluster"></a>Descripción de un clúster de Service Fabric
Service Fabric Cluster Resource Manager proporciona varios mecanismos para describir un clúster. Durante el tiempo de ejecución, Cluster Resource Manager usa esta información para garantizar la alta disponibilidad de los servicios que se ejecutan en el clúster y para asegurarse de que los recursos del clúster se estén usando de la forma adecuada.

## <a name="key-concepts"></a>Conceptos clave
Cluster Resource Manager es compatible con varias de las características que describen un clúster:

* Dominios de error
* Dominios de actualización
* Propiedades del nodo
* Capacidades de nodo

## <a name="fault-domains"></a>Dominios de error
Un dominio de error es cualquier área de error coordinado. Una única máquina constituye un dominio de error (ya que ella sola puede dejar de funcionar por muchas razones diferentes, desde errores en el sistema de alimentación hasta unidades averiadas o un firmware de NIC defectuoso). Varias máquinas conectadas al mismo conmutador Ethernet se encuentran en el mismo dominio de error, al igual que aquellas que estén conectadas a una sola fuente de energía. Ya que es natural que estas se superpongan, los dominios de error son intrínsecamente jerárquicos y se representan como identificadores URI de Service Fabric.

Si estuviera configurando su propio clúster, debería tener en cuenta todas estas diferentes áreas de error y asegurarse de que los dominios de error estuvieran configurados correctamente para que Service Fabric supiera dónde es seguro colocar servicios. En realidad, aquí "seguro" quiere decir inteligente; no conviene colocar servicios de tal forma que la pérdida de un dominio de error (el fallo de cualquiera de los componentes que aparecen listados anteriormente, por ejemplo) haga que el servicio deje de funcionar.  En el entorno de Azure, aprovechamos la información sobre dominios de error proporcionada por el entorno para configurar correctamente los nodos del clúster en su nombre.
En el gráfico siguiente (figura 7), como ejemplo sencillo, aparecen coloreadas todas las entidades que dan lugar de forma razonable a un dominio de error, así como todos los distintos dominios de error resultantes. En este ejemplo, tenemos centros de datos (DC), bastidores (R) y servidores blade (B). Posiblemente, si cada servidor blade contiene más de una máquina virtual, podría haber otra capa en la jerarquía de dominios de error.

![Nodos organizados a través de dominios de error][Image1]

 Durante el tiempo de ejecución, Service Fabric Cluster Resource Manager tiene en cuenta los dominios de error del clúster e intenta distribuir las réplicas de un servicio determinado de forma que se encuentren en dominios de error independientes. Este proceso ayuda a garantizar que, en caso de error en cualquiera de los dominios de error (a cualquier nivel en la jerarquía), la disponibilidad de ese servicio no peligre.

 En el caso del Administrador de recursos del clúster de Service Fabric, realmente no es relevante el número de capas de la jerarquía. Pero, dado que intenta garantizar que la pérdida de parte de la jerarquía no afecte negativamente al clúster ni a los servicios que en él se ejecutan, por lo general es preferible que cada nivel de profundidad del dominio de error cuente con el mismo número de máquinas. Esto impide que una parte de la jerarquía se vea obligada a contener más servicios que otras.

 Si se configura el clúster de manera que el "árbol" de dominios de error esté desequilibrado, es más difícil para Cluster Resource Manager saber cuál es la mejor asignación de réplicas, sobre todo porque significa que la pérdida de un dominio determinado puede afectar excesivamente a la disponibilidad del clúster. Cluster Resource Manager está dividido entre utilizar eficazmente las máquinas del dominio "pesado" colocando servicios en ellas y colocar los servicios de forma que la pérdida del dominio no cause problemas.

 En el diagrama siguiente, se muestran dos ejemplos diferentes diseño de clústeres, uno donde los nodos están bien distribuidos entre los dominios de error y otro donde un dominio de error termina con muchos más nodos.  Tenga en cuenta que en Azure las decisiones sobre qué nodos terminan en qué dominios de error y de actualización se toman de forma automática, por lo que no debería ver nunca este tipo de desequilibrio. Sin embargo, si alguna vez configura su propio clúster local o en otro entorno, es algo que debe considerar.

 ![Dos diseños de clúster distintos][Image2]

## <a name="upgrade-domains"></a>Dominios de actualización
Los dominios de actualización son otra característica que ayuda a Service Fabric Resource Manager a comprender el diseño del clúster para poder prever errores con antelación. Los dominios de actualización definen áreas (en realidad, conjuntos de nodos) que dejarán de funcionar a la vez durante una actualización.

Los dominios de actualización son muy similares a los dominios de error, pero con algunas diferencias clave. En primer lugar, los dominios de actualización se definen normalmente mediante una directiva, mientras que los dominios de error se definen rigurosamente en función de las áreas de errores coordinados (y, por lo tanto, normalmente en función del diseño de hardware del entorno). En cambio, en el caso de los dominios de actualización, es posible decidir cuántos se quieren. Otra diferencia es que los dominios de actualización (al menos en la actualidad) no son jerárquicos, sino que son más una simple etiqueta que una jerarquía.

En la imagen siguiente se muestra una configuración ficticia con tres dominios de actualización divididos entre tres dominios de error. También se muestra una colocación posible de tres réplicas diferentes de un servicio con estado. Observe que se encuentran en diferentes dominios de error y de actualización. Esto significa que se podría perder un dominio de error mientras se esté realizando una actualización de servicio y aún existiría una copia en ejecución del código y los datos en el clúster. Según sus necesidades esto podría ser suficiente, pero quizá se haya dado cuenta de que esta copia podría ser antigua (dado que Service Fabric usa la replicación basada en el quórum). Para poder sobrevivir realmente a dos errores, se necesitarían más réplicas (cinco como mínimo).

![Colocación con dominios de error y de actualización][Image3]

Existen ventajas y desventajas de tener grandes números de dominios de actualización: como ventaja, cada paso de la actualización es más granular y, por tanto, afecta a un número menor de nodos o servicios. Como consecuencia, hay que mover menos servicios a la vez, lo que reduce la actividad en el sistema y mejora la confiabilidad general (ya que los problemas afectarán a una proporción menor del servicio). La desventaja de tener muchos dominios de actualización es que Service Fabric comprueba el estado de cada uno de ellos a medida que se actualiza y se asegura de que sea bueno antes de pasar al siguiente. El objetivo de esta comprobación es asegurarse de que los servicios tengan la oportunidad de estabilizarse y de que su estado se valide antes de continuar con la actualización, para que se detecten los posibles problemas. El compromiso es aceptable, ya que impide que los cambios incorrectos afecten a una proporción excesiva del servicio a la vez.

El uso de muy pocos dominios de actualización también tiene efectos secundarios: mientras cada dominio de actualización individual esté sin funcionar y actualizándose, una gran parte de la capacidad general no estará disponible. Por ejemplo, si solo dispone de tres dominios de actualización, estará dejando sin funcionar alrededor de un tercio de la capacidad general del servicio o del clúster al mismo tiempo. Esto no es deseable ya que debe contar con capacidad suficiente en el resto del clúster para cubrir la carga de trabajo, lo que significa que, en una situación normal, esos nodos están menos cargados de lo posible, lo que incrementa el COGS.

No existe ningún límite real en el número total de dominios de error o de actualización en un entorno ni tampoco restricciones a la forma en que se superponen. Algunas estructuras habituales son 1:1 (donde cada dominio de error único se asigna a su propio dominio de actualización), un dominio de actualización por nodo (instancia de sistema operativo físico o virtual) y un modelo de "bandas" o "matriz" donde los dominios de error y los de actualización forman una matriz en que las máquinas suelen seguir la diagonal.

![Diseños de dominio de error y de actualización][Image4]

No existe un diseño ideal, sino que cada uno tiene sus ventajas e inconvenientes. Por ejemplo, el modelo 1FD:1UD es bastante fácil de configurar, mientras que el modelo de 1 UD (dominio de actualización) por nodo es más parecido a lo que los usuarios están acostumbrados por haber administrado antes conjuntos pequeños de máquinas en los que cada una se pondría fuera de servicio de forma independiente.

El modelo más habitual (y el que se usa para los clústeres de Service Fabric de Azure hospedados) es la matriz FD/UD, en la que los FD (dominios de error) y los UD forman una tabla donde los nodos se colocan empezando por la diagonal. El hecho de que termine siendo un diseño ralo o denso depende del número total de nodos en comparación con el número de dominios de error y dominios de actualización (es decir, si los clústeres son lo bastante grandes, prácticamente cualquier diseño acaba adquiriendo el aspecto de un patrón de matriz densa, como se muestra en la opción abajo a la derecha en la Figura 10).

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restricciones de dominio de error y de actualización y el comportamiento resultante
Cluster Resource Manager trata el deseo de mantener un servicio en equilibrio entre los dominios de error y de actualización como una restricción. Para más información sobre las restricciones, vea [este artículo](service-fabric-cluster-resource-manager-management-integration.md). Las restricciones de dominios de error y de actualización se definen de esta forma: "para una partición de servicio específica, nunca tiene que haber una diferencia *mayor que uno* en el número de réplicas entre dos dominios".  En la práctica esto significa que para un servicio determinado, ciertos movimientos o determinadas disposiciones pueden no ser válidos en el clúster, porque si lo fueran podría infringir las restricciones de dominio de error o de actualización.

Vamos a ver un ejemplo. Supongamos que tenemos un clúster con 6 nodos, configurado con 5 dominios de error (FD) y 5 dominios de actualización (UD).

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| UD0 |N1 | | | | |
| UD1 |N6 |N2 | | | |
| UD2 | | |N3 | | |
| UD3 | | | |N4 | |
| UD4 | | | | |N5 |

Ahora supongamos que creamos un servicio con un valor TargetReplicaSetSize de 5. Las réplicas se dirigen a N1-N5. De hecho, N6 no se usará nunca. Pero ¿por qué? Vamos a echar un vistazo a la diferencia entre el diseño actual y lo que sucedería si hubiéramos elegido N6 en su lugar, vamos a ver cómo esto se relaciona con nuestra definición de la restricción de dominio de error (FD) y dominio de actualización (UD).

Este es el diseño que obtuvimos y el número total de las réplicas por dominio de error y de actualización.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 | |R2 | | | |1 |
| UD2 | | |R3 | | |1 |
| UD3 | | | |R4 | |1 |
| UD4 | | | | |R5 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Observe que este diseño se equilibra en términos de nodos por dominio de error y dominio de actualización, y que también está equilibrado en cuanto al número de réplicas por dominio de error y de actualización. Cada dominio tiene el mismo número de nodos y el mismo número de réplicas.

Ahora, echemos un vistazo a lo que sucedería si hubiéramos utilizado N6 en lugar de N2. ¿Cómo se distribuirían las réplicas entonces? Tendrían un aspecto similar al siguiente:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 |R1 | | | | |1 |
| UD1 |R5 | | | | |1 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |2 |0 |1 |1 |1 |- |

Esto infringe nuestra definición de la restricción de dominio de error, ya que FD0 tiene 2 réplicas mientras FD1 tiene 0, lo que hace que la diferencia total sea 2 y, por tanto, Cluster Resource Manager no permitirá esta disposición. De forma similar si hubiéramos seleccionado N2-6 obtendríamos:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| UD0 | | | | | |0 |
| UD1 |R5 |R1 | | | |2 |
| UD2 | | |R2 | | |1 |
| UD3 | | | |R3 | |1 |
| UD4 | | | | |R4 |1 |
| FDTotal |1 |1 |1 |1 |1 |- |

Esto aunque ofrece equilibrio en términos de dominios de error, infringe la restricción de dominio de actualización (ya que UD0 tiene 0 réplicas mientras UD1 tiene 2) y, por tanto, tampoco es válido.

## <a name="configuring-fault-and-upgrade-domains"></a>Configuración de dominios de error y de actualización
La definición de dominios de error y de actualización se realiza automáticamente en las implementaciones de Service Fabric hospedado en Azure; Service Fabric se limita a recopilar la información sobre el entorno de Azure. En Azure, la información sobre dominios de error y de actualización parece "de un solo nivel" pero realmente encapsula datos de capas inferiores de Azure Stack y simplemente presenta los dominios lógicos de error y de actualización desde la perspectiva del usuario.

Si está configurando su propio clúster (o si simplemente desea probar una determinada topología en la máquina de desarrollo), debe proporcionar la información sobre dominios de error y de actualización personalmente. En este ejemplo, se define un clúster de desarrollo local de 9 nodos que abarca tres "centros de datos" (cada uno con tres bastidores) y tres dominios de actualización divididos entre esos tres centros de datos. En la plantilla del manifiesto de clúster, el aspecto será similar al siguiente:

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```
> [!NOTE]
> En las implementaciones de Azure, los dominios de error y los de actualización son asignados por Azure. Por lo tanto, la definición de los nodos y roles dentro de la opción de infraestructura de Azure no incluye la información del dominio de errores ni de actualizaciones.
> 
> 

## <a name="placement-constraints-and-node-properties"></a>Restricciones de colocación y propiedades de nodo
A veces (de hecho, la mayor parte del tiempo) le va a interesar asegurarse de que ciertas cargas de trabajo solo se ejecuten en determinados nodos o conjuntos de nodos en el clúster. Por ejemplo, algunas cargas de trabajo pueden requerir GPU o SSD, al contrario que otras. Un buen ejemplo de esto es prácticamente cualquier arquitectura de n niveles, en la que determinadas máquinas sirven de front-end o lado de la aplicación que sirve a la interfaz (y por tanto están expuestas a Internet), mientras que otro conjunto (a menudo con recursos de hardware diferentes) se encarga del trabajo de las capas de proceso o almacenamiento (y generalmente no están expuestas a Internet). Service Fabric espera que, incluso en un mundo de microservicios, haya casos en que se deban ejecutar cargas de trabajo concretas en configuraciones de hardware concretas, por ejemplo:

* una aplicación de n niveles existente se ha transferido "tal cual" a un entorno de Service Fabric;
* se prefiere ejecutar una carga de trabajo en un hardware específico por motivos de rendimiento, escala o aislamiento de seguridad;
* una carga de trabajo debe estar aislada de otras por una directiva o a causa del consumo de recursos.

Para admitir estos tipos de configuraciones, Service Fabric parte de una noción de primera clase de lo que llamamos restricciones de colocación. Se pueden usar restricciones de selección de ubicación para indicar dónde se deben ejecutar determinados servicios. Los usuarios pueden extender el conjunto de restricciones, lo que significa que se pueden etiquetar nodos con propiedades personalizadas y después seleccionarlas también.

![Diferentes cargas de trabajo por diseño de clúster][Image5]

Las distintas etiquetas de clave-valor en los nodos se conocen como *propiedades* de selección de ubicación del nodo (o simplemente propiedades de nodo), mientras que la instrucción en el servicio se denomina *restricción* de selección de ubicación. El valor especificado en la propiedad de nodo puede ser una cadena, un booleano o de tipo Long con signo. La restricción puede ser cualquier instrucción booleana que opera en las diferentes propiedades del nodo en el clúster. Los selectores válidos en estas instrucciones booleanas (que son cadenas) son:

* comprobaciones condicionales para crear instrucciones concretas
  * "igual a" ==
  * "mayor que" >
  * "menor que" <
  * "no igual a" !=
  * "mayor que o igual a" >=
  * "menor que o igual a" <=
* instrucciones booleanas para la agrupación y la negación
  * "y" &&
  * "o" ||
  * "no" !
* paréntesis para operaciones de agrupamiento
  
  * ()
  
  Estos son algunos ejemplos de instrucciones de restricción básicas que utilizan algunos de los símbolos anteriores. Tenga en cuenta que las propiedades de nodo pueden ser cadenas, bools o valores numéricos.   
  
  * "Foo > = 5"
  * "NodeColor! = verde"
  * "((OneProperty < 100) || ((AnotherProperty == falso) & & (OneProperty > = 100))) "

El servicio se puede colocar solo en los nodos donde la instrucción general se evalúa como "True". Los nodos que no tengan definida ninguna propiedad no coinciden con ninguna restricción de colocación que contenga esa propiedad.

Además, Service Fabric define algunas propiedades predeterminadas que se pueden usar automáticamente sin que el usuario tenga que definirlas. En el momento de escribir este artículo, las propiedades predeterminadas definidas en cada nodo eran NodeType y NodeName. Por ejemplo podría escribir una restricción de selección de ubicación como "(NodeType == NodeType03)". Por lo general, NodeType parece ser una de las propiedades más usadas, ya que se suele corresponder 1:1 con un tipo de una máquina, que a su vez se corresponde con un tipo de carga de trabajo en una arquitectura de aplicación tradicional de n niveles.

![Restricciones de colocación y propiedades de nodo][Image6]

Supongamos que se definieron las siguientes propiedades de nodo para un tipo de nodo determinado: ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Puede crear restricciones de colocación de servicio para un servicio de forma parecida a esta:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Si está seguro de que todos los nodos de NodeType01 son válidos, también puede seleccionar simplemente ese tipo de nodo, mediante restricciones de colocación como las que se muestran en las imágenes anteriores.

Una de las cosas interesantes sobre las restricciones de colocación de un servicio es que se pueden actualizar dinámicamente durante el tiempo de ejecución. Así que, si es necesario, puede mover un servicio por el clúster, agregar y quitar requisitos, etc. Service Fabric se encarga de garantizar que el servicio siempre esté en funcionamiento y disponible incluso cuando se estén produciendo estos tipos de cambios.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Las restricciones de selección de ubicación (junto con muchas otras propiedades de control de orquestador de las que hablaremos) se especifican para cada instancia de servicio nombrada. Las actualizaciones siempre asumen el lugar o sobrescriben lo que se especificó antes.

También es interesante reseñar que, en este punto, las propiedades de un nodo se definen mediante la definición del clúster y, por tanto, no se pueden actualizar sin una actualización del clúster, siendo necesario que cada nodo deje de funcionar y vuelva a activarse para actualizar sus propiedades.

## <a name="capacity"></a>Capacity
Uno de los trabajos más importantes de cualquier organizador es ayudar a administrar el consumo de recursos del clúster. Lo último que le interesa si está intentando ejecutar servicios de forma eficaz es un montón de nodos que están activos (lo que conlleva contención de recursos y un rendimiento deficiente) mientras que otros están pasivos (desperdicio de recursos). Pero pensemos en algo aún más básico que el equilibrio (que trataremos en breve); ¿que hay de garantizar que los nodos no se queden sin recursos en primer lugar?

Service Fabric representa los recursos como "Métricas". Las métricas son cualquier recurso físico o lógico que desee describir a Service Fabric. Algunos ejemplos de métricas son elementos como "WorkQueueDepth" o "MemoryInMb". Las métricas se diferencian de las restricciones de selección ubicación y de las propiedades de nodo en que las propiedades de nodo son generalmente descriptores estáticos de los nodos mismos, mientras que las métricas se refieren a los recursos que tienen los nodos y que los servicios consumen cuando se están ejecutando en un nodo. Así que una propiedad sería algo como HasSSD y se podría establecer en true o false, pero la cantidad de espacio que está disponible en esa unidad SSD (y que los servicios consumen) sería una métrica como "DriveSpaceInMb". La capacidad en el nodo establecería "DriveSpaceInMb" en la cantidad total de espacio no reservado en la unidad y los servicios notificarían qué cantidad de la métrica usaron durante el tiempo de ejecución.

Si desactivó todo el *equilibrio*de recursos, Service Fabric Cluster Resource Manager aún podría asegurarse de que ningún nodo terminara superando su capacidad (a menos que el clúster en total estuviera demasiado lleno). Capacidad es otra *restricción* que utiliza Cluster Resource Manager para entender cuánto de un recurso tiene un nodo. Tanto la capacidad como el consumo en el nivel de servicio se expresan con métricas. Por ejemplo, la métrica puede ser "MemoryInMb": un nodo determinado puede tener una capacidad de MemoryInMb de 2048, mientras que un servicio determinado puede decir que actualmente consume 64 de MemoryInMb.

Durante el tiempo de ejecución, Cluster Resource Manager realiza un seguimiento de la cantidad de cada recurso que hay presente en cada nodo (definida por su capacidad) y la cantidad restante (restando cualquier uso declarado de cada servicio). Con esta información, Service Fabric Resource Manager puede averiguar dónde colocar o mover las réplicas para que los nodos no superen su capacidad.

C#:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "MemoryInMb";
metric.PrimaryDefaultLoad = 64;
metric.SecondaryDefaultLoad = 64;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,64,64)
```

![Nodos del clúster y capacidad][Image7]

Puede ver esto en el manifiesto de clúster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="Disk" Value="10000"/>
      </Capacities>
    </NodeType>
```

También es posible que la carga de un servicio cambie de forma dinámica. Pongamos que la carga de la réplica cambió de 64 a 1024, pero el nodo que se estaba ejecutando en ese momento solo tenía 512 (de la métrica de "MemoryInMb") restantes. Por eso, la colocación actual de una instancia o una réplica deja de ser válida, ya que el uso combinado de todas las réplicas e instancias de ese nodo supera su capacidad. Hablaremos después sobre este escenario en que la carga puede cambiar dinámicamente, pero en lo que respecta a la capacidad, se controla de la misma manera: Cluster Resource Manager se inicia automáticamente y reduce el uso de capacidad del nodo moviendo una o varias réplicas o instancias de ese nodo a otros. Al hacer esto, Cluster Resource Manager intenta minimizar el costo de todos los movimientos (volveremos a la noción de costo más adelante).

## <a name="cluster-capacity"></a>Capacidad del clúster
Entonces, ¿cómo se impide que el clúster en general se llene demasiado? Bueno, con la carga dinámica realmente no se puede hacer mucho (ya que los servicios pueden tener un pico de carga con independencia de las acciones realizadas por Cluster Resource Manager; un clúster con espacio de sobra hoy puede quedarse corto cuando se haga famoso mañana), pero existen algunos controles preparados para evitar problemas básicos. Lo primero que se puede hacer es evitar la creación de nuevas cargas de trabajo que harían que el clúster se llenara.

Supongamos que va a crear un simple servicio sin estado y que tiene cierta carga asociada (más adelante, se trata la notificación de cargas predeterminadas y dinámicas). Para este servicio, digamos que le interesa un recurso (por ejemplo, DiskSpace) y que de forma predeterminada va a consumir 5 unidades de DiskSpace para cada instancia del servicio. Debería crear 3 instancias del servicio. Estupendo. Eso significa que necesitamos 15 unidades de DiskSpace en el clúster para poder siquiera crear estas instancias de servicio. Service Fabric está continuamente calculando la capacidad total y el consumo de cada métrica, por lo que se puede tomar una decisión fácilmente y rechazar la llamada para crear el servicio si el espacio es insuficiente.

Tenga en cuenta que, dado que el requisito es solo que haya 15 unidades disponibles, este espacio se podría asignar de muchas maneras distintas; por ejemplo, podría ser una unidad de capacidad restante en 15 nodos diferentes o las tres unidades de capacidad que quedan en 5 nodos diferentes, etc. Si no hay capacidad suficiente en tres nodos diferentes, Service Fabric reorganizará los servicios que ya se encuentran en el clúster con el fin de liberar espacio en los tres nodos necesarios. Tal reorganización es casi siempre posible a menos que el clúster en su totalidad esté prácticamente lleno.

## <a name="buffered-capacity"></a>Capacidad de búfer
Otra cosa que ayuda a administrar la capacidad general del clúster es agregar la noción de un búfer de reserva a la capacidad especificada en cada nodo. Esta configuración es opcional, pero permite a los usuarios reservar una parte de la capacidad total del nodo para que solo se use para colocar servicios durante las actualizaciones y los errores, es decir, en aquellos casos en que la capacidad del clúster se ve reducida. En la actualidad, el búfer se especifica de forma global para cada métrica y para todos los nodos mediante ClusterManifest. El valor que elija para la capacidad reservada será una función de los recursos para los que los servicios tienen mayores restricciones, así como el número de dominios de error y de actualización que contiene el clúster. Por lo general, un mayor número de dominios de error y de actualización quiere decir que puede elegir un número menor para la capacidad de búfer, ya que espera que la proporción del clúster que no esté disponible durante las actualizaciones y los errores sea menor. Tenga en cuenta que el porcentaje de búfer solo tiene sentido si también especifica la capacidad de nodo para una métrica.

Este es un ejemplo de cómo especificar la capacidad de búfer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

La creación de nuevos servicios generarán un error cuando el clúster carece de capacidad de búfer, lo que garantiza que el clúster mantenga una reserva suficiente para que los errores y las actualizaciones no hagan que los nodos superen su capacidad. Cluster Resource Manager expone gran parte de esta información a través de PowerShell y las API de consulta, lo que le permite ver la configuración de capacidad de búfer, la capacidad total y el consumo actual de cada métrica que se usa en el clúster. Aquí podemos ver un ejemplo del resultado:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2015 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2015 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre el flujo de información y la arquitectura dentro de Cluster Resource Manager, consulte [este artículo ](service-fabric-cluster-resource-manager-architecture.md)
* Definir las métricas de desfragmentación es una manera de consolidar la carga en los nodos en lugar de distribuirla. Para saber cómo configurar la desfragmentación, consulte [este artículo](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Empiece desde el principio y [obtenga una introducción a Cluster Resource Manager de Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
* Para más información sobre cómo Cluster Resource Manager administra y equilibra la carga en el clúster, consulte el artículo sobre el [equilibrio de carga](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png



<!--HONumber=Dec16_HO2-->


