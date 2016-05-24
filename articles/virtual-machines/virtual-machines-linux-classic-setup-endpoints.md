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
	ms.date="04/19/2016"
	ms.author="cynthn"/>

# Cómo configurar puntos de conexión de máquinas virtuales Linux clásicas en Azure

Todas las máquinas virtuales con Linux que se crean en Azure con el modelo de implementación clásico pueden comunicarse automáticamente a través de un canal de red privado con otras máquinas virtuales del mismo servicio en la nube o de la misma red virtual. Sin embargo, los equipos en Internet o en otras redes virtuales necesitan extremos para dirigir el tráfico de red entrante a una máquina virtual. Este artículo también está disponible para [máquinas virtuales Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.

Cuando se crea una máquina virtual de Linux en el Portal de Azure clásico, se suelen crear automáticamente puntos de conexión comunes como de Shell seguro (SSH). Puede configurar extremos adicionales al crear la máquina virtual o posteriormente, según sea necesario.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Pasos siguientes

* También se puede crear un punto de conexión mediante la [interfaz de línea de comandos de Azure](../virtual-machines-command-line-tools.md). Ejecute el comando **azure vm endpoint create**.

* Si ha creado una máquina virtual en el modelo de implementación de Resource Manager, también puede usar la CLI de Azure en modo de Resource Manager para [crear grupos de seguridad de la red](../virtual-network/virtual-networks-create-nsg-arm-cli.md) con el fin de controlar el tráfico en la máquina virtual.

<!---HONumber=AcomDC_0511_2016-->