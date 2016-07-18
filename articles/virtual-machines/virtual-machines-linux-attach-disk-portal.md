<properties
	pageTitle="Incorporación de un disco de datos a una máquina virtual Linux | Microsoft Azure"
	description="Cómo adjuntar un disco de datos nuevo o existente a una máquina virtual Linux en el Portal de Azure con el modelo de implementación de Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# Incorporación de un disco de datos a una máquina virtual Linux en el Portal de Azure

En este artículo se muestra cómo adjuntar discos nuevos y existentes a una máquina virtual Linux a través del Portal de Azure. También puede [adjuntar un disco de datos a una máquina virtual Windows en el Portal de Azure](virtual-machines-windows-attach-disk-portal.md). Antes de hacerlo, revise estas sugerencias:

- El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).
- Para usar Almacenamiento premium, necesitará una máquina virtual de serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md)
- Los discos conectados a las máquinas virtuales son en realidad archivos .vhd en una cuenta de almacenamiento de Azure. Para obtener más información, vea [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).
- Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.
- Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar uno que ya esté disponible allí, si no está conectado a otra máquina virtual, o cargar su propio archivo .vhd en la cuenta de almacenamiento.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## Pasos siguientes

Después de agregar el disco, debe prepararlo para poder usarlo. En el sistema operativo de la máquina virtual siga los pasos que se indican en la sección "Inicialización de un nuevo disco de datos en Linux" en este [artículo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux) .

<!---HONumber=AcomDC_0706_2016-->