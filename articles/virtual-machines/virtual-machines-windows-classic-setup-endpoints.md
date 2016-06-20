<properties
	pageTitle="Configuración de puntos de conexión en una máquina virtual de Windows clásica | Microsoft Azure"
	description="Aprenda a configurar puntos de conexión en el Portal de Azure clásico para permitir la comunicación con una máquina virtual Windows en Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Cómo configurar puntos de conexión de máquinas virtuales Linux clásicas en Azure


Todas las máquinas virtuales de Windows que se crean en Azure con el modelo de implementación clásico se pueden comunicar automáticamente a través de un canal de red privado con otras máquinas virtuales del mismo servicio en la nube o la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual. Este artículo también está disponible para [máquinas virtuales Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-nsg-quickstart-portal.md). En el modelo de implementación de **Resource Manager**, los puntos de conexión se configuran mediante **grupos de seguridad de red (NSG)**. Para más información, consulte [Allow external access to your VM using the Azure Portal](virtual-machines-windows-nsg-quickstart-portal.md) (Permitir el acceso externo a la máquina virtual mediante el Portal de Azure).

Al crear una máquina virtual Windows en el Portal de Azure clásico, los puntos de conexión comunes, como los de Escritorio remoto y Windows PowerShell Remoting se suelen crear automáticamente. Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Pasos siguientes

* Para usar un cmdlet de Azure PowerShell para configurar un punto de conexión de máquina virtual, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Para usar un cmdlet de Azure PowerShell para gestionar una ACL en un punto de conexión, consulte [Administración de listas de control de acceso (ACL) para puntos de conexión mediante PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si ha creado una máquina virtual en el modelo de implementación de Resource Manager, también puede usar Azure PowerShell para [crear grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-ps.md) con el fin de controlar el tráfico en la máquina virtual.

<!---HONumber=AcomDC_0608_2016-->