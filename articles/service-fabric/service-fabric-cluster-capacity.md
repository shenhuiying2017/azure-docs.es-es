---
title: "Consideraciones de planeación de capacidad del clúster de Service Fabric | Microsoft Docs"
description: "Consideraciones de planeación de capacidad del clúster de Service Fabric Tipos de nodos, operaciones, durabilidad y niveles de confiabilidad"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: chackdan
ms.openlocfilehash: 04964175f06675a486fcf252f194f0d790acea4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Consideraciones de planeación de capacidad del clúster de Service Fabric
En cualquier implementación de producción, la planeación de capacidad es un paso importante. Estos son algunos de los elementos que se deben tener en cuenta como parte de ese proceso.

* Número de tipos de nodos con los que el clúster tiene que empezar
* Propiedades de cada tipo de nodo (tamaño, principal, accesible desde Internet, número de máquinas virtuales, etc.)
* Características de confiabilidad y durabilidad del clúster

Repasemos brevemente cada uno de estos elementos.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Número de tipos de nodos con los que el clúster tiene que empezar
En primer lugar, tiene que averiguar para qué se va a usar el clúster que está creando y qué tipos de aplicaciones piensa implementar en este clúster. Si no tiene clara la finalidad del clúster, es muy probable que todavía no está listo para especificar el proceso de planeación de capacidad.

Establezca el número de tipos de nodos con los que el clúster tiene que empezar.  Cada tipo de nodo se asigna a un conjunto de escalado de máquina virtual. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Por lo que la decisión del número de tipos de nodo depende esencialmente de las consideraciones siguientes:

* ¿Tiene la aplicación varios servicios y alguno de ellos ha de ser público o accesible desde Internet? Las aplicaciones típicas contienen un servicio front-end de puerta de enlace que recibe entrada de un cliente, así como uno o varios servicios back-end que se comunican con los servicios front-end. Por lo que en este caso, acaba teniendo al menos dos tipos de nodo.
* ¿Tienen los servicios (que componen la aplicación) distintos requisitos de infraestructura, como, por ejemplo, una RAM mayor o un número más alto de ciclos de CPU? Por ejemplo, supongamos que la aplicación que desea implementar contiene un servicio front-end y un servicio back-end. El servicio front-end se puede ejecutar en máquinas virtuales más pequeñas (tamaños de VM como D2) que tienen puertos abiertos a Internet.  Pero el servicio back-end es de cálculo intensivo y tiene que ejecutarse en máquinas virtuales más grandes (con tamaños de VM como D4, D6 o D15) que no son accesibles desde Internet.
  
  En este ejemplo, aunque puede optar por colocar todos los servicios en un tipo de nodo, se recomienda colocarlos en un clúster con dos tipos de nodos.  Esto permite que cada tipo de nodo pueda tener distintas propiedades, como por ejemplo, conectividad a Internet o tamaño de la máquina virtual. El número de máquinas virtuales también se puede escalar de forma independiente.  
* Puesto que no puede predecir el futuro, parta de hechos que conozca y decida el número de tipos de nodo que con el que deben empezar las aplicaciones. Siempre puede agregar o quitar tipos de nodos más adelante. Un clúster de Service Fabric debe tener como mínimo un tipo de nodo.

## <a name="the-properties-of-each-node-type"></a>Las propiedades de cada tipo de nodo.
El **tipo de nodo** puede considerarse similar a los roles de Cloud Services. Los tipos de nodos definen los tamaños de máquina virtual, el número de máquinas virtuales y sus propiedades. Cada tipo de nodo que se define en un clúster de Service Fabric está configurado como un conjunto de escalado de máquinas virtuales independiente. Los conjuntos de escalado de máquinas virtuales son un recurso de proceso de Azure que se puede usar para implementar y administrar una colección de máquinas virtuales de forma conjunta. Al definirse como diferentes conjuntos de escalado de máquinas virtuales, cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente, tener diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad.

Lea [este documento](service-fabric-cluster-nodetypes.md) para más información sobre la relación de Nodetypes con el conjunto de escalado de máquinas virtuales, cómo usar el protocolo RDP en una de las instancias, abrir nuevos puertos, etc.

