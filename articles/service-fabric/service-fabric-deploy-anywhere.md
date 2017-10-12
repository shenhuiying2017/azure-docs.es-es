---
title: "Creación de clústeres de Service Fabric de Azure en Windows Server y Linux | Microsoft Docs"
description: "Los clústeres de Service Fabric se ejecutan en Windows Server y Linux, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric en cualquier lugar donde sea posible ejecutar Windows Server y Linux."
services: service-fabric
documentationcenter: .net
author: Chackdan
manager: timlt
editor: 
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: chackdan
ms.openlocfilehash: e3cfad19e42af24edd68befd7b1eac8cef41a1d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Creación de clústeres de Service Fabric en Windows Server o Linux
Un clúster de Azure Service Fabric es un conjunto de máquinas físicas o virtuales conectadas a la red en las que se implementan y administran los microservicios. Una máquina física o virtual que forma parte de un clúster se denomina nodo del clúster. Los clústeres pueden escalarse a miles de nodos. Si agrega nuevos nodos al clúster, Service Fabric reequilibra las réplicas e instancias de la partición del servicio en el número aumentado de nodos. El rendimiento general de la aplicación mejora y se reduce la contención para el acceso a la memoria. Si los nodos del clúster no se usan de forma eficaz, puede reducir su número de nodos. Service Fabric vuelve a reequilibrar las réplicas e instancias de la partición en el número reducido de nodos para aprovechar mejor el hardware de cada nodo.

Service Fabric permite la creación de clústeres de Service Fabric en cualquier máquina virtual o equipo con Windows Server o Linux. Esto significa que podrá implementar y ejecutar aplicaciones de Service Fabric en cualquier entorno donde haya un conjunto de equipos de Windows Server o Linux que estén conectados entre sí, ya sea de manera local, en Microsoft Azure o con algún proveedor en la nube.

## <a name="create-service-fabric-clusters-on-azure"></a>Creación de clústeres de Service Fabric en Azure
La creación de un clúster en Azure debe realizarse mediante una plantilla del modelo de recursos o en [Azure Portal](https://portal.azure.com). Lea [Creación de un clúster de Service Fabric en Azure mediante Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) o [Creación de un clúster de Service Fabric en Azure mediante Azure Portal](service-fabric-cluster-creation-via-portal.md) para más información.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operativos compatibles con clústeres en Azure
Puede crear clústeres en máquinas virtuales que ejecuten estos sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 
* Linux Ubuntu 16.04  

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Creación de clústeres independientes de Service Fabric de forma local o con cualquier proveedor de nube
Service Fabric proporciona un paquete de instalación para que cree clústeres independientes de Service Fabric de forma local o en cualquier proveedor en la nube.

Para más información sobre la configuración de clústeres independientes de Service Fabric en Windows Server, lea [Creación de un clúster de Service Fabric para Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Actualmente, los clústeres independientes no se admiten para Linux. Linux se admite solo en one-box para el desarrollo y en clústeres de varios equipos Linux de Azure.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Implementaciones en la nube frente a implementaciones locales
El proceso de creación de un clúster local de Service Fabric es similar al proceso de creación de un clúster en cualquier nube que elija donde cuente con un conjunto de máquinas virtuales. Los pasos iniciales para aprovisionar las máquinas virtuales se regirán por el proveedor de la nube o el entorno local que use. Cuando tenga un conjunto de máquinas virtuales con la conectividad de red habilitada entre ellas, los pasos para configurar el paquete de Service Fabric, editar la configuración del clúster y ejecutar la creación del clúster y los scripts de administración son idénticos. Esto garantiza que sus conocimientos y su experiencia con respecto al uso y la administración de los clústeres de Service Fabric se puedan aplicar si decide cambiar a un nuevo entorno de hospedaje.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Ventajas de crear clústeres independientes de Service Fabric
* Es libre de elegir cualquier proveedor en la nube para hospedar el clúster.
* Las aplicaciones de Service Fabric, una vez escritas, se pueden ejecutar en varios entornos de hospedaje con pocos cambios (o ninguno).
* El proceso de creación de aplicaciones de Service Fabric se aplica de un entorno de hospedaje a otro.
* La experiencia operativa que se adquiere al ejecutar y administrar clústeres de Service Fabric se puede aplicar de un entorno a otro.
* Existe una amplia cobertura de clientes sin limitaciones derivadas de las restricciones del entorno de hospedaje.
* Hay un nivel adicional de confiabilidad y protección contra interrupciones generalizadas al poder mover los servicios a otro entorno de implementación en caso de que un centro de datos o un proveedor de nube sufran una interrupción.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operativos compatibles con clústeres independientes
Podrá crear clústeres en VM o equipos que ejecuten estos sistemas operativos (aún no se admite Linux):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Ventajas de los clústeres de Service Fabric en Azure frente a los clústeres independientes de Service Fabric creados de forma local
La ejecución de clústeres de Service Fabric en Azure proporciona ventajas frente a la opción local. Por tanto, si no tiene necesidades específicas en cuanto al lugar donde se ejecutan los clústeres, le sugerimos que los ejecute en Azure. En Azure se proporciona integración con otras características y servicios de Azure, lo que facilita las operaciones y la administración del clúster y hace que sea más confiable.

* **Portal de Azure:** el Portal de Azure facilita la creación y la administración de clústeres.
* **Administrador de recursos de Azure:** el uso del Administrador de recursos de Azure permite una administración sencilla de todos los recursos que usa el clúster como una unidad y simplifica el costo del seguimiento y la facturación.
* **Clúster de Service Fabric como recurso de Azure** Un clúster de Service Fabric es un recurso de Azure, de modo que puede ajustarlo como hace con otros recursos en Azure.
* **Integración con la infraestructura de Azure** : Service Fabric se coordina con la infraestructura de Azure subyacente para el SO, la red y otras actualizaciones con el fin de mejorar la disponibilidad y la confiabilidad de las aplicaciones.  
* **Diagnósticos:** en Azure se proporciona la integración con Diagnósticos y Log Analytics de Azure.
* **Escalado automático:** para los clústeres hospedados en Azure, proporcionamos una funcionalidad integrada de escalado automático debido a los conjuntos de escalas de la máquina virtual. En los entornos locales y otros entornos en la nube, hay que crear una característica propia de escalado automático o realizar el escalado manualmente mediante las API que ofrece Service Fabric para escalar los clústeres.

## <a name="next-steps"></a>Pasos siguientes

* Creación de un clúster en máquinas virtuales o equipos que ejecutan Windows Server: [Creación y administración de un clúster que se ejecute en Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creación de un clúster en VM o equipos que ejecutan Linux: [Crear un clúster Linux](service-fabric-cluster-creation-via-portal.md)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

