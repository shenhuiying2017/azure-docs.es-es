---
title: "Directrices de máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre las directrices clave de diseño e implementación para implementar máquinas virtuales Windows en Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f932e65a-437b-48b0-8d70-f61ded8ce1c6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f5fd03ef7e18706bd99688acb83cda02fb4a027a
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


---
<a id="azure-virtual-machines-guidelines-for-windows" class="xliff"></a>

# Directrices de máquinas virtuales de Azure para Windows
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir los pasos de planeamiento necesarios para crear y administrar máquinas virtuales (VM) en su entorno de Azure.

<a id="implementation-guidelines-for-vms" class="xliff"></a>

## Directrices de implementación para las máquinas virtuales
Decisiones:

* ¿Cuántas máquinas virtuales necesita para sus diversos niveles de aplicación y los componentes de su infraestructura?
* ¿Qué recursos de CPU y memoria necesita cada máquina virtual y cuáles son los requisitos de almacenamiento?

Tareas:

* Defina las cargas de trabajo de la aplicación y los recursos que requerirá la máquina virtual.
* Alinee las demandas de recursos de cada máquina virtual con el tipo de almacenamiento y el tamaño de VM adecuados.
* Defina sus grupos de recursos para los diferentes niveles y los componentes de su infraestructura.
* Defina la convención de nomenclatura de la máquina virtual.
* Cree sus máquinas virtuales con Azure PowerShell, el portal web o las plantillas de Resource Manager.

<a id="virtual-machines" class="xliff"></a>

## Máquinas virtuales
Es muy probable que uno de los recursos principales de su entorno de Azure sean las máquinas virtuales. Este recurso es donde ejecutará sus aplicaciones, bases de datos, servicios de autenticación, etc.

Es importante conocer los [diferentes tamaños de máquina virtual](sizes.md) para cambiar correctamente el tamaño de su entorno desde una perspectiva de costos y rendimiento. Si sus máquinas virtuales no tienen una cantidad adecuada de núcleos de CPU o memoria, el rendimiento de su aplicación se verá afectado independientemente de su diseño y desarrollo. Revise las cargas de trabajo sugeridas para cada serie de máquinas virtuales como punto de partida al decidir el tamaño de VM que debe usar para cada componente de su infraestructura. Puede [cambiar el tamaño de una máquina virtual](resize-vm.md) después de la implementación.

El almacenamiento desempeña un papel clave en el rendimiento de la máquina virtual. Puede usar Standard Storage que utilizan los discos giratorios habituales, o bien Premium Storage para altas cargas de trabajo de E/S y el máximo rendimiento que utilizan los discos SSD. Al igual que ocurre con el tamaño de VM, existen consideraciones sobre el costo en lo referente a la selección del medio de almacenamiento. Puede leer el [artículo de las directrices de infraestructura de almacenamiento](infrastructure-storage-solutions-guidelines.md) para saber cómo diseñar el almacenamiento adecuado para que el rendimiento de sus máquinas virtuales sea óptimo.

<a id="resource-groups" class="xliff"></a>

## Grupos de recursos
Componentes como las máquinas virtuales se agrupan de forma lógica para facilitar la administración y el mantenimiento mediante los [grupos de recursos de Azure](../../azure-resource-manager/resource-group-overview.md). Al usar los grupos de recursos, puede crear, administrar y supervisar todos los recursos que conforman una aplicación determinada. También puede implementar [controles de acceso basados en rol](../../active-directory/role-based-access-control-what-is.md) para conceder acceso a otros usuarios de su equipo solo a los recursos que necesiten. Tómese su tiempo para planear sus grupos de recursos y asignaciones de roles. Existen distintos enfoques para diseñar e implementar realmente grupos de recursos, de modo que asegúrese de leer el [artículo de las directrices de grupos de recursos](infrastructure-resource-groups-guidelines.md) para saber cuál es la mejor forma de crear sus máquinas virtuales.

<a id="templates" class="xliff"></a>

## Plantillas
Puede crear plantillas, definidas por archivos JSON declarativos, para crear sus máquinas virtuales. Normalmente, las plantillas también crearán el almacenamiento, las redes, las interfaces de red o el direccionamiento IP necesarios junto con las propias máquinas virtuales. Puede usar plantillas para crear entornos coherentes y reproducibles con fines de desarrollo y prueba para replicar fácilmente entornos de producción y viceversa. Puede leer más sobre cómo [crear y usar plantillas](../../azure-resource-manager/resource-group-overview.md#template-deployment) a fin de aprender a usarlas para crear e implementar sus máquinas virtuales.

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


