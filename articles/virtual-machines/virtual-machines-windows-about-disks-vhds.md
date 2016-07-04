<properties
	pageTitle="Acerca de los discos y los VHD de máquinas virtuales Windows | Microsoft Azure"
	description="Conozca los aspectos básicos de los discos y los VHD para las máquinas virtuales Windows en Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="cynthn"/>

# Acerca de los discos y los discos duros virtuales para máquinas virtuales de Azure

Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen, como mínimo, dos discos: un disco del sistema operativo Windows (en el caso de máquinas virtuales Windows) y un disco temporal. El disco del sistema operativo se crea a partir de una imagen y el disco del sistema operativo y la imagen son discos duros en realidad virtuales (VHD) almacenados en una cuenta de almacenamiento de Azure. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales. Este artículo también está disponible para [máquinas virtuales Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Pasos siguientes
-  [Capturar una máquina virtual Windows](virtual-machines-windows-capture-image.md) para poder escalar horizontalmente la implementación de máquinas virtuales
-  [Cargar una imagen de máquina virtual de Windows Azure](virtual-machines-windows-upload-image.md) para usarla al crear una nueva máquina virtual
-  [Cambiar la letra de unidad de disco temporal de Windows](virtual-machines-windows-classic-change-drive-letter.md) con el fin de que la aplicación pueda usar la unidad D: para los datos

<!---HONumber=AcomDC_0622_2016-->