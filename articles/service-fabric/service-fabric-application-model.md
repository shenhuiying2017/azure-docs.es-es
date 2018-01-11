---
title: "Modelo de aplicación de Azure Service Fabric | Microsoft Docs"
description: "Cómo modelar y describir las aplicaciones y servicios en Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: ryanwi
ms.openlocfilehash: d5f6fbb9d9c0bc0d9762f8d6b4b4eb3b02d29adc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>Modelar una aplicación en Service Fabric
En este artículo, se proporciona información general sobre el modelo de aplicación de Azure Service Fabric y se describe el modo de definir una aplicación y un servicio a través de archivos de manifiesto.

## <a name="understand-the-application-model"></a>Entender el modelo de aplicación
Una aplicación es una colección de servicios constituyentes que realizan una determinada función o funciones. Un servicio realiza una función completa e independiente, y puede iniciarse y ejecutarse independientemente de otros servicios.  Un servicio se compone de código, configuración y datos. Para cada servicio, el código consta de los archivos binarios ejecutables, la configuración consta de una configuración del servicio que se puede cargar en tiempo de ejecución y los datos constan de datos estáticos arbitrarios que el servicio va a consumir. Cada componente de este modelo de aplicación jerárquico puede tener varias versiones y actualizarse independientemente.

![Modelo de aplicación de Service Fabric][appmodel-diagram]

Un tipo de aplicación es una categorización de una aplicación, que consta de un conjunto de tipos de servicio. Un tipo de servicio es una categorización de un servicio. La categorización puede tener una configuración diferente, pero cuya funcionalidad básica sigue siendo la misma. Las instancias de un servicio son las distintas variaciones de la configuración del servicio del mismo tipo de servicio.  

Las clases (o "tipos") de aplicaciones y servicios se describen a través de archivos XML (manifiestos de aplicación y de servicio).  Los manifiestos describen los servicios y las aplicaciones y son las plantillas sobre las que se pueden crear instancias de las aplicaciones desde el almacén de imágenes del clúster.  Los manifiestos se tratan detalladamente en [Application and service manifests](service-fabric-application-and-service-manifests.md) (Manifiestos de servicio y aplicación). La definición de esquema para los archivos ServiceManifest.xml y ApplicationManifest.xml se instala con el SDK y las herramientas de Service Fabric en *C:\Archivos de programa\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. El esquema XML se documenta en la [documentación del esquema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

El código de las diversas instancias de aplicación se ejecuta como procesos independientes, incluso cuando lo hospede el mismo nodo de Service Fabric. Además, el ciclo de vida de cada instancia de aplicación se puede administrar (por ejemplo, actualizar) de forma independiente. En el siguiente diagrama se muestra cómo los tipos de aplicación constan de tipos de servicio, que a su vez constan de código, configuración y paquetes de datos. Para simplificar el diagrama, solo se muestran los paquetes code/config/data para `ServiceType4`, aunque cada tipo de servicio podría incluir algunos o todos esos tipos de paquetes.

![Tipos de aplicaciones de Service Fabric y tipos de servicio][cluster-imagestore-apptypes]

Puede haber una o más instancias de un tipo de servicio activas en el clúster. Por ejemplo, las instancias de servicio con estado o réplicas logran una alta confiabilidad mediante la replicación del estado entre réplicas ubicadas en distintos nodos del clúster. La replicación esencialmente proporciona redundancia para que el servicio esté disponible, incluso si se produce un error en un nodo de un clúster. Un [servicio con particiones](service-fabric-concepts-partitioning.md) divide aún más su estado (y patrones de acceso a ese estado) entre los nodos del clúster.

En el siguiente diagrama se muestra la relación entre aplicaciones e instancias de servicio, particiones y réplicas.

![Particiones y réplicas dentro de un servicio][cluster-application-instances]

> [!TIP]
> Puede ver el diseño de las aplicaciones en un clúster mediante la herramienta Service Fabric Explorer disponible en http://&lt;direcciónDelClúster&gt;:19080/Explorer. Para más información, vea [Visualización del clúster mediante Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre [escalabilidad de aplicaciones](service-fabric-concepts-scalability.md).
- Obtenga información acerca del [estado](service-fabric-concepts-state.md), las [particiones](service-fabric-concepts-partitioning.md) y la [disponibilidad](service-fabric-availability-services.md) del servicio.
- Obtenga información sobre cómo se definen los servicios y las aplicaciones en [Application and service manifests](service-fabric-application-and-service-manifests.md) (Manifiestos de servicio y aplicación).
- Los [Modelos de hospedaje de aplicaciones](service-fabric-hosting-model.md) describen la relación entre las réplicas o las instancias de un servicio implementado y el proceso de host de servicio.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


