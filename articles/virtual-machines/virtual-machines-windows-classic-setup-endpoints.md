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
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Configuración de puntos de conexión en una máquina virtual de Windows clásica en Azure



Todas las máquinas virtuales de Windows que se crean en Azure con el modelo de implementación clásico se pueden comunicar automáticamente a través de un canal de red privado con otras máquinas virtuales del mismo servicio en la nube o la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual. Si desea obtener información acerca de los puntos de conexión en máquinas virtuales de Linux, consulte [Configuración de puntos de conexión en una máquina virtual de Windows clásica en Azure](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

Al crear una máquina virtual de Windows en el Portal de Azure clásico, los puntos de conexión comunes, como los de Escritorio remoto o Windows PowerShell Remoting se suelen crear automáticamente, según el sistema operativo que elija. Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Pasos siguientes

* Para usar un cmdlet de Azure PowerShell para configurar un punto de conexión de máquina virtual, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Para usar un cmdlet de Azure PowerShell para gestionar una ACL en un punto de conexión, consulte [Administración de listas de control de acceso (ACL) para puntos de conexión mediante PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si ha creado una máquina virtual en el modelo de implementación de Resource Manager, también puede usar Azure PowerShell para [crear un equilibrador de carga con conexión a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

<!---HONumber=AcomDC_0330_2016-->