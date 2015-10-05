<properties
	pageTitle="Creación de una máquina virtual personalizada que ejecute Windows | Microsoft Azure"
	description="Aprenda a crear máquinas virtuales personalizadas que ejecuten Windows en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Crear una máquina virtual personalizada que ejecute Windows en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de un recurso con el modelo de implementación clásica.

Una máquina virtual *personalizada* no es más que una máquina virtual que se crea con la opción **Desde la galería** porque ofrece más opciones de configuración que la opción **Creación rápida**. Estas opciones incluyen:

- Conectar la máquina virtual a una red virtual.
- Instalación del agente de máquina virtual, por ejemplo para funciones antimalware.
- Adición de la máquina virtual a un servicio en la nube existente.
- Adición de la máquina virtual a una cuenta de almacenamiento existente.
- Agregar la máquina virtual a un conjunto de disponibilidad.

> [AZURE.IMPORTANT]Si desea que la máquina virtual use una red virtual, con el fin de poder conectarse a ella directamente mediante un nombre de host o configurar conexiones entre locales, asegúrese de que especifica la red virtual al crear la máquina virtual. Puede configurarse una máquina virtual para que se una a una red virtual solo después de crear la máquina virtual. Para obtener detalles acerca de las redes virtuales, consulte [Información general sobre redes virtuales de Azure](virtual-networks-overview.md).

##Para crear la máquina virtual

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Sept15_HO4-->