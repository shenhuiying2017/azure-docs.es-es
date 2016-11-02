<properties
   pageTitle="Arquitectura de Resource Manager | Microsoft Azure"
   description="Información general de la arquitectura de Service Fabric Cluster Resource Manager"
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

# Información general de la arquitectura del Administrador de recursos de clúster
Para administrar los recursos del clúster, Cluster Resource Manager de Service Fabric debe disponer de varios datos. Debe saber qué servicios existen actualmente y la cantidad actual (o predeterminada) de recursos que consumen esos servicios. También debe conocer la capacidad real de los nodos del clúster y, por lo tanto, la cantidad de recursos que están disponibles en el clúster como un conjunto y que quedan en un nodo concreto. Que el consumo de recursos de un servicio determinado puede cambiar a lo largo del tiempo y que los servicios suelen consumir más de un recurso. Entre muchos servicios diferentes, puede haber tanto recursos físicos reales que se estén midiendo y notificando como métricas, por ejemplo, el consumo de disco y memoria, como (y en realidad más comúnmente) métricas lógicas, cosas como "WorkQueueDepth" o "TotalRequests". Se pueden usar métricas lógicas y físicas en muchos tipos diferentes de servicios o de forma específica en solo un par de ellos.

## Otras consideraciones
Los propietarios y los operadores del clúster en ocasiones son distintos de los autores del servicio, o como mínimo son las mismas personas desempeñando funciones diferentes. Por ejemplo, cuando desarrolla su servicio sabe cuántos recursos requiere y cómo se deberían implementar idealmente los componentes, pero cuando debe controlar un incidente de un sitio activo para ese servicio en la producción, debe llevar a cabo otras tareas que requieren herramientas diferentes. Además, ni el clúster ni los servicios están configurados de manera estática: el número de nodos del clúster puede aumentar o reducirse, los nodos de diferentes tamaños pueden ir y venir, y los servicios pueden cambiar su asignación de recursos, crearse y eliminarse. Pueden producirse actualizaciones u otras operaciones de administración en el clúster y, por supuesto, en cualquier momento puede producirse un error.

## Componentes y flujo de datos del Administrador de recursos de clúster
Cluster Resource Manager tendrá que saber muchas cosas acerca del clúster en general, así como los requisitos de los servicios individuales y las instancias sin estado o las réplicas con estado que lo constituyen. Para ello, contamos con agentes de Cluster Resource Manager que se ejecutan en nodos individuales con el fin de agregar información sobre el consumo de recursos locales, y un servicio de Cluster Resource Manager centralizado y tolerante a errores que agrega toda la información sobre los servicios y el clúster reacciona en función de su configuración actual. La tolerancia a errores del servicio de Cluster Resource Manager (y de todos los demás servicios) se consigue exactamente con el mismo mecanismo que usamos para sus servicios, en concreto la replicación del estado del servicio para cuórums de un número cualquiera de réplicas del clúster (por lo habitual 7).

![Arquitectura del equilibrador de recursos][Image1]

Echemos un vistazo al diagrama anterior como ejemplo. En tiempo de ejecución, podrían producirse numerosos cambios. Por ejemplo, podría cambiar la cantidad de recursos que los servicios consumen, podrían producirse errores en el servicio, algunos nodos podrían unirse al clúster o abandonarlo, etc. Todos los cambios en un nodo específico se agregan y se envían periódicamente al servicio de Cluster Resource Manager (1, 2) donde se agregan de nuevo, se analizan y se almacenan. Cada pocos segundos, ese servicio examina todos los cambios y determina si es necesario llevar a cabo alguna acción (3). Por ejemplo, podría detectar que se han agregado al clúster nodos vacíos y decidir mover algunos servicios a dichos nodos. También podría detectar que un nodo en concreto está sobrecargado, o bien que se ha producido un error en determinados servicios (o que se han eliminado), y liberar los recursos de otros nodos.

Vamos a examinar el siguiente diagrama y ver lo que sucede a continuación. Supongamos que Cluster Resource Manager determina que se necesitan cambios. Coordina con otros servicios del sistema (en concreto, con el Administrador de conmutación por error) la realización de los cambios necesarios. A continuación, se envían las solicitudes de cambio a los nodos correspondientes (4). En este caso, se supone que Resource Manager ha detectado la sobrecarga en el nodo 5 y, por lo tanto, ha decidido cambiar el servicio B de N5 a N4. Al final de la reconfiguración (5), el clúster tiene el siguiente aspecto:

![Arquitectura del equilibrador de recursos][Image2]

## Pasos siguientes
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0824_2016-->