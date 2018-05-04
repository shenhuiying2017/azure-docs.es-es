---
title: Visualización de los registros de contenedor en Azure Service Fabric | Microsoft Docs
description: Describe cómo ver los registros de contenedor para servicios de contenedor de Service Fabric en ejecución utilizando Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Visualización de registros para un servicio de contenedor de Service Fabric
Azure Service Fabric es un orquestador de contenedores que admite [contenedores Linux y Windows](service-fabric-containers-overview.md).  Este artículo describe cómo ver los registros de contenedor de un servicio de contenedor en ejecución para así poder realizar diagnósticos y solucionar problemas.

## <a name="access-container-logs"></a>Acceso a los registros de contenedores
Registros de contenedor se pueden acceder mediante [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  En un explorador web, vaya a [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer) para abrir Service Fabric Explorer desde el punto de conexión de administración del clúster.  

Los registros de contenedor se encuentran en el nodo de clúster en el que se ejecuta la instancia de servicio de contenedor. Como ejemplo, obtenga los registros del contenedor de front-end web de la [aplicación de ejemplo de votación de Linux](service-fabric-quickstart-containers-linux.md). En la vista de árbol, expanda **Cluster**> **Applications (Aplicaciones)**> **VotingType**>**fabric:/Voting/azurevotefront**.  A continuación, expanda la partición (d1aa737e-f22a-e347-be16-eec90be24bc1, en este ejemplo) y compruebe que el contenedor se está ejecutando en el nodo de clúster *_lnxvm_0*.

En la vista de árbol, busque el paquete de código en el nodo  *_lnxvm_0* expandiendo **Nodes (nodos)**>**_lnxvm_0**>**fabric:/Voting**> **azurevotfrontPkg**>**Code Packages (Paquetes de código)**>**code (código)**.  A continuación, seleccione la opción **Registros de contenedor** para que se muestren los registros de contenedor.

![Plataforma de Service Fabric][Image1]


## <a name="next-steps"></a>Pasos siguientes
- Trabaje en el [tutorial para la creación de una aplicación contenedora en Linux](service-fabric-tutorial-create-container-images.md).
- Más información acerca de [Service Fabric y los contenedores](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
