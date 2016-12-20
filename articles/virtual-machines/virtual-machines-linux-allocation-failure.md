---
title: "Solución de problemas de errores de asignación de una máquina virtual con Linux | Microsoft Docs"
description: "Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual de Linux en Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d4520768a13de519fcc3f8778d2b87caa667b654


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual de Linux en Azure
Cuando se crea una máquina virtual, se reinician las detenidas (desasignadas) o se les cambia el tamaño, Microsoft Azure asigna recursos de proceso a la suscripción. En ocasiones, es posible que reciba errores al realizar estas operaciones, incluso antes de llegar a los límites de la suscripción de Azure. En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones. La información también puede ser útil si tiene pensado realizar la implementación de sus servicios. También puede [solucionar los errores de asignación al crear, reiniciar o cambiar de tamaño máquinas virtuales Windows en Azure](virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