El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal (el primero que se define en el portal) debe tener al menos cinco máquinas virtuales para los clústeres que se usan en cargas de trabajo de producción (o al menos tres máquinas virtuales en clústeres de prueba). Si va a crear el clúster mediante una plantilla de Resource Manager, busque el atributo **Es principal** en la definición del tipo de nodo. El tipo de nodo principal es el tipo de nodo en que se colocan los servicios del sistema de Service Fabric.  

### <a name="primary-node-type"></a>Tipo de nodo principal
En el caso de un clúster con varios tipos de nodo, tendrá elegir uno de ellos como principal. Estas son las características de un tipo de nodo principal:

* El **tamaño mínimo de las máquinas virtuales** del tipo de nodo principal se determina mediante el **nivel de durabilidad** que se elija. El valor predeterminado del nivel de durabilidad es Bronze. Desplácese hacia abajo para más información sobre el nivel de durabilidad y los valores que puede adoptar.  
* El **número mínimo de máquinas virtuales** del tipo de nodo principal se determina mediante el **nivel de confiabilidad** que se elija. El valor predeterminado del nivel de confiabilidad es Silver. Desplácese hacia abajo para más información sobre el nivel de confiabilidad y los valores que puede adoptar. 


* Los servicios del sistema de Service Fabric (por ejemplo, el servicio Administrador de clústeres o el servicio de almacén de imágenes) se colocan en el tipo de nodo principal y así la confiabilidad y la durabilidad del clúster se determinan mediante el valor de nivel de confiabilidad y el valor de nivel de durabilidad que se seleccionen para el tipo de nodo principal.

![Captura de pantalla de un clúster que tiene dos tipos de nodos ][SystemServices]

### <a name="non-primary-node-type"></a>Tipo de nodo no principal
Para un clúster con varios tipos de nodos, hay un tipo de nodo principal y los demás son de tipo no principal. Estas son las características de un tipo de nodo no principal:

* El tamaño mínimo de las máquinas virtuales para este tipo de nodo se determina mediante el nivel de durabilidad que se elija. El valor predeterminado del nivel de durabilidad es Bronze. Desplácese hacia abajo para más información sobre el nivel de durabilidad y los valores que puede adoptar.  
* El número mínimo de máquinas virtuales para este tipo de nodo puede ser uno. Pero este número se debe elegir en función del número de réplicas de la aplicación o de servicios que desea ejecutar en este tipo de nodo. Después de implementar el clúster, se puede aumentar el número de máquinas virtuales de un tipo de nodo.

## <a name="the-durability-characteristics-of-the-cluster"></a>Características de durabilidad del clúster
El nivel de durabilidad se usa para indicar al sistema los privilegios que tienen las máquinas virtuales con la infraestructura subyacente de Azure. En el tipo de nodo principal, este privilegio permite que Service Fabric pause cualquier solicitud de la infraestructura de nivel de máquina virtual (por ejemplo, reinicio de máquina virtual, restablecimiento de la imagen inicial de máquina virtual o migración de máquina virtual) que afecte a los requisitos de quórum de los servicios del sistema y los servicios con estado. En el tipo de nodo no principal, este privilegio permite que Service Fabric pause cualquier solicitud de la infraestructura de nivel de máquina virtual (por ejemplo, reinicio de máquina virtual, restablecimiento de la imagen inicial de máquina virtual o migración de máquina virtual) que afecte a los requisitos de quórum de los servicios con estado que se ejecuten en él.

Este privilegio se expresa con los siguientes valores:

* Gold: los trabajos de infraestructura se pueden pausar dos horas por cada UD. La durabilidad de Gold solo puede habilitarse en las SKU de la máquina virtual del nodo completo, como D15_V2, G5, etc.
* Silver: los trabajos de infraestructura se pueden pausar 10 minutos por cada UD y están disponibles en todas las máquinas virtuales estándares de un solo núcleo y superiores.
* Bronze: sin privilegios. Este es el valor predeterminado. Utilice solo este nivel de durabilidad en los tipos de nodos que ejecutan _exclusivamente_ cargas de trabajo sin estado. 

