---
title: "Acerca de los discos y los VHD para máquinas virtuales de Linux en Microsoft Azure | Microsoft Docs"
description: "Conozca los aspectos básicos de los discos y los VHD para las máquinas virtuales Linux en Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 7be8dd52-98f7-4187-9b78-55197915bc9b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: robinsh
ms.openlocfilehash: be5f09af275142590ec6ade02562e914d5726e08
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>Acerca de los discos y los discos duros virtuales para máquinas virtuales de Linux en Azure
Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco del sistema operativo Linux y un disco temporal. El disco del sistema operativo se crea a partir de una imagen y el disco del sistema operativo y la imagen son discos duros en realidad virtuales (VHD) almacenados en una cuenta de almacenamiento de Azure. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. 

En este artículo, se tratan los diferentes usos de los discos y después se describen los diferentes tipos de discos que puede crear y utilizar. Este artículo también está disponible para [máquinas virtuales Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por las máquinas virtuales

Observemos cómo las máquinas virtuales utilizan los discos.

## <a name="operating-system-disk"></a>Disco del sistema operativo
Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado de forma predeterminada como /dev/sda. Este disco tiene una capacidad máxima de 2048 gigabytes (GB). 

## <a name="temporary-disk"></a>Disco temporal
Cada máquina contiene un disco temporal. El disco temporal proporciona almacenamiento a corto plazo para aplicaciones y procesos, y está destinado únicamente a almacenar datos como archivos de paginación o de intercambio. Los datos del disco temporal pueden perderse durante un [evento de mantenimiento](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) o cuando [vuelva a implementar una máquina virtual](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante un reinicio estándar de la máquina virtual, los datos de la unidad temporal deben conservarse.

En las máquinas virtuales de Linux, el disco es normalmente **/dev/sdb** y lo formatea y lo monta en **/mnt** el agente de Linux de Azure. El tamaño del disco temporal varía, según el tamaño de la máquina virtual. Para más información, consulte [Tamaños de las máquinas virtuales Linux en Azure](../windows/sizes.md).

Para más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de datos
Un disco de datos es un disco duro virtual (VHD) que se adjunta a una máquina virtual para almacenar datos de aplicaciones u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted. Cada disco de datos tiene una capacidad máxima de 4095 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

> [!NOTE]
> Para ver más detalles acerca de las capacidades de las máquinas virtuales, consulte [Tamaños de las máquinas virtuales Linux en Azure](../windows/sizes.md).
> 

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento **conectando** el disco a la máquina virtual. Puede usar un VHD cargado o copiado por usted en la cuenta de almacenamiento o uno que Azure cree para usted. Al adjuntar un disco de datos, el archivo de VHD se asocia a la máquina virtual, y se coloca una "concesión" en dicho VHD para que no pueda eliminarse del almacenamiento mientras esté adjunto.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Solución de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Conecte un disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para agregar almacenamiento adicional para la máquina virtual.
* [Configure el RAID de software](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para la redundancia.
* [Capture una máquina virtual Linux](./classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para poder implementar rápidamente máquinas virtuales adicionales.

