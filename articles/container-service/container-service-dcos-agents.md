---
title: "ACS de grupos de agentes de DC/OS públicos y privados | Microsoft Docs"
description: "Cómo funcionan los grupos de agentes públicos y privados con un clúster de Azure Container Service."
services: container-service
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Mesos, Azure
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Grupos de agentes de DC/OS para Azure Container Service
Azure Container Service en DC/OS divide los agentes en grupos públicos o privados. Una implementación puede realizarse en cualquier grupo, de tal manera que afecte a la accesibilidad entre máquinas en el servicio de contenedor. Las máquinas pueden estar expuestas a Internet (públicas) o mantenerse internas (privadas). En este artículo se ofrece una breve descripción de por qué hay un grupo público y privado.

### <a name="private-agents"></a>Agentes privados
Los nodos de agente privado se ejecutan a través de una red no enrutable. Esta red solo es accesible desde la zona de administración o a través del enrutador de borde de la zona pública. De forma predeterminada, DC/OS inicia aplicaciones en nodos de agente privado. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información acerca de la seguridad de red.

### <a name="public-agents"></a>Agentes públicos
Los nodos de agente público ejecutan servicios y aplicaciones de DC/OS a través de una red de acceso público. Consulte la [documentación de DC/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) para obtener más información acerca de la seguridad de red.

## <a name="using-agent-pools"></a>Uso de grupos de agente
De forma predeterminada, **Marathon** implementa cualquier nueva aplicación en los nodos de agente *privado* . Tendrá que implementar explícitamente la aplicación en el nodo *público* durante la creación de la aplicación. Seleccione la pestaña **Opcional** y escriba **slave_public** en el valor **Accepted Resource Roles** (Roles de recursos aceptados). Este proceso se describe [aquí](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) y en la documentación de [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo [administrar contenedores de DC/OS](container-service-mesos-marathon-ui.md).

Obtenga información sobre cómo [abrir el firewall](container-service-enable-public-access.md) proporcionado por Azure para permitir acceso público al contenedor de DC/OS.




<!--HONumber=Nov16_HO3-->


