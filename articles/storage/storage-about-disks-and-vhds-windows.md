---
title: "Acerca de los discos y los VHD para máquinas virtuales de Windows en Microsoft Azure | Microsoft Docs"
description: "Conozca los aspectos básicos de los discos y los VHD para las máquinas virtuales Windows en Azure."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 0142c64d-5e8c-4d62-aa6f-06d6261f485a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: e34a2bfbf5f1ae544a729c994d91c485d48bb440
ms.openlocfilehash: 1027f9c73a45b7d148643f5ec217683f71c4e899
ms.lasthandoff: 03/01/2017


---
# <a name="about-disks-and-vhds-for-azure-windows-vms"></a>Acerca de los discos y los discos duros virtuales para máquinas virtuales de Windows en Azure
Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco de sistema operativo Windows y un disco temporal. El disco de sistema operativo se crea a partir de una imagen, y tanto el disco de sistema operativo como la imagen son discos duros virtuales (VHD) almacenados en una cuenta de Azure Storage. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. 

En este artículo, se tratan los diferentes usos de los discos y después se describen los diferentes tipos de discos que puede crear y utilizar. Este artículo también está disponible para [máquinas virtuales Linux](storage-about-disks-and-vhds-linux.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por las máquinas virtuales

Observemos cómo las máquinas virtuales utilizan los discos.

### <a name="operating-system-disk"></a>Disco del sistema operativo
Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado de forma predeterminada como la unidad C:. Este disco tiene una capacidad máxima de 1023 gigabytes (GB). 

### <a name="temporary-disk"></a>Disco temporal
Cada máquina contiene un disco temporal. El disco temporal proporciona almacenamiento a corto plazo para aplicaciones y procesos, y está destinado únicamente a almacenar datos como archivos de paginación o de intercambio. Los datos del disco temporal pueden perderse durante un [evento de mantenimiento](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-planned-vs-unplanned-maintenance) o cuando [vuelva a implementar una máquina virtual](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Durante un reinicio estándar de la máquina virtual, los datos de la unidad temporal deben conservarse.

El disco temporal está etiquetado de forma predeterminada como unidad D: y se usa para almacenar el archivo pagefile.sys. Para reasignar este disco a una letra de unidad diferente, consulte [Cambiar la letra de unidad de disco temporal de Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md). El tamaño del disco temporal varía, según el tamaño de la máquina virtual. Para más información, consulte [Tamaños de las máquinas virtuales Windows](../virtual-machines/virtual-machines-windows-sizes.md).

Para más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)


### <a name="data-disk"></a>Disco de datos
Un disco de datos es un disco duro virtual que se adjunta a una máquina virtual para almacenar los datos de la aplicación u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted. Cada disco de datos tiene una capacidad máxima de 1023 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

> [!NOTE]
> Para más información acerca de las capacidades de las máquinas virtuales, consulte [Tamaños de las máquinas virtuales Windows](../virtual-machines/virtual-machines-windows-sizes.md).
> 

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento **conectando** el disco a la máquina virtual. Puede usar un disco duro virtual cargado o copiado por usted en su cuenta de almacenamiento o uno creado por Azure para usted. Al adjuntar un disco de datos, el archivo VHD se asocia a la máquina virtual, y se coloca una "concesión" en el disco duro virtual para que no pueda eliminarse del almacenamiento mientras esté adjunto.


[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="one-last-recommendation-use-trim-with-unmanaged-standard-disks"></a>Una última recomendación: utilice TRIM con los discos estándar no administrados 

Si utiliza discos estándar no administrados (HDD), debería habilitar TRIM. TRIM descarta los bloques sin utilizar del disco, por lo que solo se le facturará el almacenamiento que utiliza realmente. Esto puede suponerle un ahorro si crea archivos grandes y, luego, los elimina. 

Puede ejecutar este comando para comprobar la configuración de TRIM. Abra un símbolo del sistema en su máquina virtual de Windows y escriba:

```
fsutil behavior query DisableDeleteNotify
```

Si el comando devuelve 0, significa que TRIM está habilitada correctamente. Si devuelve 1, ejecute el siguiente comando para habilitar TRIM:

```
fsutil behavior set DisableDeleteNotify 0
```

<!-- Might want to match next-steps from overview of managed disks -->
## <a name="next-steps"></a>Pasos siguientes
* [Conecte un disco](../virtual-machines/virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para agregar almacenamiento adicional para la máquina virtual.
* [Carga de una imagen de máquina virtual Windows en Azure](../virtual-machines/virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para usarla al crear una nueva máquina virtual.
* [Cambiar la letra de unidad de disco temporal de Windows](../virtual-machines/virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) con el fin de que la aplicación pueda usar la unidad D: para los datos.


