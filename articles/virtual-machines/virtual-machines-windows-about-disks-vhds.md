---
title: Acerca de los discos y los VHD de máquinas virtuales Windows | Microsoft Docs
description: Conozca los aspectos básicos de los discos y los VHD para las máquinas virtuales Windows en Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure
Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco de sistema operativo Windows y un disco temporal. El disco de sistema operativo se crea a partir de una imagen, y tanto el disco de sistema operativo como la imagen son discos duros virtuales (VHD) almacenados en una cuenta de Azure Storage. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. Este artículo también está disponible para [máquinas virtuales Linux](virtual-machines-linux-about-disks-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="operating-system-disk"></a>Disco del sistema operativo
Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado de forma predeterminada como la unidad C:. Este disco tiene una capacidad máxima de 1023 gigabytes (GB). 

## <a name="temporary-disk"></a>Disco temporal
El disco temporal se crea automáticamente. El disco temporal está etiquetado de forma predeterminada como unidad D: y se usa para almacenar el archivo pagefile.sys. 

El tamaño del disco temporal varía, según el tamaño de la máquina virtual. Para más información, consulte [Tamaños de las máquinas virtuales Windows](virtual-machines-windows-sizes.md).

> [!WARNING]
> No almacene datos en el disco temporal. Proporciona almacenamiento temporal para aplicaciones y procesos y está destinado únicamente a almacenar datos como archivos de páginas o de intercambio. Para reasignar este disco a una letra de unidad diferente, consulte [Cambiar la letra de unidad de disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md).
> 
> 

Para más información sobre cómo usa Azure el disco temporal, consulte [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco de datos
Un disco de datos es un disco duro virtual que se adjunta a una máquina virtual para almacenar los datos de la aplicación u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted.  Cada disco de datos tiene una capacidad máxima de 1023 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

> [!NOTE]
> Para más información acerca de las capacidades de las máquinas virtuales, consulte [Tamaños de las máquinas virtuales Windows](virtual-machines-windows-sizes.md).
> 
> 

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento **conectando** el disco a la máquina virtual. Puede usar un disco duro virtual cargado o copiado por usted en su cuenta de almacenamiento o uno creado por Azure para usted. Al adjuntar un disco de datos, el archivo VHD se asocia a la máquina virtual, y se coloca una "concesión" en el disco duro virtual para que no pueda eliminarse del almacenamiento mientras esté adjunto.

## <a name="about-vhds"></a>Acerca de los discos duros virtuales
Los discos duros virtuales usados en Azure son archivos .vhd almacenados como blobs en páginas en una cuenta de almacenamiento estándar o premium de Azure. Para más información sobre los blobs en páginas, consulte [Introducción a los blobs en bloques y a los blobs en páginas](https://msdn.microsoft.com/library/ee691964.aspx). Para más información sobre Premium Storage, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md).

Azure admite el formato VHD de disco fijo. El formato fijo coloca el disco lógico linealmente dentro del archivo, de manera que el desplazamiento de disco X se almacena en el desplazamiento de blob X. Un pequeño pie de página al final del blob describe las propiedades del VHD. El formato fijo suele desaprovechar el espacio porque la mayoría de discos contienen grandes intervalos sin usar. Sin embargo, Azure almacena los archivos .vhd en un formato disperso; así pues, se beneficia de las ventajas de los discos fijos y dinámicos al mismo tiempo. Para más información, consulte [Introducción a discos duros virtuales](https://technet.microsoft.com/library/dd979539.aspx).

Todos los archivos .vhd de Azure que desea usar como origen para crear discos o imágenes son de solo lectura. Cuando se crea un disco o una imagen, Azure hace copias de los archivos .vhd. Estas copias pueden ser de solo lectura o de lectura y escritura, en función de cómo use el disco duro virtual.

Cuando se crea una máquina virtual desde una imagen, Azure crea un disco para la máquina virtual que es una copia del archivo .vhd de origen. Para proteger contra la eliminación accidental, Azure coloca una concesión en cada archivo .vhd de origen que se usa para crear una imagen, un disco del sistema operativo o un disco de datos.

Para poder eliminar un archivo .vhd de origen, deberá quitar la concesión eliminando el disco o la imagen. Para eliminar la máquina virtual, el disco de sistema operativo y el archivo .vhd de origen a la vez, elimine la máquina virtual y todos los discos asociados. Sin embargo, para eliminar un archivo .vhd que es el origen de un disco de datos es necesario realizar varios pasos en un orden determinado. Primero, desasocie el disco de la máquina virtual y, a continuación, elimine el disco y el archivo .vhd.

> [!WARNING]
> Si elimina un archivo .vhd de origen del almacenamiento o elimina la cuenta de almacenamiento, Microsoft no puede recuperar esos datos por usted.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Conecte un disco](virtual-machines-windows-attach-disk-portal.md) para agregar almacenamiento adicional para la máquina virtual.
* [Carga de una imagen de máquina virtual Windows en Azure](virtual-machines-windows-upload-image.md) para usarla al crear una nueva máquina virtual.
* [Cambiar la letra de unidad de disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md) con el fin de que la aplicación pueda usar la unidad D: para los datos.

<!--HONumber=Oct16_HO2-->


