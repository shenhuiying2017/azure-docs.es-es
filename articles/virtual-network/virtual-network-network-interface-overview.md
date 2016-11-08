---
title: Interfaces de red | Microsoft Docs
description: Obtenga información sobre las interfaces de red en Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial

---
# <a name="network-interfaces"></a>Interfaces de red
Una interfaz de red (NIC) es la interconexión entre una máquina virtual (VM) y la red de software subyacente. Este artículo explica qué es una interfaz de red y cómo se utiliza en el modelo de implementación de Azure Resource Manager.

Microsoft recomienda implementar nuevos recursos utilizando el modelo de implementación de Resource Manager, pero también puede implementar máquinas virtuales con conectividad de red en el modelo de implementación [clásico](virtual-network-ip-addresses-overview-classic.md) . Si está familiarizado con el modelo clásico, hay diferencias importantes en las cuestiones relacionadas con las redes de máquinas virtuales en el modelo de implementación de Resource Manager. Infórmese sobre las diferencias leyendo el artículo sobre [Redes de máquinas virtuales (modelo clásico)](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

En Azure, una interfaz de red:

1. Es un recurso que se puede crear o eliminar, y que tiene sus propios valores configurables.
2. Tiene que estar conectada a una subred de una instancia de Azure Virtual Network (VNet) cuando se crea. Si no está familiarizado con las redes virtuales, puede informarse leyendo el artículo [Información general sobre redes virtuales](virtual-networks-overview.md) . La interfaz de red tiene que estar conectada a una red virtual que exista en la misma [ubicación](https://azure.microsoft.com/regions) y [suscripción](../azure-glossary-cloud-terminology.md#subscription) de Azure en la que se encuentre la NIC. Una vez creada la NIC, puede cambiar la subred a la que está conectada, pero no la red virtual.
3. Tiene un nombre asignado que no se puede cambiar una vez creada la NIC. El nombre tiene que ser único dentro de un [grupo de recursos](../resource-group-overview.md#resource-groups)de Azure, pero no es necesario que sea único dentro de la suscripción, de la ubicación de Azure en la que se crea o de la red virtual a la que está conectada. Normalmente se crean varias NIC dentro de una suscripción de Azure. Se recomienda que diseñe una convención de nomenclatura que haga más fácil la administración de varias interfaces de red que el uso de nombres predeterminados. Consulte [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md) (Convenciones de nomenclatura recomendadas para los recursos de Azure) para obtener sugerencias.
4. Se puede conectar a una máquina virtual, pero a una sola máquina virtual que se encuentre en la misma ubicación que la NIC.
5. Tiene una dirección MAC, que se mantiene con la interfaz de red mientras esta permanezca conectada a una máquina virtual. La dirección MAC se mantiene tanto si se reinicia la máquina virtual (desde dentro del sistema operativo), como si se detiene (desasigna) y se inicia usando Azure Portal, Azure PowerShell o la interfaz de la línea de comandos de Azure. Si se desconecta de una máquina virtual y se conecta a otra máquina virtual diferente, la interfaz de red recibe una dirección MAC diferente. Si se elimina la interfaz de red, la dirección MAC se asigna a otras interfaces de red.
6. Tiene que tener una dirección IP dinámica o estática **privada** *IPv4* principal asignada.
7. Puede tener un recurso de dirección IP pública asociado.
8. Admite Accelerated Networking con virtualización de E/S de raíz única (SR-IOV) para tamaños específicos de máquina virtual que ejecuten versiones específicas del sistema operativo Microsoft Windows Server. Para más información acerca de esta característica de versión preliminar, lea el artículo [Accelerated Networking para una máquina virtual](virtual-network-accelerated-networking-powershell.md) .
9. Puede recibir tráfico no destinado a direcciones IP privadas que tenga asignadas, si está habilitado el reenvío IP para la NIC. Si una máquina virtual está ejecutando software de firewall por ejemplo, enruta paquetes no destinados a sus propias direcciones IP. La máquina virtual tiene que ejecutar software capaz de enrutar o reenviar el tráfico, pero para ello es necesario que esté habilitado el reenvío IP para una NIC.
10. A menudo se crea en el mismo grupo de recursos que la máquina virtual a la que está conectada o la misma red virtual a la que está conectada, aunque no es necesario que sea así.

Se pueden conectar varias interfaces de red a la misma máquina virtual, siempre y cuando el tamaño de la misma lo permita. Para más información acerca de los tamaños de máquina virtual que admiten varias interfaces de red, consulte los artículos sobre [Tamaños de máquinas virtuales de Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) o [Tamaños de la máquinas virtuales Linux](../virtual-machines/virtual-machines-linux-sizes.md).

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a crear una máquina virtual con una sola NIC leyendo el artículo sobre [creación de una máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Aprenda a crear una máquina virtual con varias interfaces de red leyendo el artículo sobre [implementación de una máquina virtual con varias NIC](virtual-network-deploy-multinic-arm-ps.md) .
* Para obtener información acerca de cómo crear una NIC con varias configuraciones de IP consulte el artículo sobre [asignación de varias direcciones IP para máquinas virtuales de Azure](virtual-network-multiple-ip-addresses-powershell.md) .

<!--HONumber=Oct16_HO2-->


