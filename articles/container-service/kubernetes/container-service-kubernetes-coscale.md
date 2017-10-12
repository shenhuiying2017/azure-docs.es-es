---
title: "Supervisión de un clúster de Azure Kubernetes con CoScale | Microsoft Docs"
description: "Supervisión de un clúster de Kubernetes en Azure Container Service mediante CoScale"
services: container-service
documentationcenter: 
author: fryckbos
manager: 
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f894191baced710fc0f5a8c8692df98033341a48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>Supervisión de un clúster de Kubernetes de Azure Container Service con CoScale

En este artículo, le mostramos cómo implementar el agente de [CoScale](https://www.coscale.com/) para supervisar todos los nodos y contenedores un clúster de Kubernetes en Azure Container Service. Para esta configuración se necesita una cuenta con CoScale. 


## <a name="about-coscale"></a>Acerca de CoScale 

CoScale es una plataforma de supervisión que recopila métricas y eventos de todos los contenedores de varias plataformas de orquestación. CoScale ofrece supervisión de toda la pila para los entornos de Kubernetes. Proporciona visualizaciones y análisis a todas las capas de la pila: el sistema operativo, Kubernetes, Docker y aplicaciones que se ejecutan en los contenedores. CoScale ofrece varios paneles de supervisión integrados y tiene la función de detección de anomalías integrada para permitir a los operadores y los desarrolladores encontrar rápidamente problemas en la infraestructura y las aplicaciones.

![Interfaz de usuario de CoScale](./media/container-service-kubernetes-coscale/coscale.png)

Como se muestra en este artículo, se pueden instalar agentes en un clúster de Kubernetes para ejecutar CoScale como solución de SaaS. Si desea conservar los datos en un entorno local, CoScale también estará disponible para su instalación local.


## <a name="prerequisites"></a>Requisitos previos

En primer lugar, es preciso [crear una cuenta de CoScale](https://www.coscale.com/free-trial).

En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

También se asume que tiene la CLI de Azure `az` y las herramientas de `kubectl` instaladas.

Puede probar si tiene la herramienta `az` instalada mediante la ejecución de:

```azurecli
az --version
```

Si no tiene la herramienta `az` instalada, se ofrecen instrucciones [aquí](/cli/azure/install-azure-cli).

Puede probar si tiene la herramienta `kubectl` instalada mediante la ejecución de:

```bash
kubectl version
```

Si no tiene la herramienta `kubectl` instalada, puede ejecutar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalación del agente de CoScale con un DaemonSet
Kubernetes utiliza los [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) para ejecutar una única instancia de un contenedor en cada host del clúster.
Son perfectos para ejecutar agentes de supervisión como el agente de CoScale.

Después de iniciar sesión en CoScale, vaya a la [página de los agentes](https://app.coscale.com/) para instalar agentes de CoScale en su clúster mediante un DaemonSet. La interfaz de usuario de CoScale proporciona pasos de configuración guiada para crear a un agente y comenzar a supervisar todo el clúster de Kubernetes.

![Configuración del agente de CoScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar al agente en el clúster, ejecute el comando suministrado para tal fin:

![Inicie al agente de CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Eso es todo. Una vez que los agentes están en funcionamiento debería ver datos en la consola durante unos minutos. Visite la [página de los agentes](https://app.coscale.com/) para ver un resumen del clúster, realizar los pasos de configuración adicionales y ver los paneles, como la **información general del clúster de Kubernetes**.

![Información general del clúster de Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

El agente de CoScale se implementa automáticamente en las máquinas nuevas del clúster. El agente se actualiza automáticamente cuando se lanza una versión nueva.


## <a name="next-steps"></a>Pasos siguientes

Consulte la [documentación de CoScale](http://docs.coscale.com/) y [blog](https://www.coscale.com/blog) para más información sobre las soluciones de supervisión de CoScale. 

