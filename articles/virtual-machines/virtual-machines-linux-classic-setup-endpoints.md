<properties
	pageTitle="Configuración de puntos de conexión en una máquina virtual con Linux clásica | Microsoft Azure"
	description="Aprenda a configurar puntos de conexión en el Portal de Azure clásico para permitir la comunicación con una máquina virtual con Linux en Azure."
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
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Configuración de puntos de conexión en una máquina virtual con Linux clásica en Azure

Todas las máquinas virtuales con Linux que se crean en Azure con el modelo de implementación clásico pueden comunicarse automáticamente a través de un canal de red privado con otras máquinas virtuales del mismo servicio en la nube o de la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual. Si desea información acerca de los puntos de conexión de máquinas virtuales con Windows, consulte [Configuración de puntos de conexión en una máquina virtual con Windows clásica en Azure](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

Cuando se crea una máquina virtual con Linux en el Portal de Azure clásico, se suelen crear automáticamente puntos de conexión comunes como de Shell seguro (SSH). Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Pasos siguientes

* También se puede crear un punto de conexión de máquina virtual desde la interfaz de línea de comandos de Azure en el [modo de Administración de servicios](../virtual-machines-command-line-tools.md). Ejecute el comando **azure vm endpoint create**.

<!---HONumber=AcomDC_0330_2016-->