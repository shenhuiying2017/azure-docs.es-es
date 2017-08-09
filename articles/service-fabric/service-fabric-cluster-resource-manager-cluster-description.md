---
title: "Descripción del clúster del equilibrador de recursos | Microsoft Docs"
description: "Descripción de un clúster de Service Fabric mediante la especificación de dominios de error, dominios de actualización, propiedades de nodo y funcionalidades de nodo en Cluster Resource Manager."
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
ms.date: 01/05/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: cfc38cecfaf0a0bdaae043fc35dcfed743459823
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="describing-a-service-fabric-cluster"></a>Descripción de un clúster de Service Fabric
Service Fabric Cluster Resource Manager proporciona varios mecanismos para describir un clúster. Durante el tiempo de ejecución, Cluster Resource Manager usa esta información para garantizar la alta disponibilidad de los servicios que se ejecutan en el clúster. Al aplicar estas reglas importantes, también trata de optimizar su consumo de recursos.

## <a name="key-concepts"></a>Conceptos clave
Cluster Resource Manager es compatible con varias de las características que describen un clúster:

* Dominios de error
* Dominios de actualización
* Propiedades del nodo
* Capacidades de nodo

## <a name="fault-domains"></a>Dominios de error
Un dominio de error es cualquier área de error coordinado. Una única máquina constituye un dominio de error (ya que ella sola puede dejar de funcionar por muchas razones diferentes, desde errores en el sistema de alimentación hasta unidades averiadas o un firmware de NIC defectuoso). Varias máquinas conectadas al mismo conmutador Ethernet se encuentran en el mismo dominio de error, al igual que aquellas que estén conectadas a una sola fuente de energía. Como es natural que coincidan averías de hardware, los dominios de error son intrínsecamente jerárquicos y se representan como identificadores URI en Service Fabric.

Al configurar su propio clúster, debe considerar detenidamente estas diferentes áreas de error. Es importante que los dominios de error se configuren correctamente porque Service Fabric usa esta información para colocar servicios de forma segura. Service Fabric no quiere volver a colocar los servicios de forma que la pérdida de un dominio de error (causado por la avería de algún componente) provoque que los servicios dejen de funcionar. En el entorno de Azure, Service Fabric aprovecha la información sobre dominios de error que proporciona dicho entorno para configurar automáticamente correctamente los nodos del clúster.

En el siguiente gráfico, se muestran coloreadas todas las entidades que contribuyen a la generación de dominios de error y todos los que se crean. En este ejemplo, tenemos centros de datos (DC), bastidores (R) y servidores blade (B). Posiblemente, si cada servidor blade contiene más de una máquina virtual, podría haber otra capa en la jerarquía de dominios de error.

<center>
![Nodos organizados a través de dominios de error][Image1]
</center>

Durante el tiempo de ejecución, la utilidad Cluster Resource Manager de Service Fabric tiene en cuenta los dominios de error del clúster y planea un diseño.  Trata de distribuir las réplicas con estado o las instancias sin estado de un servicio determinado para que estén en distintos dominios de error. Este proceso ayuda a garantizar que, en caso de un error en cualquiera de los dominios de error (en cualquier nivel en la jerarquía), no peligre la disponibilidad de ese servicio.

La utilidad Cluster Resource Manager de Service Fabric no tiene en cuenta cuántos niveles hay en la jerarquía de dominios de error. Sin embargo, sí que trata de asegurarse de que la pérdida de cualquier parte de la jerarquía no afecte a los servicios que se ejecutan en un nivel superior. Por este motivo, es mejor si hay el mismo número de nodos en cada nivel de profundidad de la jerarquía de dominios de error. Mantener un equilibrio en los niveles impide que una parte de la jerarquía contenga más servicios que otros. De lo contrario, se producirían desequilibrios en la carga de nodos específicos y errores en determinados dominios más graves que otros.

Si el "árbol" de dominios de error no está equilibrado en el clúster, Cluster Resource Manager tendrá más complicado determinar la mejor asignación de servicios. Los diseños de dominios de error desequilibrados provocan que la pérdida de un dominio en concreto pueda afectar más a la disponibilidad del clúster que otros. Como resultado, Cluster Resource Manager se debate entre sus dos objetivos: usar las máquinas de ese dominio "pesado" colocando servicios en ellos, y hacerlo de forma que la pérdida de un dominio no cause problemas. ¿Qué quiere decir esto?

<center>
![Dos diseños de clúster distintos][Image2]
</center>

