---
title: "Introducción a Azure Container Service para Kubernetes"
description: "Azure Container Service para Kubernetes simplifica la implementación y administración de aplicaciones basadas en contenedor en Azure."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 11/13/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 2980d4733dcf138c3a9aa1713cff994bbf5a296c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="introduction-to-azure-container-service-aks"></a>Introducción a Azure Container Service (AKS)

Azure Container Service (AKS) simplifica la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor. Esto le permite usar sus conocimientos o recurrir a un importante y creciente grupo de expertos comunitarios para implementar y administrar aplicaciones basadas en contenedores en Microsoft Azure.

Con AKS puede sacar provecho de las características empresariales de Azure sin perder la portabilidad de las aplicaciones a través de Kubernetes y el formato de imagen de Docker.

## <a name="managed-kubernetes-in-azure"></a>Managed Kubernetes en Azure

AKS reduce la complejidad y la sobrecarga operativa de la administración de un clúster de Kubernetes al descargar gran parte de esa responsabilidad en Azure. Como servicio hospedado de Kubernetes, Azure maneja tareas críticas como la supervisión del estado y el mantenimiento para usted. Además, solo paga por los nodos de agentes en los clústeres, no por los maestros. Como servicio administrado de Kubernetes, AKS proporciona:

> [!div class="checklist"]
> * Actualizaciones de versión y aplicación de revisiones de Kubernetes automatizadas
> * Escalado de clústeres sencillo
> * Plano de control hospedado de recuperación automática (maestros)
> * Ahorros en costos: pague solo por la ejecución de nodos de grupo de agentes

Como Azure controla la administración de los nodos del clúster de AKS, ya no es necesario realizar muchas tareas manualmente, como las actualizaciones de clúster. Puesto que Azure controla estas tareas de mantenimiento críticas para usted, AKS no proporciona acceso directo (como con SSH) al clúster.

## <a name="using-azure-container-service-aks"></a>Uso de Azure Container Service (AKS)
El objetivo de AKS es proporcionar un entorno de hospedaje de contenedores mediante el uso de tecnologías y herramientas de código abierto, conocidas por los clientes. Para tal fin, se exponen los puntos de conexión de API de Kubernetes estándar. Con estos puntos de conexión estándar, puede sacar provecho de cualquier software que pueda comunicarse con un clúster de Kubernetes. Por ejemplo, puede elegir [kubectl][kubectl-overview], [helm][helm] o [draft][draft].

## <a name="creating-a-kubernetes-cluster-using-azure-container-service-aks"></a>Creación de un clúster de Kubernetes mediante Azure Container Service (AKS)
Para empezar a usar AKS, implemente un clúster de AKS con la [CLI de Azure][aks-quickstart] o mediante el portal (busque en Marketplace **Azure Container Service**). Los usuarios avanzados, que necesitan más control sobre las plantillas de Azure Resource Manager, pueden usar el proyecto [acs-motor] [acs-engine] de código abierto para crear su propio clúster de Kubernetes personalizado e implementarlo mediante la CLI de `az`.

### <a name="using-kubernetes"></a>Uso de Kubernetes
Kubernetes automatiza la implementación, el escalado y la administración de aplicaciones en contenedor. Tiene un amplio conjunto de características que incluyen:
* Empaquetamiento automático en contenedores
* Recuperación automática
* Escalado horizontal
* Detección de servicio y equilibrio de carga
* Lanzamientos y reversiones automatizados
* Administración de configuración y secretos
* Orquestación de almacenamiento
* Ejecución por lotes

## <a name="videos"></a>Vídeos

Azure Container Service (AKS), Azure Friday, octubre de 2017:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Container-Orchestration-Simplified-with-Managed-Kubernetes-in-Azure-Container-Service-AKS/player]
>
>

Herramientas para desarrollar e implementar aplicaciones en Kubernetes (Azure OpenDev, junio de 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

Obtenga más información sobre la implementación y administración de AKS en la Guía de inicio rápidos de AKS.

> [!div class="nextstepaction"]
> [Tutorial de AKS][aks-quickstart]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md

