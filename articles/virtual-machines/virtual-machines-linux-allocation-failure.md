---
title: Solución de problemas de errores de asignación de una máquina virtual de Linux | Microsoft Docs
description: Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual de Linux en Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue,azure-resourece-manager,azure-service-management

ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang

---
# <a name="troubleshoot-allocation-failures-when-you-create,-restart,-or-resize-linux-vms-in-azure"></a>Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual de Linux en Azure
Cuando se crea una máquina virtual, se reinician las detenidas (desasignadas) o se les cambia el tamaño, Microsoft Azure asigna recursos de proceso a la suscripción. En ocasiones, es posible que reciba errores al realizar estas operaciones, incluso antes de llegar a los límites de la suscripción de Azure. En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones. La información también puede ser útil si tiene pensado realizar la implementación de sus servicios. También puede [solucionar los errores de asignación al crear, reiniciar o cambiar de tamaño máquinas virtuales Windows en Azure](virtual-machines-windows-allocation-failure.md).

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!--HONumber=Oct16_HO2-->