> [!WARNING]
> Los tipos de nodos que se ejecutan con la durabilidad Bronze no obtienen _privilegios_. Es decir, los trabajos de infraestructura que afectan a las cargas de trabajo sin estado no se detendrán ni retrasarán. Es posible que estos trabajos todavía pueden afectar a las cargas de trabajo, de forma que generen tiempos de inactividad u otros problemas. Se recomienda la durabilidad Silver para cualquier tipo de carga de trabajo de producción. Debe mantener un mínimo de cinco nodos de cualquier tipo cuya durabilidad sea Gold o Silver. 
> 

Puede elegir el nivel de durabilidad de todos los tipos de nodos. Puede elegir que un tipo de nodo tenga un nivel de durabilidad Gold o Silver, mientras que el otro tiene un nivel Bronze en el mismo clúster. **Debe mantener un número mínimo de 5 nodos en cualquier tipo de nodo que tenga una durabilidad Gold o Silver**. 

**Ventajas del uso de los niveles de durabilidad Silver o Gold**
 
1. Reduce el número de pasos necesarios en una operación de reducción horizontal (es decir, se llama automáticamente a la desactivación del nodo y a Remove-ServiceFabricNodeState)
2. Reduce el riesgo de pérdida de datos debido a una operación de cambio de SKU en máquinas virtuales locales iniciada por el cliente o a operaciones en la infraestructura de Azure.
     
**Desventajas del uso de los niveles de durabilidad Silver o Gold**
 
1. Las implementaciones tanto en el conjunto de escalado de máquinas virtuales como en otros recursos de Azure relacionados se pueden retrasar, pueden agotar el tiempo de espera o se pueden bloquear completamente por problemas en el clúster o en el nivel de infraestructura. 
2. Aumenta el número de [eventos de los ciclos de vida de las réplicas](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle ) (p. ej. intercambios principales) debidos a las desactivaciones automáticas de nodos durante las operaciones en la infraestructura de Azure.

### <a name="recommendations-on-when-to-use-silver-or-gold-durability-levels"></a>Recomendaciones acerca de cuándo usar los niveles de durabilidad Silver o Gold

Utilice las durabilidades Silver o Gold para todos los tipos de nodo que hospedan servicios con estado que espera reducir horizontalmente (reducir el número de instancias de máquina virtual) con frecuencia, y prefiere que las operaciones de implementación se retrasen en favor de la simplificación de estas operaciones de reducción horizontal. Los escenarios de escalabilidad horizontal (adición de instancias de máquinas virtuales) no se tienen en cuenta al elegir el nivel de durabilidad, solo se tiene en cuenta la reducción horizontal.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Recomendaciones operativas para el tipo de nodo en el que ha elegido los niveles de durabilidad Silver o Gold.

