<properties
	pageTitle="Creación de una máquina virtual Linux | Microsoft Azure"
	description="Obtenga información acerca de cómo crear una máquina virtual personalizada con el modelo de implementación clásica ejecutando el sistema operativo Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Creación de una máquina virtual personalizada que ejecuta Linux en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación clásica. También puede crear un recurso con el [modelo de implementación del Administrador de recursos](virtual-machines-linux-tutorial.md).

En este tema se describe cómo crear una máquina virtual *personalizada* con la CLI de Azure y el modelo de implementación clásica. Usaremos una imagen de Linux de una de las **IMÁGENES** disponibles en Azure. Los comandos de la CLI de Azure ofrecen, entre otras, las opciones de configuración siguientes:

- Conexión de la VM a una red virtual
- Adición de la VM a un servicio en la nube existente
- Incorporación de la máquina virtual a una cuenta de almacenamiento existente
- Incorporación de la máquina virtual a un conjunto de disponibilidad o una ubicación

> [AZURE.IMPORTANT]Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo después de crear la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>


- [Creación de una máquina virtual que ejecuta Linux](virtual-machines-linux-tutorial.md)


## Creación de una máquina virtual con Linux mediante el modelo de implementación clásica

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=Sept15_HO4-->