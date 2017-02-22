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
ms.date: 02/14/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 794f87c3d4b8378d7aeca63791a8fbfd03e44ceb
ms.openlocfilehash: 50802013de0bd6f4bf1396df00cdc72bc378d503


---
# <a name="about-disks-and-vhds-for-azure-linux-vms"></a>Acerca de los discos y los discos duros virtuales para máquinas virtuales de Linux en Azure
Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco del sistema operativo Linux y un disco temporal. El disco del sistema operativo se crea a partir de una imagen y el disco del sistema operativo y la imagen son discos duros en realidad virtuales (VHD) almacenados en una cuenta de almacenamiento de Azure. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. 

En este artículo, se tratan los diferentes usos de los discos y después se describen los diferentes tipos de discos que puede crear y utilizar. Este artículo también está disponible para [máquinas virtuales Windows](storage-about-disks-and-vhds-windows.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por las máquinas virtuales

Observemos cómo las máquinas virtuales utilizan los discos.

## <a name="operating-system-disk"></a>Disco del sistema operativo
Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado de forma predeterminada como /dev/sda. Este disco tiene una capacidad máxima de 1023 gigabytes (GB). 

## <a name="temporary-disk"></a>Disco temporal
El disco temporal se crea automáticamente. En las máquinas virtuales de Linux, el disco es normalmente /dev/sdb y es formateado y montado en /mnt/resource por el agente de Linux de Azure.

El tamaño del disco temporal varía, según el tamaño de la máquina virtual. Para más información, consulte [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-linux-sizes.md).

> [!WARNING]
> No almacene datos en el disco temporal. Proporciona almacenamiento temporal para aplicaciones y procesos y está destinado únicamente a almacenar datos como archivos de páginas o de intercambio. 
> 

Para más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de datos
Un disco de datos es un disco duro virtual que se adjunta a una máquina virtual para almacenar los datos de la aplicación u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted. Cada disco de datos tiene una capacidad máxima de 1023 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

> [!NOTE]
> Para ver más detalles acerca de las capacidades de las máquinas virtuales, consulte [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/virtual-machines-linux-sizes.md).
> 

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento **conectando** el disco a la máquina virtual. Puede usar un disco duro virtual cargado o copiado por usted en su cuenta de almacenamiento o uno creado por Azure para usted. Al conectar un disco de datos, el archivo VHD se asocia a la máquina virtual, y se coloca una "concesión" en el disco duro virtual para que no pueda eliminarse del almacenamiento mientras esté conectado.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>Solución de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Conecte un disco](../virtual-machines/virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para agregar almacenamiento adicional para la máquina virtual.
* [Configure el RAID de software](../virtual-machines/virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para la redundancia.
* [Capture una máquina virtual Linux](../virtual-machines/virtual-machines-linux-classic-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) para poder implementar rápidamente máquinas virtuales adicionales.




<!--HONumber=Feb17_HO2-->