En el diagrama anterior, se muestran dos diseños de clúster de ejemplo distintos. En el primero, los nodos se distribuyen uniformemente entre los dominios de error. En el otro, un dominio de error termina con muchos más nodos. Si nunca ha configurado su propio clúster de forma local o en otro entorno, es algo que debe considerar.

En Azure, será su responsabilidad elegir qué dominio de error contendrá un nodo. Sin embargo, dependiendo del número de nodos que se aprovisionen, puede seguir obteniendo dominios de error con más nodos que otros. Por ejemplo, supongamos que tiene cinco dominios de error, pero aprovisiona siete nodos para un determinado objeto NodeType. En este caso, los dos primeros dominios de error acaban con más nodos. Si trata de implementar más objetos NodeType con solo dos instancias, el problema será más grave.

## <a name="upgrade-domains"></a>Dominios de actualización
Los dominios de actualización son otra característica que ayuda a la utilidad Resource Manager Cluster de Service Fabric a comprender el diseño del clúster para poder prever errores con antelación. Estos definen conjuntos de nodos que se actualizan al mismo tiempo.

Los dominios de actualización son muy similares a los dominios de error, pero con algunas diferencias clave. En primer lugar, mientras que la definición de dominios de error se hace rigurosamente mediante las áreas de errores coordinados de hardware, la de los de actualización se realiza a través de directivas. Gracias a los dominios de actualización, podrá decidir cuántos desea, a diferencia de los de error, que el número se decida en función del entorno. Otra diferencia es que los dominios de actualización (al menos, en la actualidad) no son jerárquicos; constituyen algo más que una simple etiqueta.

En el siguiente diagrama se muestran tres dominios de actualización divididos en tres dominios de error. También se muestra una posible selección de ubicación de tres réplicas diferentes de un servicio con estado, donde cada una de ellas terminan con diferentes dominios de error y de actualización. Gracias a esta selección de ubicación, podemos perder un dominio de error en el transcurso de una actualización de servicio y seguir teniendo una copia del código y los datos.

<center>
![Selección de ubicación con dominios de error y de actualización][Image3]
</center>

Tener un gran número de dominios de actualización tiene ventajas y desventajas. Con más dominios de actualización, cada paso de la actualización es más granular y, por tanto, afecta a un número menor de nodos o servicios. Como consecuencia, hay que mover menos servicios a la vez, lo que reduce la actividad en el sistema y suele mejorar la confiabilidad general (ya que los problemas afectarán a una proporción menor del servicio durante la actualización). Si tiene más dominios de actualización, también necesitará menos sobrecarga en otros nodos para controlar el impacto de la actualización. Por ejemplo, si dispone de cinco dominios de actualización, los nodos de cada uno de ellos controlarán aproximadamente el 20 % del tráfico. Si necesita desactivar ese dominio de actualización para realizar una actualización, esa carga debe ir a otro lado. Si tiene más dominios de actualización, habrá menos sobrecarga que mantener en los demás nodos del clúster.

El inconveniente de tener muchos dominios de actualización es que las actualizaciones suelen tardar más tiempo. Antes de continuar, Service Fabric espera un breve periodo después de que se complete un dominio de actualización. Este retraso ocurre para que se muestren los problemas que causa la actualización y pueda solucionarlos. El compromiso es aceptable, ya que impide que los cambios incorrectos afecten a una proporción excesiva del servicio a la vez.

Usar demasiado pocos dominios de actualización también tiene efectos secundarios: mientras que cada dominio de actualización esté inactivo y actualizándose, una gran parte de la capacidad general no estará disponible. Por ejemplo, si solo dispone de tres dominios de actualización, estará inactiva un tercio de la capacidad general del servicio o del clúster al mismo tiempo. Tener una gran parte del servicio sin actividad al mismo tiempo no es recomendable, ya que hay que contar con suficiente capacidad en el resto del clúster para controlar la carga de trabajo. Asimismo, al mantener ese búfer, en un caso normal, esos nodos tendrán menos carga que de otro modo, lo que aumenta el costo de ejecución del servicio.

No existe ningún límite real en el número total de dominios de error o de actualización en un entorno, ni tampoco restricciones a la forma en que se superponen. Las estructuras comunes que se han explicado son las siguientes:

* Asignación 1:1 de dominios de error y de actualización
* Un dominio de actualización por nodo (instancia del sistema operativo física o virtual)
* Un modelo "seccionado" o "de matriz", donde los dominios de error y de actualización forman una matriz con máquinas que se ejecutan abajo de las diagonales.

