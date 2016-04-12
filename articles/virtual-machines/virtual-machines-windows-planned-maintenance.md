<properties
	pageTitle="Mantenimiento planeado de máquinas virtuales Windows | Microsoft Azure"
	description="Entienda qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales Windows que se ejecutan en Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>

# Mantenimiento planeado de máquinas virtuales Windows en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Entienda qué es el mantenimiento planeado de Azure y cómo afecta a la disponibilidad de sus máquinas virtuales Windows. También puede leer sobre [mantenimiento planeado de máquinas virtuales Linux](virtual-machines-linux-planned-maintenance.md).

## Por qué Azure realiza mantenimiento planeado

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información acerca del mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]

<!---HONumber=AcomDC_0330_2016-->