1. Mantenga el clúster y las aplicaciones en buen estado en todo momento, y asegúrese de que las aplicaciones responden a todos los [eventos de los ciclos de vida de las réplicas del servicio](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como que la réplica en compilación está bloqueada) en el momento adecuado.
2. Adopte formas más seguras para cambiar una SKU de una máquina virtual (escalar verticalmente/reducir verticalmente): el cambio de SKU de una máquina virtual de un conjunto de escalado de máquinas virtuales es en sí una operación no segura y por lo tanto debería evitarse, si es posible. Este es el proceso que se puede seguir para evitar problemas comunes.
    - **En el caso de tipo de nodo no principal:** se recomienda crear un nuevo conjunto de escalado de máquinas virtuales, modificar la restricción de la ubicación de servicio para incluir el nuevo conjunto de escalado de máquinas virtuales/tipo de servicio y, después, reducir el número de instancias del conjunto de escalado de máquinas virtuales antiguo a 0, nodo a nodo (esto es para asegurarse de que la eliminación de los nodos no afecta a la confiabilidad del clúster).
    - **En el caso de tipo de nodo principal:** nuestra recomendación es no cambiar la SKU de máquina virtual del tipo de nodo principal. No se admite el cambio de la SKU del tipo de nodo principal. Si la razón de la nueva SKU es la capacidad, se recomienda agregar más instancias. Si ello no es posible, se recomienda crear un clúster nuevo y [restaurar el estado de la aplicación](service-fabric-reliable-services-backup-restore.md) (si procede) a partir del clúster anterior. No es preciso restaurar el estado de ningún servicio del sistema, ya que se vuelven a crear al implementar las aplicaciones en el clúster nuevo. Si ha ejecutado aplicaciones sin estado en el clúster, lo único que hace es implementar las aplicaciones en el clúster nuevo, no hay nada que para restaurar. Si decide ir por la ruta no compatible y desea cambiar la SKU de la máquina virtual, realice modificaciones en la definición del modelo del conjunto de escalado de máquinas virtuales para que refleje la SKU nueva. Si el clúster tiene un solo tipo de nodo, asegúrese de que las aplicaciones con estado responden a todos los [eventos de ciclo de vida de réplica de servicio](service-fabric-reliable-services-advanced-usage.md#stateful-service-replica-lifecycle) (como que la réplica en creación está bloqueada) en el momento adecuado y que la duración de la regeneración de la réplica del servicio es inferior a cinco minutos (en el nivel de durabilidad Silver). 


> [!WARNING]
> No se recomienda cambiar el tamaño de la SKU de VM en los conjuntos de escalado de máquinas virtuales de Microsoft Azure que no ejecutan, como mínimo, la durabilidad Silver. Modificar el tamaño de la SKU de VM constituye una operación de infraestructura local de destrucción de datos. Sin la posibilidad de retrasar o supervisar este cambio, es posible que la operación pueda provocar una pérdida de datos en los servicios con estado, o bien causar otros problemas de funcionamiento imprevistos, incluso en las cargas de trabajo sin estado. 
> 
    
3. Mantenga un mínimo de cinco nodos en todos los conjuntos de escalado de máquinas virtuales que tiene los niveles de durabilidad Gold o Silver habilitados
4. No elimine instancias de máquina virtual aleatorias, use siempre la característica de reducción vertical del conjunto de escalado de máquinas virtuales. La eliminación de instancias de máquina virtual aleatorias tiene el potencial de crear desequilibrios en la instancia de máquina virtual repartidos entre UD y FD. Este desequilibrio puede afectar negativamente a la capacidad de los sistemas para realizar un correcto equilibrio de la carga entre las instancias del servicio o réplicas del servicio.
6. Si se usa el escalado automático, establezca las reglas de forma que la reducción horizontal (eliminación de instancias de máquina virtual) no se realiza en varios nodos simultáneamente. No es seguro reducir verticalmente más de una instancia a la vez.
7. Si reduce verticalmente un tipo de nodo principal, no deberá hacerlo más de lo que permite el nivel de confiabilidad.


## <a name="the-reliability-characteristics-of-the-cluster"></a>Características de confiabilidad del clúster
El nivel de confiabilidad se usa para establecer el número de réplicas de los servicios del sistema que se desea ejecutar en el tipo de nodo principal de este clúster. Cuanto mayor sea el número de réplicas, más confiables son los servicios del sistema en el clúster.  

El nivel de confiabilidad puede adoptar los siguientes valores:

* Platinum: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 9
* Gold: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 7
* Silver: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 5 
* Bronze: los servicios del sistema se ejecutan con un recuento de conjunto de réplicas de 3

> [!NOTE]
> El nivel de confiabilidad que elija determina el número mínimo de nodos que debe tener el tipo de nodo principal. 
> 
> 


### <a name="recommendations-for-the-reliability-tier"></a>Recomendaciones para el nivel de confiabilidad.

 Al aumentar o disminuir el tamaño del clúster (la suma de las instancias de máquinas virtuales en todos los tipos de nodo), debe actualizar la confiabilidad del clúster desde un nivel a otro. Esto desencadena las actualizaciones de clúster necesarias para cambiar el recuento de conjunto de réplicas de los servicios del sistema. Espere a que finalice la actualización en curso antes de realizar otros cambios en el clúster, como agregar nodos.  Puede supervisar el progreso de la actualización en Service Fabric Explorer o puede ejecutar [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps).

Esta es la recomendación sobre cómo elegir el nivel de confiabilidad.

| **Tamaño del clúster** | **Nivel de confiabilidad** |
| --- | --- |
| 1 |No especifique el parámetro nivel de confiabilidad, el sistema lo calcula |
| 3 |Bronze |
| 5 o 6|Silver |
| 7 u 8 |Gold |
| 9 y superiores |Platinum |




## <a name="primary-node-type---capacity-guidance"></a>Tipo de nodo principal: guía de capacidad

Esta es la guía para planear la capacidad del tipo de nodo principal.

1. **Número de instancias de máquina virtual para ejecutar cualquier carga de trabajo de producción en Azure:** Debe especificar un tamaño mínimo de tipo de nodo principal de 5. 
2. **Número de instancias de máquina virtual para ejecutar cargas de trabajo de prueba en Azure** Puede especificar un tamaño mínimo de tipo de nodo principal de 1 o 3. El clúster de 1 nodo uno funciona con una configuración especial y, por tanto, no se admite el escalado horizontal de ese clúster. El clúster de 1 nodo no tiene ninguna confiabilidad y, por tanto, en la plantilla de Resource Manager, tiene que quitar/no especificar esa configuración (no basta con no definir el valor de configuración). Si configura el clúster del nodo uno configurado mediante el portal, la configuración se controla automáticamente. Los clústeres de 1 y 3 nodos no se admiten en la ejecución de cargas de trabajo de producción. 
3. **SKU de máquina virtual**: el tipo de nodo principal es donde se ejecutan los servicios del sistema, así que la SKU de máquina virtual que elija, debe tener en cuenta la carga máxima global que planea colocar en el clúster. En esta analogía se ilustra el significado de esto: considere que el tipo de nodo principal son sus "pulmones", es lo que lleva oxígeno a su cerebro, de modo que si no se obtiene oxígeno suficiente, el cuerpo sufre. 

Las necesidades de capacidad de un clúster las determina la carga de trabajo que planea ejecutar en el clúster. Por consiguiente, no le podemos proporcionar una guía cualitativa de la carga de trabajo específica, aunque sí una amplia guía que le ayudará a comenzar

Para cargas de trabajo de producción 


- La SKU de máquina virtual recomendada es Standard D3 o Standard D3_V2 o equivalente con un SSD local de 14 GB como mínimo.
- La SKU de máquina virtual mínima admitida es Standard D1 o Standard D1_V2 o equivalente con un SSD local de 14 GB como mínimo. 
- Las SKU de máquina virtual de núcleo parcial, como Standard A0, no se admiten en cargas de trabajo de producción.
- La SKU Standard A1 no se admite en cargas de trabajo de producción por motivos de rendimiento.

> [!WARNING]
> En la actualidad, no se admite el cambio del tamaño de la SKU de la máquina virtual del nodo principal un clúster en ejecución. Por consiguiente, elija la SKU de la máquina virtual del nodo principal con cuidado y, al hacerlo, tenga en cuenta sus necesidades de capacidad futuras. En este momento, la única manera que se admite para mover el tipo de nodo principal a una SKU de máquina virtual nueva (mayor o menor) es crear un clúster nuevo con la capacidad correcta, implementar las aplicaciones en él y restaurar el estado de la aplicación (si procede) desde las [copias de seguridad del servicio más recientes](service-fabric-reliable-services-backup-restore.md) realizadas del clúster anterior. No es preciso restaurar el estado de ningún servicio del sistema, ya que se vuelven a crear al implementar las aplicaciones en el clúster nuevo. Si ha ejecutado aplicaciones sin estado en el clúster, lo único que hace es implementar las aplicaciones en el clúster nuevo, no hay nada que para restaurar.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Tipo de nodo no principal: guía de capacidad para cargas de trabajo con estado

Esta guía está dirigida a cargas de trabajo con estado con [colecciones confiables o actores confiables](service-fabric-choose-framework.md) de Service Fabric en ejecución en el tipo de nodo no principal.


**Número de instancias de máquina virtual**: para cargas de trabajo de producción con estado, se recomienda ejecutarlas con un número mínimo de réplicas de destino de 5. Esto significa que en estado estable acabará con una réplica (de un conjunto de réplicas) en cada dominio de error y dominio de actualización. El concepto de nivel de confiabilidad completo para el tipo de nodo principal es una manera de especificar esta configuración para los servicios del sistema. Por lo que la misma consideración se aplica a los servicios con estado también.

Por lo tanto, para cargas de trabajo de producción, el tamaño mínimo recomendado de tipo de nodo no principal es 5, si ejecuta en él cargas de trabajo con estado.


**SKU de máquina virtual**: es el tipo de nodo donde se ejecutan los servicios de aplicación, de modo que la SKU de máquina virtual que elija para él debe tener en cuenta la carga máxima que planea colocar en cada nodo. Las necesidades de capacidad del tipo de nodo vienen determinadas por la carga de trabajo que planea ejecutar en el clúster, por lo que no es posible proporcionar una guía cualitativa de su carga de trabajo específica, aunque sí una amplia información para ayudarle a comenzar

Para cargas de trabajo de producción 

- La SKU de máquina virtual recomendada es Standard D3 o Standard D3_V2 o equivalente con un SSD local de 14 GB como mínimo.
- La SKU de máquina virtual mínima admitida es Standard D1 o Standard D1_V2 o equivalente con un SSD local de 14 GB como mínimo. 
- Las SKU de máquina virtual de núcleo parcial, como Standard A0, no se admiten en cargas de trabajo de producción.
- En concreto, la SKU Standard A1 no se admite en cargas de producción por motivos de rendimiento.


## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Tipo de nodo no principal: guía de capacidad para cargas de trabajo sin estado

Esta guía se aplica a las cargas de trabajo sin estado en ejecución en el tipo de nodo no principal.

**Número de instancias de máquina virtual:** en el caso de las cargas de trabajo de producción sin estado, el tamaño mínimo admitido del tipo de nodo no principal es 2. Esto le permite ejecutar dos instancias sin estado de la aplicación y permite que el servicio para sobreviva a la pérdida de una instancia de máquina virtual. 

> [!NOTE]
> Si el clúster se ejecuta en una versión de Service Fabric inferior a la 5.6 debido a un defecto en el entorno de tiempo de ejecución (que se prevé corregir en la 5.6), al reducir verticalmente un tipo de nodo no principal a menos de 5, el mantenimiento del clúster pasará a un estado incorrecto hasta que llame a [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/servicefabric/vlatest/Remove-ServiceFabricNodeState) con el nombre de nodo adecuado. Para más información, lea [Escalado o reducción horizontal de un clúster de Service Fabric ](service-fabric-cluster-scale-up-down.md).
> 
>

**SKU de máquina virtual**: es el tipo de nodo donde se ejecutan los servicios de aplicación, de modo que la SKU de máquina virtual que elija para él debe tener en cuenta la carga máxima que planea colocar en cada nodo. Las necesidades de capacidad del tipo de nodo vienen determinadas por la carga de trabajo que planea ejecutar en el clúster, por lo que no es posible proporcionar una guía cualitativa de su carga de trabajo específica, aunque sí una amplia información para ayudarle a comenzar

Para cargas de trabajo de producción 


- La SKU de máquina virtual recomendada es Standard D3 o Standard D3_V2 o equivalente. 
- La SKU de máquina virtual mínima admitida es Standard D1 o Standard D1_V2 o equivalente. 
- Las SKU de máquina virtual de núcleo parcial, como Standard A0, no se admiten en cargas de trabajo de producción.
- La SKU Standard A1 no se admite en cargas de trabajo de producción por motivos de rendimiento.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Pasos siguientes
Después de finalizar la planeación de capacidad y configurar un clúster, puede consultar lo siguiente:

* [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)
* [Planeamiento de recuperación ante desastres](service-fabric-disaster-recovery.md)
* [Relación de tipos de nodos con conjuntos de escalado de máquinas virtuales](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
