---
title: Preguntas frecuentes sobre Microsoft Azure Service Fabric | Microsoft Docs
description: "Preguntas más frecuentes acerca de Service Fabric y sus respuestas"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2017
ms.author: seanmck
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2bfbb3b8f7282ec8ae8abe9597230a3485221ecf
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017

---


# <a name="commonly-asked-service-fabric-questions"></a>Preguntas frecuentes sobre Service Fabric

Hay muchas preguntas que se plantean con frecuencia acerca de qué puede hacer Service Fabric y cómo se debe usar. En este documento se incluyen muchas de esas preguntas y sus respuestas.

## <a name="cluster-setup-and-management"></a>Instalación y administración de clústeres

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions"></a>¿Puedo crear un clúster que abarque varias regiones de Azure?

En la actualidad no, pero esta es una solicitud común y seguimos investigando en este sentido.

El núcleo de la tecnología de clústeres de Service Fabric no tiene conocimiento de las regiones de Azure y puede utilizarse para combinar máquinas que se ejecuten en cualquier lugar del mundo, siempre y cuando tengan conectividad de red entre sí. Sin embargo, el recurso de clúster de Service Fabric en Azure es regional, como los conjuntos de escalado de máquinas virtuales en los que el clúster está basado. Además, hay un desafío inherente en la entrega de la replicación de datos de gran coherencia entre equipos separados por grandes distancias. Antes de admitir clústeres que abarquen varias regiones queremos asegurarnos de que el rendimiento es predecible y aceptable.

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>¿Los nodos de Service Fabric reciben automáticamente actualizaciones del sistema operativo?

En la actualidad no, pero esta es también una solicitud común a la que pensamos responder.

El desafío con las actualizaciones del sistema operativo es que normalmente requieren un reinicio de la máquina, lo que supone una pérdida temporal de disponibilidad. En sí mismo, esto no es problema, puesto que Service Fabric redirigirá automáticamente el tráfico para los servicios afectados a otros nodos. Sin embargo, si las actualizaciones del sistema operativo no se coordinan en el clúster, existe la posibilidad de que muchos nodos dejen de funcionar a la vez. Estos reinicios simultáneos pueden provocar la pérdida de disponibilidad completa para un servicio, o por lo menos para una partición específica (para un servicio con estado).

En el futuro, mantendremos una directiva de actualización del sistema operativo, completamente automatizada, que esté coordinada entre los dominios de actualización, asegurando con ello el mantenimiento de la disponibilidad a pesar de los reinicios y otros errores inesperados.

