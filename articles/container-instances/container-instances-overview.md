---
title: "Introducción a Azure Container Instances"
description: "Descripción de Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 01e539856adbdcf02dc4e49087a3ab71b328db5a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

Los contenedores están convirtiéndose rápidamente en la manera preferida de empaquetar, implementar y administrar aplicaciones en la nube. Azure Container Instances ofrece la forma más rápida y sencilla de ejecutar un contenedor en Azure, sin tener que aprovisionar ninguna máquina virtual y sin necesidad de adoptar un servicio de nivel superior.

Azure Container Instances es una excelente solución para cualquier escenario que pueda funcionar en contenedores aislados, incluidas las aplicaciones simples, la automatización de tareas y los trabajos de compilación. En aquellos escenarios donde se necesita una orquestación completa de contenedores, incluida la detección de servicios en varios contenedores, el escalado automático y las actualizaciones de aplicaciones coordinadas, se recomienda [Azure Container Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Tiempos de inicio rápido

Los contenedores ofrecen importantes ventajas de inicio sobre las máquinas virtuales. Con Azure Container Instances, puede iniciar un contenedor en Azure en segundos sin que sea necesario aprovisionar y administrar máquinas virtuales.

## <a name="hypervisor-level-security"></a>Seguridad de nivel de hipervisor

Históricamente, los contenedores han ofrecido aislamiento a la dependencia entre aplicaciones y gobierno de recursos, pero no se han considerado suficientemente protegidos para el uso de varios inquilinos hostiles. Con Azure Container Instances, la aplicación está tan aislada en un contenedor como lo estaría en una máquina virtual.

## <a name="custom-sizes"></a>Tamaños personalizados

Los contenedores normalmente están optimizados para ejecutar una sola aplicación, pero las necesidades exactas de esas aplicaciones pueden diferir considerablemente. Con Azure Container Instances, puede solicitar exactamente lo que necesita en relación a la memoria y núcleos de CPU. El usuario paga según lo que solicita, facturado por segundo, para que pueda optimizar con precisión los gastos según sus necesidades.

## <a name="public-ip-connectivity"></a>Conectividad IP pública

Con Azure Container Instances, puede exponer los contenedores directamente a Internet con una dirección IP pública. En el futuro, expandiremos nuestras funcionalidades de red para incluir la integración con redes virtuales, equilibradores de carga y otras partes principales de la infraestructura de red de Azure.

## <a name="persistent-storage"></a>Almacenamiento persistente

Para recuperar y conservar el estado con Azure Container Instances, ofrecemos el [montaje directo de los recursos compartidos de Azure Files](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contenedores de Linux y Windows

Con Azure Container Instances, puede programar los contenedores de Windows y Linux con la misma API. Simplemente especifique el tipo de sistema operativo al crear los [grupos de contenedor](container-instances-container-groups.md).

Algunas características están restringidas actualmente a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Grupos con programación compartida

Azure Container Instances admite la programación de [grupos con varios contenedores](container-instances-container-groups.md) que comparten una máquina host, la red local, el almacenamiento y el ciclo de vida. Esto le permite combinar la aplicación principal con otras que actúan en una función auxiliar, como el registro.

## <a name="next-steps"></a>pasos siguientes

Pruebe a implementar un contenedor en Azure con un único comando mediante nuestra [Guía de inicio rápido](container-instances-quickstart.md).