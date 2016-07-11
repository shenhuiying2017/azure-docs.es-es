<properties
	pageTitle="Conexión de máquinas virtuales Linux en un servicio en la nube | Microsoft Azure"
	description="Conexión de máquinas virtuales Linux creadas con el modelo de implementación clásico con una red virtual o un servicio en la nube de Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2016"
	ms.author="cynthn"/>

# Conexión de máquinas virtuales Linux creadas con el modelo de implementación clásico con una red virtual o un servicio en la nube

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Las máquinas virtuales Linux creadas con el modelo de implementación clásico se colocan siempre en un servicio en la nube. El servicio en la nube actúa como contenedor y proporciona un nombre DNS público único, una dirección IP pública y un conjunto de extremos para acceder a la máquina virtual a través de Internet. El servicio en la nube puede estar en una red virtual, pero esto no es un requisito. También puede [conectar máquinas virtuales Windows con una red virtual o un servicio en la nube](virtual-machines-windows-classic-connect-vms.md).

Si no es un servicio en la nube en una red virtual, se denomina un servicio en la nube *independiente*. Las máquinas virtuales de un servicio en la nube independiente solo pueden comunicarse con otras máquinas virtuales mediante los nombres DNS públicos de dichas máquinas, y el tráfico viaja a través de Internet. Si un servicio en la nube está en una red virtual, las máquinas virtuales de ese servicio en la nube pueden comunicarse con todas las otras máquinas virtuales de la red virtual sin enviar tráfico a través de Internet.

Si coloca las máquinas virtuales en el mismo servicio en la nube independiente, aún puede usar los equilibrios de cargas y los conjuntos de disponibilidad. Para obtener más información, consulte [Máquinas virtuales de equilibrio de carga](virtual-machines-linux-load-balance.md) y [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-linux-manage-availability.md). Sin embargo, no se pueden organizar las máquinas virtuales en subredes ni conectar un servicio en la nube independiente a la red local. Este es un ejemplo:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## Pasos siguientes

Después de haber creado una máquina virtual, es conveniente [agregar un disco de datos](virtual-machines-linux-classic-attach-disk.md) para que los servicios y las cargas de trabajo tengan una ubicación donde almacenar los datos.

<!---HONumber=AcomDC_0629_2016-->