Mientras tanto, [suministramos un script](https://blogs.msdn.microsoft.com/azureservicefabric/2017/01/09/os-patching-for-vms-running-service-fabric/) que el administrador de un clúster puede usar para iniciar manualmente la revisión de cada nodo de una manera segura.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>¿Puedo usar conjuntos de escalado grandes de máquinas virtuales en el clúster SF? 

**Respuesta corta**: no. 

**Respuesta larga**: aunque los conjuntos de escalado grandes de máquinas virtuales permiten escalar un conjunto de escalado de máquinas virtuales hasta en 1000 instancias de máquina virtual, lo hace mediante el uso de grupos de selección de ubicación (PG). Los dominios de error (FD) y los dominios de actualización (UD) solo son coherentes dentro de un grupo de selección de ubicación. Service Fabric usa los FD y los UD para tomar decisiones de selección de ubicación de las réplicas o instancias del servicio. Puesto que los FD y los UD solo son comparables dentro de un grupo de selección de ubicación SF no puede usarlo. Por ejemplo, si VM1 en PG1 tiene una topología de FD=0 y VM9 en PG2 tiene una topología de FD=4, esto no significa que VM1 y VM2 se encuentren en dos bastidores de hardware diferentes. Por tanto, SF no podrá usar los valores de FD en este caso para tomar decisiones de selección de ubicación.

Actualmente, hay otros problemas con los conjuntos de escalado grandes de máquinas virtuales, como la falta de soporte del equilibrio de carga del nivel 4. Consulte para obtener [detalles sobre conjuntos de escalado grandes](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>¿Cuál es el tamaño mínimo de un clúster de Service Fabric? ¿Por qué no puede ser menor?

El tamaño mínimo admitido para un clúster de Service Fabric que ejecute cargas de trabajo de producción es cinco nodos. Para escenarios de desarrollo y prueba, se admiten los nodos de tres clústeres.

Estos mínimos existen porque el clúster de Service Fabric ejecuta un conjunto de servicios de sistema con estado, incluidos el servicio de nomenclatura y el administrador de conmutación por error. Estos servicios, que realizan un seguimiento de qué servicios se han implementado en el clúster y en dónde se hospedan en la actualidad, dependen de la existencia de una fuerte coherencia. Esta fuerte coherencia depende a su vez de la capacidad de obtener *cuórum* para cualquier actualización del estado de esos servicios, en donde un cuórum representa una mayoría estricta de las réplicas (N/2 + 1) para un servicio dado.

Con esta información, examinemos algunas posibles configuraciones de clúster:

**Un nodo**: esta opción no proporciona alta disponibilidad, ya que la pérdida de este nodo único por cualquier razón, supone la pérdida de todo el clúster.

**Dos nodos**: un cuórum para un servicio implementado en dos nodos (N = 2) is 2 (2/2 + 1 = 2). Cuando se pierde una réplica, es imposible crear un cuórum. Teniendo en cuenta que realizar una actualización del servicio requiere desconectar una réplica, esta no es una configuración útil.

**Tres nodos**: con tres nodos (N=3), el requisito para crear un cuórum es aún dos nodos (3/2 + 1 = 2). Esto significa que se puede perder un solo nodo y continuar manteniendo el cuórum.

La configuración de clúster de tres nodos se admite en desarrollo y pruebas porque puede realizar actualizaciones de forma segura y sobrevivir a fallos de nodos individuales, siempre y cuando no ocurran a la vez. Para cargas de trabajo de producción, es necesario que haya resistencia para soportar fallos simultáneos, y por ello se necesitan cinco nodos.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>¿Puedo apagar mi clúster por la noche o los fines de semana para ahorrar costos?

En general, no. Service Fabric almacena el estado en discos efímeros locales, lo que quiere decir que si la máquina virtual se mueve a un host distinto, los datos no se mueven con ella. En el funcionamiento normal, esto no es un problema ya que otros nodos ponen al día al nuevo nodo. Pero, si detiene todos los nodos y los reinicia más tarde, existe una posibilidad importante de que la mayoría de los nodos se inicien en nuevos hosts y hagan que el sistema no pueda recuperar.

Si quiere crear clústeres para probar su aplicación antes de implementarla, le recomendamos que cree dinámicamente esos clústeres como parte de su [integración continua/ canalización de implementación continua](service-fabric-set-up-continuous-integration.md).

## <a name="container-support"></a>Compatibilidad con contenedores

### <a name="why-are-my-containers-that-are-deployed-to-sf-are-unable-to-resolve-dns-addresses"></a>¿Por qué son mis contenedores que se implementan en SF los que no pueden resolver direcciones DNS?

Este problema se ha informado en los clústeres de la versión 5.6.204.9494 

**Mitigación**: siga [este documento](service-fabric-dnsservice.md) para habilitar el servicio Service Fabric DNS en el clúster.

**Corregir**: actualizar a una versión del clúster compatible mayor que la 5.6.204.9494, cuando esté disponible. Si el clúster está establecido en actualizaciones automáticas, se actualizará automáticamente a la versión en este problema está corregido.

  
## <a name="application-design"></a>Diseño de aplicaciones

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>¿Cuál es la mejor forma de consultar datos entre las particiones de una instancia de Reliable Collection?

Las colecciones confiables están generalmente [particionadas](service-fabric-concepts-partitioning.md) para habilitar el escalado horizontal, y así conseguir un mayor rendimiento y capacidad de proceso. Esto significa que el estado de un servicio determinado puede distribuirse en decenas o cientos de máquinas. Para realizar operaciones sobre ese conjunto de datos completo, tiene varias opciones:

- Crear un servicio que realiza consultas en todas las particiones de otro servicio para extraer los datos necesarios.
- Crear un servicio que puede recibir datos de todas las particiones de otro servicio.
- Insertar datos periódicamente desde cada servicio a un almacén externo. Este método es adecuado solamente si las consultas que va a realizar no forman parte de su lógica de negocios troncal.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>¿Cuál es la mejor forma de consultar los datos a través de mis actores?

Los actores están diseñados para ser unidades independientes de estado y cálculo, por lo que no se recomienda realizar consultas amplias de estado de actor en tiempo de ejecución. Si tiene una necesidad de consulta en el conjunto completo de estado de actor, debe considerar una de estas dos opciones:

- Reemplazar los servicios de actor por servicios confiables con estado, como el número de solicitudes de red para recopilar todos los datos del número de actores para el número de particiones en el servicio.
- Diseñar los actores para insertar periódicamente su estado en un almacén externo y así facilitar las consultas. Como anteriormente, este método solo es viable si las consultas que está realizando no son necesarias para su comportamiento en tiempo de ejecución.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>¿Qué cantidad de datos puedo almacenar en una instancia de Reliable Collection?

Reliable Services normalmente tienen particiones, por lo que la cantidad que se puede almacenar está limitada únicamente por el número de máquinas que tiene en el clúster y la cantidad de memoria disponible en esas máquinas.

Por ejemplo, suponga que tiene una colección confiable de un servicio con 100 particiones y 3 réplicas, en donde se almacenan objetos que tienen un tamaño medio de 1 kB. Ahora suponga que tiene un clúster de 10 máquinas con 16 GB de memoria por máquina. Por simplificar y para mantener un enfoque conservador, vamos a asumir que el sistema operativo y los servicios del sistema, el tiempo de ejecución de Service Fabric y sus servicios consumen 6 GB, dejando 10 GB disponibles por máquina, o 100 GB para el clúster.

Teniendo en cuenta que cada objeto tiene que almacenarse tres veces (una principal y dos réplicas), debería tener suficiente memoria para aproximadamente 35 millones de objetos en la colección cuando se trabaja con la máxima capacidad. Sin embargo, se recomienda que cuente con la pérdida simultánea de un dominio de error y un dominio de actualización, lo que representa aproximadamente 1/3 de la capacidad y que podría reducir el número hasta aproximadamente a 23 millones.

Tenga en cuenta que en este cálculo también se da por supuesto:

- Que la distribución de datos en las particiones es aproximadamente uniforme o que informa sobre las métricas de carga en la instancia de Resource Manager del clúster. De forma predeterminada, Service Fabric equilibra la carga según el número de réplicas. En nuestro ejemplo anterior, se pondrían 10 réplicas principales y 20 réplicas secundarias en cada nodo del clúster. Esto funciona bien para las cargas que se distribuyen uniformemente en todas las particiones. Si carga no es uniforme, tiene que informar sobre ella para que Resource Manager pueda empaquetar juntas las réplicas más pequeñas y dejar que las réplicas mayores consuman más memoria en un nodo individual.

- Que el servicio confiable en cuestión es el único que almacena estado en el clúster. Dado que puede implementar varios servicios en un clúster, tiene que prestar atención a los recursos que cada uno de ellos necesita para ejecutar y administrar su estado.

- Que el propio clúster no experimente crecimiento o reducción. Si agrega más máquinas, Service Fabric reequilibra las réplicas para aprovechar la capacidad adicional hasta que el número de máquinas supere el número de particiones en el servicio, ya que una réplica individual no puede abarcar varias máquinas. Por el contrario, si reduce el tamaño del clúster mediante la eliminación de las máquinas, las réplicas se empaquetarán de forma más ajustada y tendrán menos capacidad total.

### <a name="how-much-data-can-i-store-in-an-actor"></a>¿Cuántos datos puedo almacenar en un actor?

Al igual que con Reliable Services, la cantidad de datos que se pueden almacenar en un servicio de actor solo está limitada por el espacio total disponible en disco y memoria en todos los nodos del clúster. Sin embargo, los actores individuales son más eficaces cuando se utilizan para encapsular una pequeña cantidad de estado y la lógica de negocio asociada. Como norma general, un actor individual debe tener un estado que se mida en kilobytes.

## <a name="other-questions"></a>Otras preguntas

### <a name="how-does-service-fabric-relate-to-containers"></a>¿Cómo se relacionan Service Fabric con los contenedores?

Los contenedores ofrecen una manera sencilla de empaquetar servicios y sus dependencias, de modo que se ejecuten de forma coherente en todos los entornos y puedan funcionar de forma aislada en una única máquina. Service Fabric ofrece una manera de implementar y administrar servicios, incluidos los [servicios empaquetados en un contenedor](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>¿Tienen previsto abrir el código de Service Fabric?

Se va a abrir el código de los entornos de servicios de confianza y actores confiables en GitHub, y se aceptarán las contribuciones de la comunidad a los proyectos. Siga el [blog de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) para obtener más información a medida que esté disponible.

Actualmente no hay planes para abrir el código del tiempo de ejecución de Service Fabric.

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre conceptos fundamentales de Service Fabric y procedimientos recomendados](https://mva.microsoft.com/en-us/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965)

