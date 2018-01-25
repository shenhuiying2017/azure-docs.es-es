---
title: "Solución de problemas de implementación de máquinas virtuales Linux (modelo clásico) | Microsoft Docs"
description: "Solución de problemas de implementación clásica al crear una nueva máquina virtual de Linux en Azure"
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Solución de problemas de la implementación clásica con la creación de una máquina virtual de Linux en Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para la versión de Resource Manager de este artículo, consulte [aquí](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Recopilación de registros de auditoría
Para iniciar la solución de problemas, recopile los registros de auditoría para identificar el error asociado con el problema.

En Azure Portal, haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual de Windows* > **Configuración** > **Registros de auditoría**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** si el sistema operativo es Linux generalizado y se carga o captura con la configuración generalizada, no habrá errores. De forma similar, si el sistema operativo es Linux especializado y se carga o captura con la configuración especializada, no habrá errores.

**Errores de carga:**

**N<sup>1</sup>:** si el sistema operativo es Linux generalizado y se carga como especializado, recibirá un error de tiempo de espera de aprovisionamiento porque la máquina virtual se atascará en la fase de aprovisionamiento.

**N<sup>2</sup>:** si el sistema operativo es Linux especializado y se carga como generalizado, recibirá un error de aprovisionamiento porque la nueva máquina virtual se ejecuta con el nombre del equipo, el nombre de usuario y la contraseña originales.

**Resolución:**

Para resolver estos errores, cargue el VHD original, disponible en el entorno local, con la misma configuración que para el sistema operativo (generalizada o especializada). Para cargarlo como generalizado, no olvide ejecutarlo o desaprovisionarlo antes. Consulte [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux](create-upload-vhd-classic.md)

**Errores de captura:**

**N<sup>3</sup>:** si el sistema operativo es Linux generalizado y se captura como especializado, recibirá un error de tiempo de espera de aprovisionamiento porque la máquina virtual original no se puede utilizar, ya que está marcada como generalizada.

**N<sup>4</sup>:** si el sistema operativo es Linux especializado y se captura como generalizado, recibirá un error de aprovisionamiento porque la nueva máquina virtual se ejecuta con el nombre del equipo, el nombre de usuario y la contraseña originales. Además, no se puede utilizar la máquina virtual original ya que está marcada como especializada.

**Resolución:**

Para resolver estos errores, elimine la imagen actual del portal y [vuelva a capturarla desde los discos duros virtuales actuales](capture-image-classic.md) con la misma configuración que para el sistema operativo (generalizada o especializada).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problema: Imagen de galería/marketplace/personalizada; error de asignación
Este error se produce en situaciones en las que la nueva solicitud de máquina virtual se envía a un clúster que no tiene espacio libre disponible para alojar la solicitud o no admite el tamaño de la máquina virtual que se solicita. No es posible mezclar varias series diferentes de máquinas virtuales en el mismo servicio en la nube. Por tanto, si desea crear una nueva máquina virtual de un tamaño distinto al que puede admitir el servicio en la nube, la solicitud de proceso producirá un error.

Según las restricciones del servicio en la nube que utilice para crear la nueva máquina virtual, podría recibir un error debido a una de estas dos situaciones.

**Causa 1:** El servicio en la nube está anclado a un clúster concreto o está vinculado a un grupo de afinidad y anclado, por tanto, a un clúster específico por diseño. Por ello, las nuevas solicitudes de recursos de proceso de ese grupo de afinidad se intentan llevar a cabo en el mismo clúster en el que están hospedados los recursos existentes. Sin embargo, el mismo clúster puede no admitir el tamaño solicitado de la máquina virtual o no tener suficiente espacio disponible, lo que generará un error de asignación. Esto es así con independencia de que los nuevos recursos se creen mediante un servicio en la nube nuevo o existente.

**Resolución 1:**

* Cree un nuevo servicio en la nube y asócielo a una región o a una red virtual basada en regiones.
* Cree una nueva máquina virtual en el nuevo servicio en la nube.
  Si se produce un error al intentar crear un nuevo servicio en la nube, vuelva a intentarlo más tarde o cambie la región de dicho servicio.

> [!IMPORTANT]
> Si intentó crear una nueva máquina virtual en un servicio en la nube existente, pero no lo logró y tuvo que crear uno nuevo para la nueva máquina virtual, puede consolidar todas las máquinas virtuales en el mismo servicio en la nube. Para ello, elimine las máquinas virtuales en el servicio en la nube existente y vuelva a capturarlas desde los discos del nuevo servicio en la nube. No obstante, es importante recordar que el nuevo servicio en la nube tendrá un nuevo nombre y dirección VIP, por lo que deberá actualizar esta información en todas las dependencias que utilicen esta información para el servicio en la nube existente.
> 
> 

**Causa 2:** El servicio en la nube está asociado con una red virtual que está vinculada a un grupo de afinidad y anclada, por tanto, a un clúster específico por diseño. Por tanto, todas las nuevas solicitudes de recursos de proceso de ese grupo de afinidad se intentarán llevar a cabo en el mismo clúster en el que están hospedados los recursos existentes. Sin embargo, el mismo clúster puede no admitir el tamaño solicitado de la máquina virtual o no tener suficiente espacio disponible, lo que generará un error de asignación. Esto es así con independencia de que los nuevos recursos se creen mediante un servicio en la nube nuevo o existente.

**Resolución 2:**

* Cree una nueva red virtual regional.
* Cree la nueva máquina virtual en la nueva red virtual.
* [Conecte la red virtual existente](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) a la nueva red virtual. Consulte más información sobre las [redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Asimismo, puede [migrar la red virtual basada en un grupo de afinidad a una red virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)y, después, crear la nueva máquina virtual.

## <a name="next-steps"></a>pasos siguientes
Si tiene problemas al iniciar una máquina virtual Linux detenida o al cambiar el tamaño de una máquina virtual Linux existente en Azure, consulte [Solución de problemas de la implementación clásica con el reinicio o el cambio de tamaño de una máquina virtual con Linux existente en Azure](restart-resize-error-troubleshooting.md).

