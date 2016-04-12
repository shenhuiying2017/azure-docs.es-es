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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Información general de la arquitectura del Administrador de recursos de clúster
Para administrar los recursos del clúster, Cluster Resource Manager debe disponer de varios datos. Debe saber qué servicios existen actualmente y la cantidad actual (o predeterminada) de recursos que consume un servicio. También debe conocer la capacidad real de los nodos del clúster y, por lo tanto, la cantidad de recursos que están disponibles en el clúster como un conjunto y que quedan en un nodo concreto. Debemos tener en cuenta que el consumo de recursos de un servicio determinado puede cambiar a lo largo del tiempo y que los servicios suelen consumir más de un recurso. Entre los diferentes servicios puede haber recursos "reales", como el consumo de memoria y de disco, que suelen usar diversos tipos de servicios, así como recursos que solo consume un servicio en particular.

## Otras consideraciones
Para complicar más las cosas, los propietarios y los operadores del clúster en ocasiones son distintos de los autores del servicio, o como mínimo son las mismas personas desempeñando funciones diferentes. Por ejemplo, cuando desarrolla su servicio sabe cuántos recursos requiere y cómo se deberían implementar idealmente los componentes, pero cuando debe controlar un incidente de un sitio activo para ese servicio en la producción, debe llevar a cabo otras tareas que requieren herramientas diferentes. Además, ni el clúster ni los servicios están configurados de manera estática: el número de nodos del clúster puede aumentar o reducirse, los nodos de diferentes tamaños pueden ir y venir, y los servicios pueden cambiar su asignación de recursos, crearse y eliminarse. Pueden producirse actualizaciones u otras operaciones de administración en el clúster y, por supuesto, en cualquier momento puede producirse un error.

## Componentes y flujo de datos del Administrador de recursos de clúster
El administrador de recursos tendrá que conocer muchos datos del clúster en general, así como los requisitos de determinados servicios. Para ello, en Service Fabric, tenemos los agentes de Resource Manager, que se ejecutan en nodos individuales para agregar información sobre el consumo de recursos locales, y un servicio de Resource Manager centralizado y con tolerancia a errores, que agrega toda la información sobre los servicios y el clúster y responde a los cambios en función de la configuración de estado deseado del clúster y el servicio. La tolerancia a errores se logra a través de exactamente el mismo mecanismo que seguimos para los servicios, es decir, la replicación del estado del servicio en un número de réplicas (normalmente 7).

![Arquitectura del equilibrador de recursos][Image1]

Echemos un vistazo al diagrama anterior como ejemplo. En tiempo de ejecución, podrían producirse numerosos cambios. Por ejemplo, podría cambiar la cantidad de recursos que los servicios consumen, podrían producirse errores en el servicio, algunos nodos podrían unirse al clúster o abandonarlo, etc. Todos los cambios en un nodo específico se agregan y se envían periódicamente al servicio central de Resource Manager (1, 2) donde se agregan de nuevo, se analizan y se almacenan. Cada pocos segundos, ese servicio central examina todos los cambios y determina si es necesario llevar a cabo alguna acción (3). Por ejemplo, podría detectar que se han agregado al clúster nodos vacíos y decidir mover algunos servicios a dichos nodos. También podría detectar que un nodo en concreto está sobrecargado, o bien que se ha producido un error en determinados servicios (o que se han eliminado), y liberar los recursos de otros nodos.

Vamos a echar un vistazo al siguiente diagrama y ver lo que sucede en este ejemplo. Supongamos que el Administrador de recursos determina que se necesitan cambios. Coordina con otros servicios del sistema (en concreto, con el Administrador de conmutación por error) la realización de los cambios necesarios. A continuación, se envían las solicitudes de cambio a los nodos correspondientes (4). En este caso, se supone que Resource Manager ha detectado la sobrecarga en el nodo 5 y, por lo tanto, ha decidido cambiar el servicio B de N5 a N4. Al final de la reconfiguración (5), el clúster tiene el siguiente aspecto:

![Arquitectura del equilibrador de recursos][Image2]

## Pasos siguientes
- El Administrador de recursos de clúster tiene muchas opciones para describir el clúster. Para más información sobre ellas, consulte este artículo: [Describing a service fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) (Descripción de un clúster de Service Fabric).

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0316_2016-->