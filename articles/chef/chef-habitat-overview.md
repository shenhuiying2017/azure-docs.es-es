---
title: Uso de Habitat para implementar una aplicación en Azure
description: Aprenda a implementar sistemáticamente la aplicación en máquinas virtuales y contenedores de Azure.
keywords: azure, chef, devops, máquinas virtuales, introducción, automatizar, habitat
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267206"
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Uso de Habitat para implementar una aplicación en Azure
[Habitat](https://www.habitat.sh/) es el primero de sus proyectos de código abierto en ofrecer un enfoque completamente nuevo sobre la administración de aplicaciones. Habitat convierte la aplicación y su automatización en la unidad de implementación. Cuando las aplicaciones se encapsulan en un "hábitat" ligero, el entorno en tiempo de ejecución, ya sea un contenedor, sin sistema operativo, o un PaaS, ya no es el centro y no restringe la aplicación. 

En este artículo se describen las ventajas de uso de Habitat.

## <a name="support-for-the-modern-application"></a>Compatibilidad con aplicaciones modernas
Los paquetes de Habitat incluyen todo lo que necesita una aplicación para funcionar a lo largo de su ciclo de vida. Los componentes principales de Habitat son:
- Formato de empaquetado: las aplicaciones de un paquete de Habitat son atómicas, inmutables y auditables.
- El supervisor de Habitat ejecuta los paquetes de aplicación con conocimiento de las relaciones del mismo nivel, la estrategia de actualización y las directivas de seguridad. El supervisor de Habitat configura y administra la aplicación sea cual sea el entorno existente.
- El ecosistema de Habitat también proporciona un servicio de compilación que toma un plan de compilación de Habitat, crea el paquete de Habitat y lo publica en un almacén.

## <a name="run-any-application-anywhere"></a>Ejecución de una aplicación en cualquier parte
Con Habitat, las aplicaciones pueden ejecutarse sin modificar en cualquier entorno de tiempo de ejecución. Aquí se incluyen desde máquinas virtuales y sin sistema operativo hasta contenedores (como Docker), sistemas de administración de clústeres (como Mesosphere o Kubernetes) y sistemas PaaS (como Pivotal Cloud Foundry).

## <a name="easily-port-legacy-applications"></a>Migración fácil de aplicaciones heredadas
Cuando las aplicaciones heredadas se encapsulan en un paquete de Habitat, son independientes del entorno para el que se diseñaron originalmente. Los paquetes se pueden mover rápidamente a entornos más modernos, como la nube o los contenedores. Además, como los paquetes de Habitat tienen una interfaz estándar orientada al exterior, las aplicaciones heredadas son mucho más fáciles de administrar.

## <a name="improve-the-container-experience"></a>Mejora de la experiencia del contenedor
Habitat reduce la complejidad de administrar contenedores en entornos de producción. Como la configuración de las aplicaciones dentro de un contenedor se automatiza, Habitat soluciona las dificultades a las que se enfrentan los desarrolladores al mover las aplicaciones basadas en contenedor de los entornos de desarrollo a producción.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integración en el flujo de trabajo de Chef DevOps
El proyecto Habitat está patrocinado por Chef. Hábitat aprovecha la enorme experiencia de Chef con la automatización de la infraestructura para ofrecer funcionalidades de automatización sin precedentes para las aplicaciones. Chef ofrecerá compatibilidad comercial con Habitat y garantizará la perfecta integración entre Habitat y Chef Delivery para automatizar completamente el ciclo de versión de aplicaciones, desde el desarrollo hasta la implementación.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de una máquina virtual Windows en Azure mediante Chef](/azure/virtual-machines/windows/chef-automation)