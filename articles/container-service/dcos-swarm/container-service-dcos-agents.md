---
title: Grupos de agentes de DC/OS para Azure Container Service | Microsoft Docs
description: "Funcionamiento de los grupos de agentes públicos y privados con un clúster de DC/OS de Azure Container Service."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: da4a196b1a73c78dfff7d8310edcc349b8d10665
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Grupos de agentes de DC/OS para Azure Container Service
Los clústeres de DC/OS en Azure Container Service contienen nodos de agente en dos grupos, uno público y otro privado. Una aplicación se puede implementar en cualquier grupo, lo que afecta a la accesibilidad entre las máquinas del servicio de contenedores. Las máquinas pueden estar expuestas a Internet (públicas) o mantenerse internas (privadas). En este artículo se ofrece una breve descripción de por qué hay grupos públicos y privados.


* **Agentes privados**: los nodos de agentes privados se ejecutan mediante una red no enrutable. Esta red solo es accesible desde la zona de administración o a través del enrutador de borde de la zona pública. De forma predeterminada, DC/OS inicia aplicaciones en nodos de agente privado. 

* **Agentes públicos**: los nodos de agentes públicos ejecutan servicios y aplicaciones de DC/OS por medio de una red de acceso público. 

Para más información sobre la seguridad de red de DC/OS, consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Implementación de grupos de agentes

Los grupos de agentes de DC/OS de Azure Container Service se crean de la manera siguiente:

* El **grupo privado** contiene el número de nodos de agente que especifica cuando [implementa el clúster de DC/OS](container-service-deployment.md). 

* El **grupo público** contiene inicialmente un número predeterminado de nodos de agente. Este grupo se agrega automáticamente cuando se aprovisiona el clúster de DC/OS.

El grupo privado y el grupo público son conjuntos de escalado de máquinas virtuales de Azure. Puede cambiar el tamaño de estos grupos después de la implementación.

## <a name="use-agent-pools"></a>Uso de grupos de agentes
De forma predeterminada, **Marathon** implementa cualquier nueva aplicación en los nodos de agente *privado* . Tendrá que implementar explícitamente la aplicación en el nodo *público* durante la creación de la aplicación. Seleccione la pestaña **Opcional** y escriba **slave_public** en el valor **Accepted Resource Roles** (Roles de recursos aceptados). Este proceso se describe [aquí](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) y en la documentación de [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## <a name="next-steps"></a>Pasos siguientes
* Lea sobre cómo [administrar los contenedores de DC/OS](container-service-mesos-marathon-ui.md).

* Aprenda a [abrir el firewall](container-service-enable-public-access.md) proporcionado por Azure para permitir acceso público al contenedor de DC/OS.

