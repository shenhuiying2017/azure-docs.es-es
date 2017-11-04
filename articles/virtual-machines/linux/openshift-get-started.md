---
title: "Información general sobre OpenShift en Azure| Microsoft Docs"
description: "Información general sobre OpenShift en Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>Información general sobre OpenShift

OpenShift es una plataforma de aplicación de contenedor abierta y extensible que aporta Docker y Kubernetes para la empresa.  

OpenShift incluye Kubernetes para la administración y orquestación de contenedores. Incluye herramientas para desarrolladores y centradas en las operaciones que permiten:

- Desarrollo rápido de aplicaciones
- Implementación y escalado sencillos
- Mantenimiento del ciclo de vida a largo plazo de los equipos y las aplicaciones

Hay varias ofertas de OpenShift, dos de las cuales están disponibles para ejecutarse en Azure.

- OpenShift Origin
- OpenShift Container Platform
- OpenShift Online
- OpenShift Dedicated

De las cuatro ofertas cubiertas, dos están disponibles para que los clientes las implementen en Azure por sí solas: OpenShift Origin y OpenShift Container Platform.

## <a name="openshift-origin"></a>OpenShift Origin

Proyecto ascendente de [código abierto](https://www.openshift.org/) de OpenShift, compatible con la comunidad. Origin puede instalarse en CentOS o RHEL.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Versión preparada para empresas ([oferta comercial](https://www.openshift.com)) de Red Hat, compatible con Red Hat. El cliente adquiere los derechos necesarios para OpenShift Container Platform y es responsable de la instalación y la administración de toda la infraestructura.

Puesto que el cliente "posee" toda la plataforma, puede realizar instalaciones en su centro de datos local, nube pública (Azure, AWS, Google, etc.), etcétera.

## <a name="openshift-online"></a>OpenShift Online

OpenShift para **varios suscriptores** administrado por Red Hat (mediante Container Platform). Red Hat administra toda la infraestructura subyacente (máquinas virtuales, clúster OpenShift, redes, almacenamiento, etc.). 

El cliente implementa los contenedores, pero no tiene ningún control sobre qué hosts ejecutan los contenedores. Puesto que es un servicio para varios suscriptores, los contenedores pueden colocarse en los mismos hosts de máquina virtual como contenedores de otros clientes. El coste es por contenedor.

## <a name="openshift-dedicated"></a>OpenShift Dedicated

OpenShift para **un solo suscriptor** administrado por Red Hat (mediante Container Platform). Red Hat administra toda la infraestructura subyacente (máquinas virtuales, clúster OpenShift, redes, almacenamiento, etc.). El clúster es específico para un cliente y se ejecuta en una nube pública (AWS, Google, Azure: el servicio entrará en vigor a principios de 2018). El clúster inicial incluye cuatro nodos de aplicación por 48 000 $ anuales (pago por adelantado para todo un año).

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de los requisitos previos comunes para OpenShift en Azure](./openshift-prerequisites.md)
- [Deploy OpenShift Origin](./openshift-origin.md) (Implementar OpenShift Origin)
- [Deploy OpenShift Container Platform](./openshift-container-platform.md) (Implementar OpenShift Container Platform)
- [Tareas posteriores a la implementación](./openshift-post-deployment.md)
- [Solución de problemas de implementación de OpenShift](./openshift-troubleshooting.md)
