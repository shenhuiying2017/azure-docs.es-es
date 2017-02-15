---
title: "Solución de problemas de errores de asignación de una máquina virtual de Windows | Microsoft Docs"
description: "Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una VM de Windows en Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7c83071775cd4c237dfcdb77dc89a6d5c4df4aac


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de las VM de Windows en Azure
Cuando se crea una máquina virtual, se reinician las detenidas (desasignadas) o se les cambia el tamaño, Microsoft Azure asigna recursos de proceso a la suscripción. En ocasiones, es posible que reciba errores al realizar estas operaciones, incluso antes de llegar a los límites de la suscripción de Azure. En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones. La información también puede ser útil si tiene pensado realizar la implementación de sus servicios. También puede [solucionar los errores de asignación al crear, reiniciar o cambiar de tamaño máquinas virtuales Linux en Azure](virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


