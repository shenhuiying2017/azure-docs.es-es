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
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f0d2202c17bf4d378a625a61e941edf7f3f24636
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="cluster-resource-manager-architecture-overview"></a>Información general de la arquitectura del Administrador de recursos de clúster
Cluster Resource Manager de Service Fabric es un servicio centralizado que se ejecuta en el clúster. Administra el estado deseado de los servicios del clúster, concretamente con respecto al consumo de recursos y las reglas de selección de ubicación. 

Para administrar los recursos del clúster, Cluster Resource Manager de Service Fabric debe disponer de varios datos:

- Qué servicios existen actualmente
- Consumo de recursos actual (o predeterminado) de cada servicio 
- Capacidad restante del clúster 
- Capacidad de los nodos del clúster 
- Cantidad de recursos consumidos en cada nodo

El consumo de recursos de un servicio determinado puede cambiar con el tiempo y los servicios normalmente se ocupan de más de un tipo de recurso. En los distintos servicios, es posible que se midan tanto los recursos lógicos como los recursos físicos reales. Los servicios pueden hacer seguimiento de métricas físicas, como el consumo de disco y memoria. Es más frecuentes que los servicios se ocupen de las métricas lógicas, como "WorkQueueDepth" o "TotalRequests". Se pueden usar métricas lógicas y físicas en un mismo clúster. Las métricas pueden compartirse entre varios servicios o ser específicas de un servicio concreto.

## <a name="other-considerations"></a>Otras consideraciones
Los propietarios y operadores del clúster pueden ser distintos de los autores de los servicios y las aplicaciones o, por lo menos, tienen distintas responsabilidades. Hay algunas cosas que se saben sobre lo que necesita la aplicación al desarrollarla. Se tiene un cálculo aproximado de los recursos que consumirá y se sabe cómo deben implementarse los distintos servicios. Por ejemplo, el nivel web tiene que ejecutarse en nodos expuestos en Internet, mientras que los servicios de base de datos, no. Otro ejemplo puede ser que los servicios web probablemente estén restringidos por la CPU y la red, mientras que los servicios del nivel de datos dan más importancia al consumo de memoria y de disco. Aunque la persona que controla un incidente en un sitio activo de ese servicio en producción o que administra una actualización del servicio tiene un trabajo distinto que realizar y necesita herramientas distintas. 

El clúster y los servicios son dinámicos:

- La cantidad de nodos del clúster puede aumentar o disminuir.
- Los nodos de distintos tipos y tamaños pueden ir y venir.
- Se pueden crear y quitar servicios, así como cambiarles las asignaciones de recursos y las reglas de selección de ubicación deseadas.
- Pueden producirse actualizaciones u otras operaciones de administración en los niveles de aplicación o de infraestructura del clúster.
- Pueden generarse errores en cualquier momento.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Componentes y flujo de datos del Administrador de recursos de clúster
Cluster Resource Manager debe hacer seguimiento de los requisitos de cada servicio y el consumo de recursos que cada objeto de servicio hace en esos servicios. Cluster Resource Manager tiene dos partes conceptuales: los agentes que se ejecutan en cada nodo y un servicio tolerante a errores. Los agentes de cada nodo hacen seguimiento de los informes de carga de los servicios, los suman e informan de manera periódica. El servicio Cluster Resource Manager suma toda la información de los agentes locales y reacciona en función de su configuración actual.

Observemos el diagrama siguiente:

<center>
![Arquitectura del equilibrador de recursos][Image1]
</center>

Durante el tiempo de ejecución, pueden ocurrir muchos cambios. Por ejemplo, supongamos que la cantidad de recursos que algunos servicios consumen cambia, algunos servicios presentan errores y algunos nodos se unen y abandonan el clúster. Todos los cambios en un nodo se agregan y se envían periódicamente al servicio de Cluster Resource Manager (1, 2) donde se agregan de nuevo, se analizan y se almacenan. Cada pocos segundos, ese servicio examina los cambios y determina si es necesario llevar a cabo alguna acción (3). Por ejemplo, podría detectar que se agregaron nodos vacíos al clúster. Por consiguiente, decide mover algunos servicios a esos nodos. Cluster Resource Manager también podría detectar que un nodo en concreto está sobrecargado, o bien que determinados servicios no han funcionado o se han eliminado, y liberar recursos de otros lugares.

Examinemos el siguiente diagrama y veamos qué sucede a continuación. Supongamos que Cluster Resource Manager determina que se necesitan cambios. Coordina con otros servicios del sistema (en concreto, con el Administrador de conmutación por error) la realización de los cambios necesarios. A continuación, los comandos necesarios se envían a los nodos correspondientes (4). Por ejemplo, digamos que Resource Manager ha detectado que Node5 está sobrecargado y por eso ha decidido mover el servicio B de Node5 a Node4. Al final de la reconfiguración (5), el clúster tiene el siguiente aspecto:

<center>
![Arquitectura del equilibrador de recursos][Image2]
</center>

## <a name="next-steps"></a>Pasos siguientes
- Cluster Resource Manager tiene muchas opciones para describir el clúster. Para obtener más información sobre ellas, consulte este artículo [Descripción de un clúster de Service Fabric](./service-fabric-cluster-resource-manager-cluster-description.md).
- Las principales tareas de Cluster Resource Manager son el reequilibrado del clúster y la aplicación de reglas de selección de ubicación. Para más información sobre cómo configurar estos comportamientos, vea [Equilibrio del clúster de Service Fabric](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
