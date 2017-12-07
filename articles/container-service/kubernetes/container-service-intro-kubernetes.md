---
title: "Introducción a Azure Container Service para Kubernetes"
description: "Azure Container Service para Kubernetes simplifica la implementación y administración de aplicaciones basadas en contenedor en Azure."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 1a877204e91098191c218bf1c991cc5d8e53ee7a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="introduction-to-azure-container-service-for-kubernetes"></a>Introducción a Azure Container Service para Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure Container Service para Kubernetes simplifica la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor. Esto le permite usar sus conocimientos o recurrir a un importante y creciente grupo de expertos comunitarios para implementar y administrar aplicaciones basadas en contenedores en Microsoft Azure.

Con Azure Container Service puede sacar provecho de las características empresariales de Azure sin perder la portabilidad de las aplicaciones a través de Kubernetes y el formato de imagen de Docker.

## <a name="using-azure-container-service-for-kubernetes"></a>Uso de Azure Container Service para Kubernetes
Nuestro objetivo con el servicio Contenedor de Azure es proporcionar un entorno de hospedaje de contenedores mediante el uso de tecnologías y herramientas de código abierto, conocidas por nuestros clientes. Para tal fin, se exponen los puntos de conexión de API de Kubernetes estándar. Con estos puntos de conexión estándar, puede sacar provecho de cualquier software que pueda comunicarse con un clúster de Kubernetes. Por ejemplo, puede elegir [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) o [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Creación de un clúster de Docker mediante Azure Container Service
Para empezar a usar Azure Container Service, implemente un clúster de Azure Container Service con la [CLI de Azure 2.0](container-service-kubernetes-walkthrough.md) o a través del portal (busque **Azure Container Service** en Marketplace). Los usuarios avanzados, que necesitan más control sobre las plantillas de Azure Resource Manager, puede usar el proyecto [acs-motor](https://github.com/Azure/acs-engine) de código abierto para crear su propio clúster de Kubernetes personalizado e implementarlo a través de la CLI de `az`.

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

Diagrama de la arquitectura de Kubernetes implementado a través de Azure Container Service:

![Azure Container Service configurado para usar Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Vídeos

Compatibilidad con Kubernetes de Azure Container Services (Azure, viernes, enero de 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Herramientas para desarrollar e implementar aplicaciones en Kubernetes (Azure OpenDev, junio de 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Pasos siguientes

Consulte la [Guía de inicio rápido de Kubernetes](container-service-kubernetes-walkthrough.md) para empezar a explorar Azure Container Service hoy mismo.