<center>
![Diseños de dominio de error y de actualización][Image4]
</center>

No existe un diseño ideal, sino que cada uno tiene sus ventajas e inconvenientes. Por ejemplo, el modelo 1FD:1UD es bastante fácil de configurar, mientras que el modelo de 1 UD (dominio de actualización) por nodo es más parecido a lo que los usuarios están acostumbrados por haber administrado antes conjuntos pequeños de máquinas en los que cada una se pondría fuera de servicio de forma independiente.

El modelo más habitual (y el que se usa en Azure) es la matriz FD/UD, en la que los FD (dominios de error) y los UD forman una tabla donde los nodos se colocan empezando por la diagonal. El hecho de que termine siendo un diseño ralo o denso depende del número total de nodos en comparación con el de dominios de error y de actualización. Es decir, si los clústeres son lo bastante grandes, prácticamente cualquier diseño acaba adquiriendo el aspecto de un patrón de matriz densa, como se muestra en la opción abajo a la derecha en la imagen anterior.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Restricciones de dominio de error y de actualización y el comportamiento resultante
Cluster Resource Manager trata el deseo de mantener un servicio en equilibrio entre los dominios de error y de actualización como una restricción. Para más información sobre las restricciones, vea [este artículo](service-fabric-cluster-resource-manager-management-integration.md). Las restricciones de dominios de error y de actualización se definen de esta forma: "para una partición de servicio específica, nunca tiene que haber una diferencia *mayor que uno* en el número de réplicas (instancias de servicios sin estado o réplicas de servicios con estado) entre dos dominios".  En la práctica, esto significa que para un servicio determinado, ciertos movimientos o determinadas disposiciones pueden no ser válidos en el clúster, porque si lo fueran podría infringir las restricciones de dominio de error o de actualización.

Veamos un ejemplo. Supongamos que tenemos un clúster con 6 nodos, configurado con 5 dominios de error y 5 dominios de actualización.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Ahora, supongamos que creamos un servicio con un valor TargetReplicaSetSize de 5. Las réplicas se dirigen a N1-N5. De hecho, nunca se usará N6, independientemente del número de servicios que cree. Pero ¿por qué? Echemos un vistazo a la diferencia entre el diseño actual y lo que sucedería si se elige N6.

Este es el diseño que obtuvimos y el número total de las réplicas por dominio de error y de actualización:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Este diseño se equilibra en términos de nodos por dominio de error y de actualización. También está equilibrado en cuanto al número de réplicas por dominios de error y de actualización. Cada dominio tiene el mismo número de nodos y el mismo número de réplicas.

Ahora, echemos un vistazo a lo que sucedería si hubiéramos utilizado N6 en lugar de N2. ¿Cómo se distribuirían las réplicas entonces?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

¿Se ha percatado de algo? Este diseño no cumple la definición de la restricción de dominio de error. FD0 tiene 2 réplicas, mientras que FD1 tiene 0, por lo que la diferencia entre FD0 y FD1 es 2. Cluster Resource Manager no permite este tipo de disposición. De forma similar, si se ha seleccionado N2 y N6 (en lugar de N1 y N2), obtendríamos lo siguiente:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Aunque este diseño ofrece equilibrio en términos de dominios de error, infringe la restricción de dominio de actualización (ya que UD0 tiene 0 réplicas, mientras que UD1 tiene 2) y, por tanto, tampoco es válido.

## <a name="configuring-fault-and-upgrade-domains"></a>Configuración de dominios de error y de actualización
La definición de dominios de error y de actualización se realiza automáticamente en las implementaciones de Service Fabric hospedadas en Azure. Service Fabric se limita a recopilar la información sobre el entorno de Azure.

Si va a crear su propio clúster (o quiere ejecutar una determinada topología en desarrollo), debe proporcionar la información de dominios de error y de actualización. En este ejemplo, definimos un clúster de desarrollo local de nueve nodos que abarca tres centros de datos (cada uno con tres bastidores). Este clúster también tiene tres dominios de actualización seccionados en esos tres centros de datos. En la plantilla del manifiesto de clúster, el aspecto será similar al siguiente:

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

a través de ClusterConfig.json para las implementaciones independientes

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> En las implementaciones de Azure, Azure asigna los dominios de error y de actualización. Por lo tanto, la definición de los nodos y roles dentro de la opción de infraestructura de Azure no incluye la información de los dominios de error y de actualización.
>
>

