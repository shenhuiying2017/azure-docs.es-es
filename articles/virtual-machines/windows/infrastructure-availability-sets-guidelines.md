---
title: "Conjuntos de disponibilidad para máquinas virtuales Windows en Azure| Microsoft Docs"
description: "Obtenga información sobre las directrices clave de diseño e implementación para implementar conjuntos de disponibilidad en los servicios de infraestructura de Azure."
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f9449f58-664b-4d5d-82f6-84c5d083047f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 2d146d3a3bd2497a6c1d9b2b924d2e2cd00710de
ms.lasthandoff: 04/14/2017


---
# <a name="azure-availability-sets-guidelines-for-windows-vms"></a>Directrices de conjuntos de disponibilidad de Azure para máquinas virtuales Windows

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir los pasos de planeación necesarios para que los conjuntos de disponibilidad garanticen que sus aplicaciones permanezcan accesibles durante eventos planeados o no planeados.

## <a name="implementation-guidelines-for-availability-sets"></a>Directrices de implementación para los conjuntos de disponibilidad
Decisiones:

* ¿Cuántos conjuntos de disponibilidad necesita para los distintos roles y niveles de la infraestructura de la aplicación?

Tareas:

* Defina el número de máquinas virtuales en cada nivel de aplicación que necesite.
* Determine si necesita ajustar el número de dominios de error o actualización que se va a usar para la aplicación.
* Defina los conjuntos de disponibilidad necesarios usando su convención de nomenclatura y las máquinas virtuales que residirán en ellos. Una máquina virtual solo puede residir en un conjunto de disponibilidad. 

## <a name="availability-sets"></a>Conjuntos de disponibilidad
En Azure, las máquinas virtuales (VM) se pueden colocar en una agrupación lógica llamada conjunto de disponibilidad. Al crear máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure distribuirá la ubicación de esas máquinas virtuales a través de la infraestructura subyacente. Si hubiera un evento de mantenimiento planeado para la plataforma de Azure o un error de hardware o infraestructura subyacente, el uso de conjuntos de disponibilidad garantiza que al menos una máquina virtual siguiera ejecutándose.

Como procedimiento recomendado, las aplicaciones no deben residir en una sola máquina virtual. Un conjunto de disponibilidad que contiene una sola máquina virtual no obtiene ninguna protección por parte de eventos planeados o no planeados dentro de la plataforma de Azure. El [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requiere dos o más máquinas virtuales dentro de un conjunto de disponibilidad para permitir la distribución de máquinas virtuales a través de la infraestructura subyacente. Si utiliza [Azure Premium Storage](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), el Acuerdo de Nivel de Servicio de Azure se aplica a una sola máquina virtual.

La infraestructura subyacente de Azure se divide en varios clústeres de hardware. Cada uno de ellos puede admitir una gran variedad de tamaños de máquina virtual. Un conjunto de disponibilidad solo se hospedada en un clúster de hardware único en cualquier momento. Por lo tanto, los tamaños de máquina virtual que puede haber en un único conjunto de disponibilidad se limita a los que admita el clúster de hardware. El clúster de hardware del conjunto de disponibilidad se selecciona cuando se implementa la primera máquina virtual en el conjunto de disponibilidad o al iniciar la primera máquina virtual en un conjunto de disponibilidad donde todas las máquinas virtuales tienen en ese momento el estado de detenido o desasignado. El siguiente comando de PowerShell se puede utilizar para determinar los tamaños de máquina virtual disponibles para un conjunto de disponibilidad: "Get-AzureRmVMSize -ResourceGroupName \<string\> -AvailabilitySetName \<string\>"

Cada clúster de hardware está dividido en varios dominios de actualización y de error. Estos dominios se definen teniendo en cuenta qué hosts compartirán un ciclo de actualización común o una infraestructura física similar como la energía y redes. Azure distribuirá automáticamente las máquinas virtuales dentro de un conjunto de disponibilidad en los dominios para mantener la disponibilidad y tolerancia a errores. Dependiendo del tamaño de la aplicación y el número de máquinas virtuales dentro de un conjunto de disponibilidad, puede ajustar el número de dominios que desea usar. Puede leer más sobre la [administración de la disponibilidad y el uso de los dominios de actualización y error](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Al diseñar la infraestructura de la aplicación, planee los niveles de aplicación que usará. Agrupe las máquinas virtuales que prestan el mismo servicio a conjuntos de disponibilidad, tales como un conjunto de disponibilidad para las máquinas virtuales de front-end que ejecutan IIS. Cree un conjunto de disponibilidad independiente para las máquinas virtuales de back-end que ejecutan SQL Server. El objetivo es garantizar que un conjunto de disponibilidad proteja cada uno de los componentes de la aplicación y que la instancia siga ejecutándose al menos una vez.

Los equilibradores de carga pueden usarse delante de cada nivel de aplicación junto con un conjunto de disponibilidad para garantizar que el tráfico pueda enrutarse siempre a una instancia en ejecución. Sin un equilibrador de carga, las máquinas virtuales pueden seguir ejecutándose durante los eventos de mantenimiento planeados y no planeados, pero es posible que los usuarios finales no puedan resolverlos si la máquina virtual principal no está disponible.

Diseñe su aplicación para lograr una alta disponibilidad en la capa de almacenamiento. El procedimiento recomendado es [usar Managed Disks para las máquinas virtuales de un conjunto de disponibilidad](../windows/manage-availability.md#use-managed-disks-for-vms-in-availability-set). Si actualmente usa discos no administrados, es muy recomendable [convertir las máquinas virtuales del conjunto de disponibilidad para que usen Managed Disks](../windows/convert-unmanaged-to-managed-disks.md#convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set).

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


