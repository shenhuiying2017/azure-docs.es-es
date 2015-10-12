<properties
	pageTitle="Creación de una máquina virtual personalizada | Microsoft Azure"
	description="Obtenga información acerca de cómo crear una máquina virtual personalizada desde el portal de Azure usando el modelo de implementación clásica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Creación de una máquina virtual personalizada


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación clásica.


Una máquina virtual *personalizada* no es más que una máquina virtual que se crea con la opción **Desde la galería** porque ofrece más opciones de configuración que la opción **Creación rápida**. Estas opciones incluyen:

- Conectar la máquina virtual a una red virtual.
- Instalar el agente de máquina virtual de Azure y las extensiones de máquina virtual de Azure, como para antimalware.
- Agregar la máquina virtual a servicios en la nube existentes.
- Agregar la máquina virtual a una cuenta de almacenamiento existente.
- Agregar la máquina virtual a un conjunto de disponibilidad.

> [AZURE.IMPORTANT]Si desea que su máquina virtual use una red virtual de forma que pueda conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual cuando cree la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo después de crear la máquina virtual. Para obtener detalles sobre las redes virtuales, consulte [Información general sobre redes virtuales de Azure](virtual-networks-overview.md).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Oct15_HO1-->