## <a name="placement-constraints-and-node-properties"></a>Restricciones de colocación y propiedades de nodo
A veces (de hecho, la mayor parte del tiempo) le va a interesar asegurarse de que ciertas cargas de trabajo solo se ejecuten en determinados nodos o conjuntos de nodos en el clúster. Por ejemplo, algunas cargas de trabajo pueden requerir GPU o SSD, al contrario que otras. Un buen ejemplo de esto es prácticamente cualquier arquitectura de n niveles, en la que determinadas máquinas sirven de front-end o lado de la aplicación a la interfaz (y, por tanto, posiblemente expuestas a Internet). Otros conjuntos de máquinas (a menudo con recursos de hardware diferentes) se encarga del trabajo de las capas de proceso o almacenamiento (y, normalmente, no expuestas a Internet). Service Fabric espera que, incluso en un mundo de microservicios, haya casos en que se deban ejecutar cargas de trabajo concretas en configuraciones de hardware específicas, por ejemplo:

* una aplicación de n niveles existente se ha transferido "tal cual" a un entorno de Service Fabric;
* se prefiere ejecutar una carga de trabajo en un hardware específico por motivos de rendimiento, escala o aislamiento de seguridad;
* una carga de trabajo debe estar aislada de otras por una directiva o a causa del consumo de recursos.

Para admitir estos tipos de configuraciones, Service Fabric parte de una noción de etiquetas de primera clase que pueden aplicarse a los nodos. Estas se denominan "restricciones de selección de ubicación". Se pueden usar restricciones de selección de ubicación para indicar dónde se deben ejecutar determinados servicios. El conjunto de restricciones es extensible; puede funcionar cualquier par clave-valor.

<center>
![Diferentes cargas de trabajo por diseño de clúster][Image5]
</center>

Las diferentes etiquetas de clave-valor de los nodos se conocen como "*propiedades* de selección de ubicación" (o simplemente "propiedades de nodo"). El valor especificado en la propiedad de nodo puede ser una cadena, un booleano o de tipo Long con signo. La instrucción en el servicio se denomina "*restricción* de selección de ubicación", puesto que limita el lugar del clúster donde puede ejecutarse el servicio. La restricción puede ser cualquier instrucción booleana que opera en las diferentes propiedades del nodo en el clúster. Los selectores válidos de estas instrucciones booleanas son los siguientes:

1) comprobaciones condicionales para crear instrucciones concretas

| Instrucción | Sintaxis |
| --- |:---:|
| "equal to" | "==" |
| "not equal to" | "!=" |
| "greater than" | ">" |
| "greater than or equal to" | ">=" |
| "less than" | "<" |
| "less than or equal to" | "<=" |

2) Instrucciones booleanas para operaciones lógicas y de agrupación

| Instrucción | Sintaxis |
| --- |:---:|
| "and" | "&&" |
| "or" | "&#124;&#124;" |
| "not" | "!" |
| "group as single statement" | "()" |

Estos son algunos ejemplos de instrucciones de restricción básicas.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

El servicio se puede colocar solo en los nodos donde la instrucción general se evalúa como "True". Los nodos que no tengan definida ninguna propiedad no coinciden con ninguna restricción de selección de ubicación que contenga esa propiedad.

Además, Service Fabric define algunas propiedades predeterminadas que se pueden usar automáticamente sin que el usuario tenga que definirlas. En el momento de escribir este artículo, las propiedades predeterminadas definidas en cada nodo eran **NodeType** y **NodeName**. Por ejemplo, podría escribir una restricción de selección de ubicación como `"(NodeType == NodeType03)"`. Por lo general, NodeType es una de las propiedades más usadas. Resulta útil porque se corresponde con un tipo de una máquina que, a su vez, se corresponde con un tipo de carga de trabajo de una arquitectura de aplicaciones de n niveles.

<center>
![Restricciones de selección de ubicación y propiedades de nodo][Image6]
</center>

Supongamos que se definieron las siguientes propiedades de nodo para un tipo de nodo determinado:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure. En la plantilla de Azure Resource Manager de un clúster, es probable que se parametrizaran algunos elementos como el nombre del tipo de nodo; el aspecto sería similar a este: "[parameters('vmNodeType1Name')]" en lugar de "NodeType01".

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Puede crear *restricciones* de selección de ubicación para un servicio de forma parecida a esta:

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

Si está seguro de que todos los nodos de NodeType01 son válidos, también podría seleccionar ese tipo de nodo.

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

Las propiedades de un nodo se definen mediante la definición del clúster y, por tanto, no se pueden actualizar sin una actualización del clúster. Para actualizar las propiedades de un nodo, cada nodo afectado debe desactivarse y, luego, volver a ponerse en marcha.

