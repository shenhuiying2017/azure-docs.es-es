---
title: Conjuntos de disponibilidad para VM de Linux en Azure| Microsoft Docs
description: "Obtenga información sobre las directrices clave de diseño e implementación para implementar conjuntos de disponibilidad en los servicios de infraestructura de Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 24f1d91c-8cc0-4251-bb67-ac4c4c37e8cd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: b67f13d279d68712beea78c93d40881d92ba8896
ms.lasthandoff: 03/18/2017


---
# <a name="azure-availability-sets-guidelines-for-linux-vms"></a>Directrices de conjuntos de disponibilidad de Azure para máquinas virtuales Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

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

Como procedimiento recomendado, las aplicaciones no deben residir en una sola máquina virtual. Un conjunto de disponibilidad que contiene una sola máquina virtual no obtiene ninguna protección por parte de eventos planeados o no planeados dentro de la plataforma de Azure. El [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) requiere dos o más máquinas virtuales dentro de un conjunto de disponibilidad para permitir la distribución de máquinas virtuales a través de la infraestructura subyacente. Si utiliza [Azure Premium Storage](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), el Acuerdo de Nivel de Servicio de Azure se aplica a una sola máquina virtual.

La infraestructura subyacente de Azure se divide en varios clústeres de hardware. Cada uno de ellos puede admitir una gran variedad de tamaños de máquina virtual. Un conjunto de disponibilidad solo se hospedada en un clúster de hardware único en cualquier momento. Por lo tanto, los tamaños de máquina virtual que puede haber en un único conjunto de disponibilidad se limita a los que admita el clúster de hardware. El clúster de hardware del conjunto de disponibilidad se selecciona cuando se implementa la primera máquina virtual en el conjunto de disponibilidad o al iniciar la primera máquina virtual en un conjunto de disponibilidad donde todas las máquinas virtuales tienen en ese momento el estado de detenido o desasignado. El siguiente comando de la CLI puede usarse para determinar los tamaños de máquina virtual disponibles para un conjunto de disponibilidad: "az vm list-sizes --location \<string\>".

Cada clúster de hardware está dividido en varios dominios de actualización y de error. Estos dominios se definen teniendo en cuenta qué hosts compartirán un ciclo de actualización común o una infraestructura física similar como la energía y redes. Azure distribuirá automáticamente las máquinas virtuales dentro de un conjunto de disponibilidad en los dominios para mantener la disponibilidad y tolerancia a errores. Dependiendo del tamaño de la aplicación y el número de máquinas virtuales dentro de un conjunto de disponibilidad, puede ajustar el número de dominios que desea usar. Puede leer más sobre la [administración de la disponibilidad y el uso de los dominios de actualización y error](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Al diseñar la infraestructura de la aplicación, también debe planear los niveles de aplicación que usará. Agrupe las máquinas virtuales que prestan el mismo servicio a conjuntos de disponibilidad tales como un conjunto de disponibilidad para las máquinas virtuales de front-end que ejecutan nginx o Apache. Cree un conjunto de disponibilidad independiente para las máquinas virtuales de back-end que ejecutan MongoDB o MySQL. El objetivo es garantizar que un conjunto de disponibilidad proteja cada uno de los componentes de la aplicación y que la instancia siga ejecutándose al menos una vez.

Los equilibradores de carga pueden usarse delante de cada nivel de aplicación junto con un conjunto de disponibilidad para garantizar que el tráfico pueda enrutarse siempre a una instancia en ejecución. Sin un equilibrador de carga, las máquinas virtuales pueden seguir ejecutándose durante los eventos de mantenimiento planeados y no planeados, pero es posible que los usuarios finales no puedan resolverlos si la máquina virtual principal no está disponible.

Al usar discos no administrados, diseñe su aplicación para lograr una alta disponibilidad en la capa de almacenamiento. El procedimiento recomendado consiste en utilizar una cuenta de almacenamiento independiente para cada máquina virtual de un conjunto de disponibilidad. Mantenga todos los discos (sistema operativo y datos) asociados a una máquina virtual en la misma cuenta de almacenamiento. Tenga en cuenta los [límites](../storage/storage-scalability-targets.md) de la cuenta de almacenamiento al agregar más discos duros virtuales a una cuenta de almacenamiento. Para [Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), la distribución de los discos subyacentes se prepara automáticamente.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


