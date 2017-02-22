---
title: Arquitectura de Resource Manager | Microsoft Docs
description: "Información general de la arquitectura de Service Fabric Cluster Resource Manager"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 67f07bad8f6f89d9e5e68326f0cc194d920e841b


---
# <a name="cluster-resource-manager-architecture-overview"></a>Información general de la arquitectura del Administrador de recursos de clúster
Para administrar los recursos del clúster, Cluster Resource Manager de Service Fabric debe disponer de varios datos. Debe saber qué servicios existen actualmente y la cantidad actual (o predeterminada) de recursos que consumen esos servicios. Para hacer seguimiento de los recursos disponibles en el clúster, debe saber la capacidad de los nodos del clúster y la cantidad de recursos que consume cada uno. El consumo de recursos de un servicio determinado puede cambiar con el tiempo y los servicios normalmente se ocupan de más de un tipo de recurso. En los distintos servicios, es posible que se midan tanto los recursos lógicos como los recursos físicos reales. Los servicios pueden hacer seguimiento de métricas físicas, como el consumo de disco y memoria. Es más frecuentes que los servicios se ocupen de las métricas lógicas, como "WorkQueueDepth" o "TotalRequests". Se pueden usar métricas lógicas y físicas en muchos tipos diferentes de servicios o de forma específica en solo un par de ellos.

## <a name="other-considerations"></a>Otras consideraciones
Los propietarios y operadores del clúster habitualmente son distintos de los autores del servicio o, como mínimo, son los mismos usuarios con roles distintos. Por ejemplo, cuando desarrolla su servicio puede saber algo de información sobre lo que se necesita en términos de recursos y cómo se implementan idealmente los distintos componentes. Sin embargo, la persona que controla un incidente en un sitio activo de ese servicio en producción tiene un trabajo distinto y necesita herramientas diferentes. Además, ni el clúster ni los servicios están configurados de forma estática:

* La cantidad de nodos del clúster puede aumentar o disminuir.
* Los nodos de distintos tipos y tamaños pueden ir y venir.
* Los servicios pueden crear, quitar y cambiar las asignaciones de recursos que desean.
* Pueden producirse actualizaciones u otras operaciones de administración en el clúster y en cualquier momento puede producirse un error.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes y flujo de datos del Administrador de recursos de clúster
Cluster Resource Manager debe hacer seguimiento de los requisitos de los servicios individuales y el consumo de recursos por parte de los objetos de servicio individuales que conforman esos servicios. Para ello, Cluster Resource Manager tiene dos partes conceptuales: los agentes que se ejecutan en cada nodo y un servicio tolerante a errores. Los agentes de cada nodo hacen seguimiento de los informes de carga de los servicios, los suman e informan de manera periódica. El servicio Cluster Resource Manager suma toda la información de los agentes locales y reacciona en función de su configuración actual.

Observemos el diagrama siguiente:

<center>
![Arquitectura del equilibrador de recursos][Image1]
</center>

Durante el tiempo de ejecución, pueden ocurrir muchos cambios. Por ejemplo, supongamos que la cantidad de recursos que algunos servicios consumen cambia, algunos servicios presentan errores y algunos nodos se unen y abandonan el clúster. Todos los cambios en un nodo se agregan y se envían periódicamente al servicio de Cluster Resource Manager (1,&2;) donde se agregan de nuevo, se analizan y se almacenan. Cada pocos segundos, ese servicio examina los cambios y determina si es necesario llevar a cabo alguna acción (3). Por ejemplo, podría detectar que se agregaron nodos vacíos al clúster y decidir mover algunos servicios a dichos nodos. Cluster Resource Manager también podría detectar que un nodo en concreto está sobrecargado, o bien que se ha producido un error en determinados servicios (o que se han eliminado), y liberar los recursos de otros lugares.

Examinemos el siguiente diagrama y veamos qué sucede a continuación. Supongamos que Cluster Resource Manager determina que se necesitan cambios. Coordina con otros servicios del sistema (en concreto, con el Administrador de conmutación por error) la realización de los cambios necesarios. A continuación, los comandos necesarios se envían a los nodos correspondientes (4). En este caso, se supone que Resource Manager ha detectado la sobrecarga en el nodo 5 y, por lo tanto, ha decidido cambiar el servicio B de N5 a N4. Al final de la reconfiguración (5), el clúster tiene el siguiente aspecto:

<center>
![Arquitectura del equilibrador de recursos][Image2]
</center>

## <a name="next-steps"></a>Pasos siguientes
* Cluster Resource Manager tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo sobre cómo [describir un clúster de Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png



<!--HONumber=Jan17_HO1-->