## <a name="capacity"></a>Capacity
Uno de los trabajos más importantes de cualquier organizador es ayudar a administrar el consumo de recursos del clúster. Lo último que desea si está tratando de ejecutar los servicios de forma eficaz es un gran número nodos activos mientras que otros permanecen inactivos. Los activos provocan la contención de recursos y un rendimiento bajo, y los inactivos están relacionados con el aumento de los costos o el desperdicio de los recursos. Antes de explicar el concepto de equilibrio, ¿por qué no se asegura primero de que los nodos no se queden sin recursos?

Service Fabric representa los recursos como `Metrics`. Las métricas son cualquier recurso físico o lógico que desee describir a Service Fabric. Algunos ejemplos de métricas son elementos como "WorkQueueDepth" o "MemoryInMb". Para obtener información sobre la configuración de métricas y sus usos, lea [este artículo](service-fabric-cluster-resource-manager-metrics.md).

Las métricas se diferencian de las restricciones de selección ubicación y de las propiedades de nodo en que las propiedades de nodo son, generalmente, descriptores estáticos de los nodos, mientras que las métricas se refieren a los recursos que tienen los nodos y que los servicios consumen cuando se están ejecutando en un nodo. Una propiedad de nodo podría ser "HasSSD" y tener un valor de True o False. La cantidad de espacio disponible en ese SSD (y consumido por servicios) sería una métrica como "DriveSpaceInMb". La capacidad "DriveSpaceInMb" del nodo sería la cantidad de espacio total no reservado de la unidad. Los servicios notificarán la cantidad de la métrica usada durante el tiempo de ejecución.

Es importante tener en cuenta que, al igual que para las restricciones de selección de ubicación y las propiedades de nodo, la utilidad Cluster Resource Manager de Service Fabric no comprende lo que significan los nombres de las métricas, ya que son simplemente cadenas. Se recomienda declarar las unidades como parte de los nombres de métricas que cree cuando puedan ser ambiguos.

Si desactivó todo el *equilibrio*de recursos, la utilidad Cluster Resource Manager de Service Fabric aún podría asegurarse de que ningún nodo terminara superando su capacidad. Por lo general, es posible, a menos que el clúster esté demasiado lleno. La capacidad es otra *restricción* que utiliza Cluster Resource Manager para entender cuánto de un recurso tiene un nodo. También se realiza un seguimiento de la capacidad restante en el clúster. Tanto la capacidad como el consumo en el nivel de servicio se expresan con métricas. Por ejemplo, la métrica podría ser "MemoryInMb", y un nodo determinado puede tener una capacidad "MemoryInMb" de 2048. Algún servicio que esté ejecutándose en ese nodo puede indicar que se está consumiendo la cantidad de 64 en "MemoryInMb".

Durante el tiempo de ejecución, Cluster Resource Manager realiza un seguimiento de la cantidad de cada recurso que hay presente en cada nodo y la cantidad restante. Esto se hace restando de la capacidad del nodo cualquier uso declarado de cada servicio que se ejecuta en ese nodo. Con esta información, la utilidad Resource Manager de Service Fabric puede averiguar dónde colocar o mover las réplicas para que los nodos no superen su capacidad.

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
<center>
![Nodos del clúster y capacidad][Image7]
</center>

Puede ver las capacidades definidas en el manifiesto de clúster:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType02">
      <Capacities>
        <Capacity Name="MemoryInMb" Value="2048"/>
        <Capacity Name="DiskInMb" Value="512000"/>
      </Capacities>
    </NodeType>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure. En la plantilla de Azure Resource Manager de un clúster, es probable que se parametrizaran algunos elementos como el nombre del tipo de nodo; el aspecto sería similar a este: "[parameters('vmNodeType2Name')]" en lugar de "NodeType02".

```json
"nodeTypes": [
    {
        "name": "NodeType02",
        "capacities": {
            "MemoryInMb": "2048",
            "DiskInMb": "512000"
        }
    }
],
```

También es posible (y habitual, de hecho) que la carga de un servicio cambie de forma dinámica. Pongamos que la carga de la réplica cambió de 64 a 1024, pero el nodo que se estaba ejecutando en ese momento solo tenía 512 (de la métrica "MemoryInMb") restantes. Ahora, esa selección de ubicación de la instancia o réplica no es válida porque no hay suficiente espacio en ese nodo. Esto también puede ocurrir si el uso combinado de las réplicas e instancias de ese nodo supera la capacidad de dicho nodo. En cualquier caso, Cluster Resource Manager se inicia y reduce el uso de capacidad del nodo moviendo una o varias réplicas o instancias de ese nodo a otros. Al mover las réplicas, Cluster Resource Manager trata de minimizar el costo de los movimientos. Los costos de los movimientos se explican en [este artículo](service-fabric-cluster-resource-manager-movement-cost.md).

