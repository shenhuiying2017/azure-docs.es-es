<properties
	pageTitle="Creación de una máquina virtual personalizada en Azure"
	description="Aprenda a crear una máquina virtual personalizada en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#Creación de una máquina virtual personalizada

Una máquina virtual *personalizada* no es más que una máquina virtual que se crea con la opción **Desde la galería** porque ofrece más opciones de configuración que la opción **Creación rápida**. Estas opciones incluyen:

- Conexión de la máquina virtual a una red virtual
- Instalación del agente de máquina virtual, por ejemplo para funciones antimalware
- Incorporación de la máquina virtual a un servicio de nube existente
- Incorporación de la máquina virtual a una cuenta de almacenamiento existente
- Adición de la VM a un conjunto de disponibilidad

**Importante**: si desea que su máquina virtual use una red virtual de forma que pueda conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual cuando cree la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo cuando se cree la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]
 

<!---HONumber=July15_HO4-->