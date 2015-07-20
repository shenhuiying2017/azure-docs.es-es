<properties
   pageTitle="Arquitectura del equilibrador de recursos"
   description="Introducción a la arquitectura del equilibrador de recursos de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Información general de arquitectura de equilibrador de recursos

![Arquitectura del equilibrador de recursos][Image1]

El equilibrador de recursos de Service Fabric consta de un único servicio de equilibrio de recursos centralizado y un componente que existe en todos los nodos, que están conceptualmente colocados con el Administrador de conmutación por error de Service Fabric y con el nodo de Service Fabric local, respectivamente.

El agente local es responsable de recopilar y almacenar en búfer los informes de carga de los servicios que se ejecutan en el nodo local, para enviarlos al servicio del equilibrador de recursos y también para los informes de errores y otros eventos al Administrador de conmutación por error y al equilibrador de recursos (1 y 2 anteriores). El servicio del equilibrador de recursos y el Administrador de conmutación por error colaboran para responder a eventos de carga y otros eventos del sistema, como errores de réplica o nodo, informes de carga y servicios y aplicaciones que se crean y eliminan. Por ejemplo, cuando se produce un error en una réplica, el Administrador de conmutación por error solicita que el equilibrador de recursos de Service Fabric sugiera una ubicación para el reemplazo que se basa en los datos de carga de los distintos nodos. De forma similar, cuando se recibe una solicitud de servicio nuevo, el Administrador de conmutación por error solicita recomendaciones desde el equilibrador de recursos acerca de dónde colocar ese servicio. En todos estos casos, el equilibrador de recursos responde con cambios en varias configuraciones de servicio (3) que, a continuación, se llevan a cabo por el Administrador de conmutación por error. En el ejemplo anterior, el Administrador de conmutación por error crea una nueva réplica en el nodo, lo que produce el mejor equilibrio general (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Características de equilibrio de recursos:

- [Describir el clúster](service-fabric-resource-balancer-cluster-description.md)
- [Describir servicios](service-fabric-resource-balancer-service-description.md)
- [Informe de carga dinámica](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Empaquetado de métrica proactivo](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Porcentaje de búfer de nodo](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png
 

<!---HONumber=July15_HO2-->