## <a name="cluster-capacity"></a>Capacidad del clúster
Entonces, ¿cómo se impide que el clúster en general se llene demasiado? Bueno, con la carga dinámica realmente no hay mucho que se pueda hacer, ya que los servicios pueden tener un pico de carga con independencia de las acciones que realice Resource Manager. Como resultado, un clúster con espacio de sobra hoy puede quedarse corto cuando se haga famoso mañana, pero existen algunos controles preparados para evitar errores básicos. Lo primero que se puede hacer es evitar la creación de nuevas cargas de trabajo que harían que el clúster se llenara.

Supongamos que va a crear un servicio sin estado y que tiene cierta carga asociada (más adelante, se explica la notificación de cargas predeterminadas y dinámicas). Supongamos que el servicio se ocupa de la métrica "DiskSpaceInMb" y que, de forma predeterminada, va a consumir 5 unidades de "DiskSpaceInMb" para cada instancia del servicio. Quiere crear 3 instancias del servicio. Estupendo. Eso significa que necesitamos 15 unidades de "DiskSpaceInMb" en el clúster para poder crear estas instancias de servicio. Cluster Resource Manager está continuamente calculando la capacidad total y el consumo de cada métrica, por lo que puede determinar si hay suficiente espacio en el clúster. Si no lo hay espacio, Cluster Resource Manager rechaza la llamada para crear el servicio.

Dado que el requisito es solo que haya 15 unidades disponibles, este espacio se podría asignar de muchas maneras distintas; por ejemplo, podría ser una unidad de capacidad restante en 15 nodos diferentes o las tres unidades de capacidad que quedan en 5 nodos diferentes. Siempre y cuando Cluster Resource Manager pueda reorganizarlo todo para que haya 5 unidades en 3 nodos, podrá colocar el servicio. Tal reorganización es casi siempre posible, a menos que el clúster en su totalidad esté prácticamente lleno o los servicios estén muy ocupados, o ambas situaciones.

## <a name="buffered-capacity"></a>Capacidad de búfer
Otra característica de Cluster Resource Manager es ayuda a administrar la capacidad general del clúster es agregar la noción de un búfer de reserva a la capacidad especificada en cada nodo. La capacidad de búfer permite reservar una parte de la capacidad total del nodo para que solo se utilice con el objetivo de colocar los servicios durante las actualizaciones y los errores de nodo. En la actualidad, el búfer se especifica de forma global para cada métrica y todos los nodos mediante la definición de clúster. El valor que elija para la capacidad reservada es una función del número de dominios de actualización y de error que hay en el clúster, y de la cantidad de sobrecarga deseada. Más dominios de error y de actualización significa que puede elegir un número menor para la capacidad de búfer. Si tiene varios dominios, puede esperar que cantidades más reducidas de su clúster no estarán disponibles durante las actualizaciones y los errores. El porcentaje de búfer solo tiene sentido si también especifica la capacidad de nodo de una métrica.

Este es un ejemplo de cómo especificar la capacidad de búfer:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="DiskSpace" Value="0.10" />
            <Parameter Name="Memory" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

a través de ClusterConfig.json para las implementaciones independientes o Template.json para los clústeres hospedados en Azure:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "DiskSpace",
          "value": "0.10"
      },
      {
          "name": "Memory",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

La creación de nuevos servicios generará un error cuando el clúster carece de capacidad de búfer en una métrica, lo que garantiza que el clúster mantenga una reserva suficiente para que los errores y las actualizaciones no hagan que los nodos superen su capacidad. La capacidad de búfer es opcional, pero se recomienda en los clústeres que definen una capacidad de una métrica.

Cluster Resource Manager expone esta información a través de PowerShell y las API de consulta. De este modo, podrá ver la configuración de capacidad de búfer, la capacidad total y el consumo actual de todas las métricas en uso en el clúster. Aquí podemos ver un ejemplo del resultado:

```posh
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
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
* Para obtener más información sobre el flujo de información y la arquitectura de Cluster Resource Manager, consulte [este artículo](service-fabric-cluster-resource-manager-architecture.md).
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

