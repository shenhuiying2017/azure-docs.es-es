---
title: "Introducción sobre Jenkins y Azure | Microsoft Docs"
description: "Hospede la compilación de Jenkins e implemente el servidor de automatización en Azure y use los recursos de proceso y almacenamiento de Azure para extender sus canalizaciones de integración y entrega continuas (CI/CD)."
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: daa202ddf0dc934c491ead3951ddc4fdc3dd819c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-jenkins"></a>Azure y Jenkins

[Jenkins](https://jenkins.io/) es un conocido servidor de automatización de código abierto usado para configurar la integración y entrega continuas (CI/CD) de los proyectos de software. Puede hospedar la implementación de Jenkins en Azure o ampliar la configuración de Jenkins existente con recursos de Azure. También hay complementos de Jenkins disponibles para simplificar la integración y entrega continuas de las aplicaciones en Azure.

Este artículo es una introducción al uso de Azure con Jenkins que detalla las principales características de Azure que están disponibles para los usuarios de Jenkins. Para empezar a trabajar con su propio servidor de Jenkins en Azure, consulte nuestro [inicio rápido](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hospedaje de los servidores de Jenkins en Azure

Hospede Jenkins en Azure para centralizar la automatización de compilaciones y escalar la implementación a medida que aumenten las necesidades de sus proyectos de software. Puede implementar Jenkins en Azure mediante:
 
- [La plantilla de solución de Jenkins](install-jenkins-solution-template.md) en Azure Marketplace.
- [Máquinas virtuales de Azure](/azure/virtual-machines/linux/overview). Consulte nuestro [tutorial](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) para crear una instancia de Jenkins en una máquina virtual.
- Para crearla en un clúster de Kubernetes en [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), consulte nuestra [guía de procedimientos](/azure/container-service/kubernetes/container-service-kubernetes-jenkin).

Supervise y administre la implementación de Azure Jenkins mediante [Log Analytics](/azure/log-analytics/log-analytics-overview), [Operations Management Suite](/azure/operations-management-suite/operations-management-suite-overview)y la [CLI de Azure] (/cli/azure/overview).

## <a name="scale-your-build-automation-on-demand"></a>Escalado a petición de la automatización de compilaciones

Agregue agentes de compilación a la implementación de Jenkins existente para escalar la capacidad de compilación de este a medida que el número de compilaciones y la complejidad de los trabajos y canalizaciones aumenta. Puede ejecutar estos agentes de compilación en máquinas virtuales de Azure mediante el [complemento de agentes de máquina virtual de Azure](jenkins-azure-vm-agents.md). Consulte nuestro [tutorial](/azure/jenkins/jenkins-azure-vm-agents) para obtener más detalles.

Una vez configurado con una [entidad de servicio de Azure](/azure/azure-resource-manager/resource-group-overview), los trabajos y canalizaciones de Jenkins pueden usar esta credencial para:

- Almacene de forma segura los artefactos de compilación en [Azure Storage](/azure/storage/common/storage-introduction) mediante el [complemento correspondiente](https://plugins.jenkins.io/windows-azure-storage). Revise la [guía de procedimientos de almacenamiento de Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) para más información.
- Administre y configure los recursos de Azure con la [CLI de Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Implementación del código en los servicios de Azure

Use los complementos de Jenkins para implementar las aplicaciones en Azure como parte de sus canalizaciones de integración y entrega continuas de Jenkins. La implementación en [Azure App Service](/azure/app-service/) y [Azure Container Service](/azure/container-service/kubernetes/) le permite realizar copias intermedias, probar y publicar actualizaciones de las aplicaciones sin tener que administrar la infraestructura subyacente.

 Hay complementos disponibles para implementar los siguientes servicios y entornos:

- [Azure Web App en Linux](/azure/app-service/containers/app-service-linux-intro). Consulte el [tutorial](java-deploy-webapp-tutorial.md) para empezar a trabajar.
- [Aplicación web de Azure](/azure/app-service/app-service-web-overview). Consulte los [procedimientos](deploy-Jenkins-app-service-plugin.md) para empezar a trabajar.

