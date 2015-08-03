<properties
	pageTitle="Creación de una máquina virtual personalizada que ejecuta Linux en Azure"
	description="Aprenda a crear una máquina virtual personalizada que ejecuta Linux en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="dkshir"/>

#Creación de una máquina virtual personalizada que ejecuta Linux en Azure

Una máquina virtual *personalizada* no es más que una máquina virtual que se crea con la opción **Desde la galería** porque ofrece más opciones de configuración que la opción **Creación rápida**. Estas opciones incluyen:

- Conexión de la máquina virtual a una red virtual
- Instalación del agente de máquina virtual, por ejemplo para funciones antimalware
- Incorporación de la máquina virtual a un servicio de nube existente
- Incorporación de la máquina virtual a una cuenta de almacenamiento existente
- Incorporación de la máquina virtual a un conjunto de disponibilidad

> [AZURE.IMPORTANT]Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo después de crear la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=July15_HO4-->