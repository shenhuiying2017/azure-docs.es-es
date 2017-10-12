---
title: "Introducción a Azure Container Instances | Azure Docs"
description: "Descripción de Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6e614f1120b3dc54871b393ac0a2703c21b30ae8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-instances"></a>Azure Container Instances

Los contenedores están convirtiéndose rápidamente en la manera preferida de empaquetar, implementar y administrar aplicaciones en la nube. Azure Container Instances ofrece la forma más rápida y sencilla de ejecutar un contenedor en Azure, sin tener que aprovisionar ninguna máquina virtual y sin necesidad de adoptar un servicio de nivel superior.

Azure Container Instances es una excelente solución para cualquier escenario que pueda funcionar en contenedores aislados, incluidas las aplicaciones simples, la automatización de tareas y los trabajos de compilación. En aquellos escenarios donde se necesita una orquestación completa de contenedores, incluida la detección de servicios en varios contenedores, el escalado automático y las actualizaciones de aplicaciones coordinadas, se recomienda [Azure Container Service](https://docs.microsoft.com/azure/container-service/).

## <a name="fast-startup-times"></a>Tiempos de inicio rápido

Los contenedores ofrecen importantes ventajas de inicio sobre las máquinas virtuales. Con Azure Container Instances, puede iniciar un contenedor en Azure en segundos sin que sea necesario aprovisionar y administrar máquinas virtuales.

## <a name="hypervisor-level-security"></a>Seguridad de nivel de hipervisor

Históricamente, los contenedores han ofrecido aislamiento a la dependencia entre aplicaciones y gobierno de recursos, pero no se han considerado suficientemente protegidos para el uso de varios inquilinos hostiles. Con Azure Container Instances, la aplicación está tan aislada en un contenedor como lo estaría en una máquina virtual.

## <a name="custom-sizes"></a>Tamaños personalizados

Los contenedores normalmente están optimizados para ejecutar una sola aplicación, pero las necesidades exactas de esas aplicaciones pueden diferir considerablemente. Con Azure Container Instances, puede solicitar exactamente lo que necesita en relación a la memoria y núcleos de CPU. El usuario paga según lo que solicita, facturado por segundo, para que pueda optimizar con precisión los gastos según sus necesidades.

## <a name="public-ip-connectivity"></a>Conectividad IP pública

Con Azure Container Instances, puede exponer los contenedores directamente a Internet con una dirección IP pública. En el futuro, expandiremos nuestras funcionalidades de red para incluir la integración con redes virtuales, equilibradores de carga y otras partes principales de la infraestructura de red de Azure.

## <a name="persistent-storage"></a>Almacenamiento persistente

Para recuperar y conservar el estado con Azure Container Instances, ofrecemos el montaje directo de los recursos compartidos de archivos de Azure.

## <a name="linux-and-windows-containers"></a>Contenedores de Linux y Windows

Con Azure Container Instances, puede programar los contenedores de Windows y Linux con la misma API. Simplemente hay que indicar el tipo de sistema operativo base y todo lo demás es idéntico.

## <a name="co-scheduled-groups"></a>Grupos con programación compartida

Azure Container Instances admite la programación de grupos con varios contenedores que comparten una máquina host, la red local, el almacenamiento y el ciclo de vida. Esto le permite combinar la aplicación principal con otras que actúan en una función auxiliar, como el registro.

## <a name="next-steps"></a>Pasos siguientes

Pruebe a implementar un contenedor en Azure con un único comando mediante nuestra [Guía de inicio rápido](container-instances-quickstart.md).