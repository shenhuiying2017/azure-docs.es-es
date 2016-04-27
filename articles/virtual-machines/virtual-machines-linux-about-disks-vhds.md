<properties
	pageTitle="Acerca de los discos y los VHD para máquinas virtuales Linux | Microsoft Azure"
	description="Conozca los aspectos básicos de los discos y los VHD para las máquinas virtuales Linux en Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure

Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen, como mínimo, dos discos: un disco del sistema operativo Linux (en el caso de máquinas virtuales Linux) y un disco temporal. El disco del sistema operativo se crea a partir de una imagen y el disco del sistema operativo y la imagen son discos duros en realidad virtuales (VHD) almacenados en una cuenta de almacenamiento de Azure. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. Este artículo también está disponible para [máquinas virtuales Windows](virtual-machines-windows-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Pasos siguientes

-  [Conexión de un disco](virtual-machines-linux-attach-disk-portal.md) para agregar almacenamiento adicional para la máquina virtual.
-  [Configuración del software RAID](virtual-machines-linux-configure-raid.md) para la redundancia.
-  [Captura de una máquina virtual en Linux](virtual-machines-linux-classic-capture-image.md) para poder implementar rápidamente máquinas virtuales adicionales.

<!---HONumber=AcomDC_0413_2016-->