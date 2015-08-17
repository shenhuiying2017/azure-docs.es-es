<properties
	pageTitle="Creación de una máquina virtual personalizada que ejecuta Linux en Azure"
	description="Aprenda a crear una máquina virtual personalizada que ejecuta Linux en Azure."
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

En este tema se describe cómo crear una máquina virtual *personalizada* con la CLI de Azure para Mac, Linux y Windows en modo Administración de servicios de Azure. Usaremos una imagen de Linux de una de las **IMÁGENES** disponibles en Azure. Los comandos de la CLI de Azure ofrecen, entre otras, las opciones de configuración siguientes:

- Conexión de la VM a una red virtual
- Adición de la VM a un servicio en la nube existente
- Incorporación de la máquina virtual a una cuenta de almacenamiento existente
- Incorporación de la máquina virtual a un conjunto de disponibilidad o una ubicación

> [AZURE.IMPORTANT]Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo después de crear la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Creación de una máquina virtual que ejecuta Linux](virtual-machines-linux-tutorial.md)


## Cómo crear una máquina virtual de Linux con Administración de servicios de Azure

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=August15_HO6-->